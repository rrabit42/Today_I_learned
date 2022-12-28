# Context  
Go 1.7 버전부터 기본 라이브러리에 탑재  

> Package context defines the Context type, which carries deadlines, cancellation signals, and other request-scoped values across API boundaries and between processes.  

위는 공식 문서에 나와있는 표현으로, 이에 따르면 context는 API와 프로세스 경계에서 기한, 취소 신호, 요청 범위의 값들을 전달한다.  
우리가 어떤 API를 호출하거나, 새로운 goroutine을 생성하여 작업을 수행할 때 context를 통해서 호출이 완료되어야 하는 기한을 명시할 수도 있고, 요청했던 작업을 취소할 수도 있으며, 특정 값을 전달할 수도 있다.  

## 취소가 필요한 이유  
작업 취소 시, 불필요한 작업을 수행하지 않고 빠르게 취소할 수 있다면 애플리케이션 성능에 큰 도움이 됨.  

데이터베이스를 호출하여 쿼리된 데이터를 클라이언트에 반환하는 서버가 있다고 생각해보자.  
문제가 없다면 아래와 같이 동작할 것이다.  
![image](https://user-images.githubusercontent.com/46364778/201805924-74020629-703a-47d4-8303-b3c0c33d967c.png)  

Server가 요청을 받아 DB에 query를 전달하기 전에 클라이언트가 요청을 취소를 한다면 아래와 같이 동작할 것이다.  
![image](https://user-images.githubusercontent.com/46364778/201805977-d43c47e6-8b85-47c0-bf57-fa5abd8cf007.png)

클라이언트가 중간에 취소했음에도 불구하고 Server는 DB에 query를 전달하고 data를 받아오는 불필요한 작업을 수행하게 된다.  
위와 동일한 상황에서 context를 사용하여 취소를 한다면 아래 그림과 같이 서버는 불필요한 작업을 수행하지 않고 바로 취소할 수 있다.  
![image](https://user-images.githubusercontent.com/46364778/201806014-1d1f8604-52f7-437f-90a4-1b93c3597f81.png)  

이와 같이 context를 사용하면 요청에 대한 모든 작업들을 빠르게 취소할 수 있게 된다.  

+) 고루틴을 사용할 때, 고루틴이 일정 시간 안에 반드시 종료될 것이라는 보장이 필요. 즉, 언제 종료될지 모른 채로 고루틴 실행시키면 안됨  
-> context의 cancelation 기능 사용하면 고루틴의 생명주기 쉽게 제어 가능  

## Context Interface  
Context는 인터페이스이고, 다음과 같은 네 가지 메서드로 구성되어 있다.  
```
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```  

각 메서드는 다음과 같은 기능을 한다.  
|메서드|기능|  
|:---:|:---:|  
|Deadline()|해당 컨텍스트에서 수행되는 작업이 취소되어야 하는 시각(time.Time)을 반환한다. 기한이 없는 경우에는 ok==false를 반환한다.|  
|Done()|해당 컨텍스트가 취소될 때 닫히는 채널을 반환한다. 취소되지 않는 컨텍스트의 경우에는 nil이 반환된다.|  
|Err()|Done() 채널이 아직 닫히지 않았으면, nil을 반환하고, 닫히지 않았다면 왜 닫혔는지를 설명하는 error가 반환된다. 에러 메시지는 "Canceled if the context was canceled" 또는 "DeadlineExceeded if the context's deadline passed." 둘 중에 하나이다.|  
|Value()|해당 컨텍스트에서 키와 연관된 값을 반환한다. 연관된 값이 없는 경우 nil을 반환한다.|  

## Context 구현  
context 패키지는 Context 인터페이스 뿐 아니라, 다음과 같은 Context 구현 메서드들을 제공한다.  

### context.Background()  
빈 Context를 반환한다.  
이 함수로 생성된 Context 객체는 기한도, 값도 없고 취소되지도 않는다.  
이 메서드는 탑레벨의 Context를 생성하는데 사용된다.  
뒤에 가서 보게 되겠지만 context.TODO()를 제외한 다른 Context 생성 함수들은 부모 Context에 파생되어 생성된다.  
context.Background()는 부모가 없는, 즉 루트 Context를 생성하기 때문에 탑레벨 Context라고 하는 것이다.  
```
Background() --- WithValue() --- WithCancel
              |
              `- WithTimeout()
```  

### context.TODO()  
context.Background()와 동일하게 기한, 값이 없고 취소되지 않는 빈 Context를 반환한다.  
소스 코드를 들어가서 보면 실제로 context.Background()와 동일하게 emptyContext 객체를 반환하는 것을 볼 수 있다.  
따라서 context.Background()를 써야할 곳에 context.TODO()를 써도 동일하게 동작할 것이다.  
하지만 이름에서 알 수 있듯 이 함수는 Context를 쓰긴 해야 하지만 어떻게 써야할지 확실치 않은 곳에 써야한다.  

### context.WithValue(Context, key, val interface{}) (ctx Context)  
한번 생성된 컨텍스트는 변경할 수 X -> 컨텍스트에 값을 추가하고 싶을 때는 context.WithValue 함수로 새로운 컨텍스트를 만들어줘야 함  

부모 Context에서 파생되어, {key, val} 쌍을 저장하는 자식 Context를 반환한다.  
저장된 값은 Value(key interface{}) 메서드로 읽어올 수 있다.  
```
ctx := context.WithValue(context.Background(), "foo", "bar")
fmt.Println(ctx.Value("foo")) // "bar"
```  
쓰임새는 맵과 비슷하지만, 값을 읽어오는 과정은 맵과는 다르다.  
Value(key interface{}) 메서드는 현재 Context에 찾는 key에 해당하는 값이 없으면 부모 Context를 하나씩 거슬러 올라가면서 값을 찾는다.  
끝내 값을 찾지 못하면 nil이 반환되고, 값을 찾으면 해당 값이 반환된다.  
```
// ctx1{1:a} --- ctx2{2:b}
//            |
//            `- ctx3{3:c}
ctx1 := context.WithValue(context.Background(), 1, "a")
ctx2 := context.WithValue(ctx1, 2, "b")
ctx3 := context.WithValue(ctx1, 3, "c")
fmt.Println(ctx2.Value(1)) // "a"
fmt.Println(ctx2.Value(2)) // "b"
fmt.Println(ctx3.Value(1)) // "a"
fmt.Println(ctx3.Value(2)) // <nil>
fmt.Println(ctx3.Value(3)) // "c"
```  
위 예제에서 ctx1은 ctx2, ctx3의 부모이기 때문에 ctx2, ctx3 모두 1에 연관된 값 "a"를 가져올 수 있다.  
하지만 ctx3에서부터 거슬러 올라갈 때는 2 키에 해당하는 값이 없기 때문에 nil이 반환된다.  

#### 값을 꺼낼 때 주의할 점  
컨텍스트의 Value 메서드의 리턴값은 interface{} 타입  
컨텍스트에 값이 존재하지 않는 경우 nil 리턴  
-> 컨텍스트에 해당 값이 존재하는지(v != nil) and 그 값이 원하는 타입이 맞는지 type assertion을 통해 확인(u, ok := v.(User)) 필요  

### context.WithCancel(parent Context) (ctx Context, cancel CancelFunc)  
위 함수로 생성한 컨텍스트에는 취소 신호를 보낼 수 있다.  
일정 시간이 되면 자동으로 컨텍스트에 취소 신호가 전달되도록 하려면 ```context.WithDeadline``` 함수나 ```context.WithTimeout``` 함수를 사용하여 컨텍스트 생성하면 됨  

* ctx: 새로 생성된 컨텍스트  
* cancel: 컨텍스트에 종료 신호를 보낼 수 이쓴ㄴ 함수  

여러 고루틴이 컨텍스트를 공유하도록 하면 하나의 컨텍스트로 여러 고루틴의 생명주기를 한꺼번에 제어 가능  
cancle 함수로 컨텍스트(ctx)에 취소 신호를 보내면 ctx를 사용하는 모든 고루틴에 동일하게 취소 신호가 전달됨.(여러 고루틴에 일일이 취소 신호를 전달하지 않아도 됨)  

### func WithDeadline(parent Context, d time.Time) (Context, CancelFunc)  
두 번째 파라미터로 받는 time의 시간이 되면 컨텍스트에 취소 신호가 전달됨  

### func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)  
두 번째 파라미터로 전달한 duration 시간이 지나면 컨텍스트에 취소 신호가 전달 됨  
context의 Deadline 메서드를 사용하면 컨텍스트로 취소 신호가 전달될 때까지 남은 시간 확인 가능  
-> 작업 시작 전, 남은 시간을 먼저 확인해서 충분한 시간이 있을 때만 작업을 수행하도록 할 수 있다.  
```
type Context interface {
	Deadline() (deadline time.Time, ok bool)
}
```  

### func WithTimeout(context.Background(), maxDuration)  
주로 네트워크 병목이 생기는 작업으로 고루틴으로 실행하는 경우가 많은데, 간혹 네트워크 문제로 timeout 발생하는 경우 있음  
-> 고루틴 안으로 context.WithTimeout 함수로 생성한 컨텍스트를 전달하여 일정한 시간이 지나면 고루틴을 자동으로 종료하도록 해서 고루틴이 무한정 길어지는 것을 막을 수 있음  
```
ctx, cancel := context.WithTimeout(context.Background(), maxDuration)

go func() {
	// 고루틴을 종료해야 할 상황이 되면 cancel 함수 실행
	cancel()
}()

start := time.Now()
result, err := longFuncWithCtx(ctx)
fmt.Printf("duration:%v result:%s\n", time.Since(start), result)
```  

## 활용 예시  

### http.Request  
```
package http

type Request struct {
	Method string	
	Header Header
	Body io.ReadCloser		

	/* ... */

	ctx context.Context
}
```  
웹 요청이 완료될 때까지 유지해야하는 값을 ctx에 보관, 필요한 곳에서 사용  
주로 미들웨어에서 요청 상태 확인해서 http.Request의 컨텍스트에 필요한 값을 담아서 다음 핸들러로 전달  


## 출처  
* https://pkg.go.dev/context. 
* https://yoongrammer.tistory.com/35  
* http://happinessoncode.com/2021/10/04/golang-context/#context-TODO  
* https://jaehue.github.io/post/how-to-use-golang-context/  

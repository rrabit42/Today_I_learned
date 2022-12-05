# Interface Type  

## Go 인터페이스  
* 구조체(struct): 필드(field)들의 집합체  
* Interface: 메서드(method)들의 집합체  
interface는 type이 구현해야하는 메서드 원형(prototype)들을 정의한다.  

하나의 사용자 정의 타입이 interface를 구현하기 위해서는 단순히 그 인터페이스가 갖는 모든 메서드들을 구현하면 됨.  

## 인터페이스 타입  
Go 프로그래밍을 하다보면 자주 보는 빈 인터페이스(empty interface), 인터페이스 타입(interface type)으로도 불림.  
> ```interface{}```  

Empty interface는 메서드를 전혀 갖지 않는 빈 인터페이스로서, Go의 모든 Type은 적어도 0개의 메서드를 구현하므로, 흔히 Go에서 **모든 Type을 나타내기 위해 빈 인터페이스를 사용**함  
즉, 빈 인터페이스는 어떠한 타입도 담을 수 있는 컨테이너, 다른 언어에서 흔히 일컫는 Dynamic Type  
(empty interface는 C#, Java에서 object라 볼 수 있으며, C/C++에서는 void* 와 같다고 볼 수 있음)  

## any vs interface{}  
미리 선언된 타입 any는 빈 인터페이스의 별칭이다. [공식문서](https://go.dev/ref/spec#Interface_types)  
서로 교환하여 쓸 수 있지만, any는 Go 1.18 버전 이후부터 가능하다.  

그러나 type parameter 로서의 any와 regular function argument 로서의 any는 차이가 있다.  
> 더 알아보기: Go의 제너릭 함수  

```
func printInterface(foo, bar any) {
  fmt.Println(foo, bar)
}

func printAny[T any](foo, bar T) {
  fmt.Println(foo, bar)
}
```  

printAny은 두 인자는 같은 type으로 인식한다. (T라는 type으로 같이 초기화 되었으므로)  
그와 반대로, printInterface는 (```printInterface(foo, bar interface{})```와 동일)의 인자들은 각각 any로 초기화 된 것이므로 서로 다른 타입을 가질 수 있다.  

```
printInterface(12.5, 0.1)    // ok
printInterface(12.5, "blah") // ok, int and string individually assignable to any

printAny(10, 20)             // ok, T inferred to int, 20 assignable to int
printAny(10, "k")            // compiler error, T inferred to int, "k" not assignable to int
printAny[any](10, "k")       // ok, T explicitly instantiated to any, int and string assignable to any

printAny(nil, nil)           // compiler error, no way to infer T
printAny[any](nil, nil)      // ok, T explicitly instantiated to any, nil assignable to any
```

gerneric 함수(이 예제에서는 printAny)은 명시적인 타입의 인자 없이 nil로 호출될 수 없다.  
왜냐하면 nil 혼자서는 타입 정보를 가지고 있지 않기 떄문에 컴파일러는 T를 추론할 수 없기 때문이다.  
그러나 interface type에서 nil은 정상적으로 할당(assign) 된다.  


## 출처  
http://golang.site/go/article/18-Go-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4  
https://stackoverflow.com/questions/71628061/difference-between-any-interface-as-constraint-vs-type-of-argument  



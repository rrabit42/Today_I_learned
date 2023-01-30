# Channel  
~~자료 읽는중~~  

## 1. FIFO  
채널은 send한 순서대로 데이터를 receive  

## 2. goroutine-safe  
thread-safe: 멀티 thread 프로그래밍에서 여러 쓰레드로부터 변수에 동시에 접근이 이뤄져도 프로그램 실행에 문제가 없음  
즉, 여러 goroutine이 하나의 채널에 동시에 접근해도 프로그램 실행에 문제가 없다.  
* 여러 고루틴이 동시에 하나의 채널에 여러 데이터를 send할 때, 누락되는 데이터 존재 X  
* 여러 고루틴이 동시에 하나의 채널에서 receive하려고 할 때, 중복으로 receive 하는 데이터 존재 X  

## 3. 서로 다른 goroutine에서 send/receive할 수 있다  
```
size := 10
ch := make(chan int, size)

// goroutine1
go func() {
	for i := 0; i < size; i++ {
		ch <- i
	}
}()
	
// goroutine2
go func() {
	for {
		select {
		case value := <-ch:
		}
	}
}()
```  
1번 고루틴에서 send한 데이터를 2번 고루틴에서 receive할 수 있다.  

## 4. block, unblock  
채널을 사용하다보면 어떤 시점에 goroutine이 block 되기도, unblock 되기도 한다.  
* block  
```
ch := make(chan int, 3)
go func() {
  ch <- 1
  ch <- 2
  ch <- 3
  ch <- 4 // block
  // do something
}
```  
이미 채널 버퍼가 꽉 찬 상태에서 데이터를 receive 하려고 하면 위 goroutine은 block되어서 그 후의 do something을 수행 X  

* unblock  
```
ch := make(chan int, 3)
go func() {
	ch <- 1
    ch <- 2
    ch <- 3 
    ch <- 4 // 2) unblock
    // 3) do something
}

go func() {
	<-ch // 1) 이게 실행되면
}
```  
위 고루틴이 block된 시점에 또 다른 고루틴이 데이터를 receive하면 채널에 자리가 생기면서 block이 풀리고(unblock)  
```ch<-4```, ```do something```이 동작하게 된다.  

## 채널 방향  
* 기본적으로 채널은 양방향이지만 단방향 채널도 만들 수 있음  
```
// 수신용 채널
c1 := make(<-chan Type)

// 송신용 채널
c2 := make(chan<- Type)
```  

* 함수 인자를 사용하여 양방향 채널을 단방향 채널로 변환할 수 있음  
양방향 채널을 수신 전용 또는 송신 전용 채널로 변환할 수 있지만 그 반대의 경우는 불가능  
```
package main

import "fmt"

func sending(s chan<- string) {
	s <- "Hello"
}

func main() {
  // 양방향 채널 생성
  myChannel := make(chan string)
  
  // sending 함수를 호출하여 양방향 채널을 송신용 채널로 변경
  go sending(myChannel)

  fmt.Println(<-myChannel)
}
```  
* 단반향 채널을 사용하면 프로그램의 타입 안정성을 높일 수 있음  

## 채널 버퍼링(channel buffering)  
go 언어에서 채널은 unbuffered channel, buffered channel 두 가지 종류가 있음  

### Unbuffered Channel  
기본적으로 채널은 Unbuffered. Unbuffered 채널은 버퍼링 되지 않기 때문에 수신자가 데이터를 받을 때까지 **송신자는 block**이 걸리기 때문에 동기화가 보장됨.  
```
package main

import "fmt"

func main() {
  // Unbuffered 채널 생성
  done := make(chan bool)

  fmt.Println("Main start")
  go func() {
    done <- true
    for i:= 0; i < 3; i++ {
      fmt.Println("Goroutine1: ", i)
    }
  } ()

  time.Sleep(100 * time.Millisecond)
  go func() {
    for i:= 0; i < 3; i++ {
      fmt.Println("Goroutine2: ", i)
    }
  <-done
  } () 

  time.Sleep(100 * time.Millisecond)
  fmt.Println("Main end")
}
```  
Output:  
```
Main start
Goroutine2:  0
Goroutine2:  1
Goroutine2:  2
Goroutine1:  0
Goroutine1:  1
Goroutine1:  2
Main end
```  

고루틴1은 고루틴2에서 데이터를 수신하기 전까지 block이 걸리기 때문에 위와 같은 순서로 출력을 하게 됨  

### Buffered Channel  
Buffered 채널은 수신자가 받을 준비가 되어 있지 않아도 지정된 버퍼만큼 데이터를 보내고 계속 다른 일을 수행할 수 있기 때문에 비동기적으로 동작할 수 있음  
buffered 채널에서 송신 측은 버퍼가 가득찬 경우에만 차단되고, 수신측에서는 버퍼가 비어있을 때만 차단됨.  

Buffered channel은 make(chan Type, N) 함수를 사용하여 만듦. N: 사용할 버퍼 개수  
```myChannel := make(chan Type, N)```  
N이 0이면 Unbuffered Channel과 같음  

```
package main

import "fmt"

func main() {
  // Bufferd 채널 생성
  done := make(chan bool, 1)

  fmt.Println("Main start")
  go func() {
    done <- true
    for i:= 0; i < 3; i++ {
      fmt.Println("Goroutine1: ", i)
    }
  } ()

  time.Sleep(100 * time.Millisecond)
  go func() {
    for i:= 0; i < 3; i++ {
      fmt.Println("Goroutine2: ", i)
    }
  <-done
  } () 

  time.Sleep(100 * time.Millisecond)
  fmt.Println("Main end")
}
```  
Output:  
```
Main start
Goroutine1:  0
Goroutine1:  1
Goroutine1:  2
Goroutine2:  0
Goroutine2:  1
Goroutine2:  2
Main end
```  
고루틴1에서 메세지를 보내고 block 되지 않고 바로 다음 일을 수행하기 때문에 위와 같은 순서로 출력됨  

## Select  
Go언어에서 select문은 switch문과 비슷하지만  
select문에서 case문은 채널에서 전송 또는 수신 작업 의미  

select는 case들 중 하나가 실행될 때까지 대기. 만약 다수의 case가 준비되는 경우에는 select가 무작위로 하나를 선택  
select문에 default문이 있으면, case문 채널이 준비되지 않더라도 대기하지 않고 바로 default문 실행  

```
select {
  case <-ch1:
    // 채널1에 값이 들어왔을 때 수행
  case <-ch2:
    // 채널2에 값이 들어왔을 때 수행
  default:
    // 모든 채널에 값이 들어오지 않았을 때 수행
}
```  

보통 select를 계속 처리하기 위해 for 반복문을 사용한다.  


## 출처  
* https://velog.io/@moonyoung/golang-channel-with-select-%ED%97%B7%EA%B0%88%EB%A6%AC%EB%8A%94-%EC%BC%80%EC%9D%B4%EC%8A%A4-%EC%A0%95%EB%A6%AC%ED%95%98%EA%B8%B0  
* https://velog.io/@moonyoung/Deep-Dive-to-Golang-Channel  
* https://yoongrammer.tistory.com/11  

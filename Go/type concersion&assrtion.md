# Type conversion 과 Type assertion  
```
v1 := int(n)  // type conversion
v2, ok := n.(int) // type assertion
```  
v1도 정수이고 v2도 정수일 것이다(에러가 나지 않는다면)  

## type conversion  
type casting. Golang에서는 명시적으로 형변환을 해줘야 함. 자동으로 형변환을 해주는 일 따위는 없음.  
ex) int형을 int64형으로 바꾸고 싶다면 아래와 같이 명시적으로 코드 작성해야 함.  
```
var n int = 15
var v1 int64 = int64(n)
```  

## type assertion  
결론부터 말하자면, interface type의 value type을 확인하기 위함.  

assert: If someone **asserts** a fact or belief, they state it firmly.  
사실임을 주장하는 것. (conversion은 전환, 개조)  
type assertion은 형 변환과는 상관이 없음.  
> type assertion provides access to an interface value's underlying concrete value  
인터페이스가 가지고 있는 실제 값(concrete value)에 접근할 수 있게 함.  
Golang의 인터페이스는 임의의 타입 값을 가질 수 있기 때문에 concrete라는 표현을 쓴 듯하다.  

interface type의 x와 타입 T를 x.(T)로 표현했을 때,  
* x가 nil이 아니며,  
* x는 T 타입에 속한다는 점을  

확인하기(assert)하는 것으로 이러한 표현을 Type assertion 이라고 부른다.  
* x가 nil이거나 x의 타입이 T가 아니라면 -> Runtime ERROR  
* x가 T타입인 경우 -> T 타입의 x를 리턴한다.  

```
package main

func main() {
  var a interface{} = 1
  
  i := a        // a와 i는 dynamic type이고 값은 1
  j := a.(int)  // j는 int 타입이고, 값은 1
  
  println(i)  // 포인터 주소 출력
  println(j)  // 1 출력
}
```  
* 여기서 value a를 담은 i를 출력했을 떄 포인터의 주소가 출력되는 부분을 주목!  
즉, interface value의 직접적인 값을 얻기 위해서는 **type assertion** 을 반드시 사용해야한다.  


```
v, ok := n.(int)
```  
어떤 인터페이스 값이 특정 타입인지 확인하기 위해서 ok 값을 받을 수도 있다.  
만약 n의 타입이 int가 아니라면 v는 int의 zero value인 0이 될 것이고, ok는 false가 될 것이다.  
(타입이 int가 맞다면 int 타입의 n을 리턴하고, ok는 true가 됨)  

## 예제  
### 정상적인 타입 선언  
#### 실제 구조체로 타입 선언  
```
func Example_TypeAssertion_인터페이스_데이터_타입_Student_값을_가져온다() {
	var p Person = Student{"Frank", 13, "1111"}
	fmt.Println(p.getName())

	s := p.(Student) //Person -> Student - student의 실제 값을 가져온다.
	fmt.Println(s.getName())
	fmt.Println(s.getPhone())

	//Output:
	//Frank
	//Frank
	//1111
}
```  
인터페이스 p 변수는 Student 구조체 값을 보유하고 있음.  
p.(Student) 타입 단언으로 p 인터페이스값에서 실제 Student 구조체 값을 얻어와 해당 데이터 타입의 메서드를 실행함.  

#### 다른 인터페이스로 타입 선언  
```
func Example_TypeAssertion_다른_인터페이스로_값을_가져온다() {
	var p Person = Student{"Frank", 13, "1111"}

	ph := p.(Phone) //Person -> Phone
	fmt.Println(ph.getPhone())

	//Output:
	//1111
}
```  
Person 인터페이스에서 다른 인터페이스인 Phone 으로 타입 단언을 하여 Phone 인터페이스의 메서드를 실행할 수 있음.  

### panic 발생 타입 선언  
#### 인터페이스가 타입 T의 동적 값을 가지고 있지 않은 경우  
```
//타입 T가 인터페이스를 구현하고 있지 않기 때문에 컴파일 에러가 발생한다
func Example_TypeAssertion_인터페이스가_타입_T의_동적_값을_소유하지_않을_경우_컴파일_에러가_발생한다() {
	//var p Person = Student{"Frank", 13, "1111"}
	//value := p.(string) //impossible type assertion: string does not implement person (missing getName method)
	//fmt.Printf("%v, %T\n", value, value)

	//Output:
}
```  
타입 단언 시 i.(T) 타입 T가 인터페이스 메서드를 구현하고 있지 않으면, 인터페이스 i가 타입 T의 동적 값을 보유할 수 없기 때문에 impossible type assertion 컴파일 에러가 발생  

#### 인터페이스가 타입 T의 실제 값을 가지고 있지 않는 경우  
타입 T는 구현된 메서드가 있지만, 인터페이스 i가 실제 값을 가지고 있지 않으면 Go에서 런타임시 panic이 발생  
```
func Example_TypeAssertion_인터페이스가_타입_T의_실제_값을_가지고_있지_않는_경우_panic이_발생한다() {
	var p Person = nil
	//value := p.(Student) //panic: interface conversion: go_type_assertions.Person is nil, not go_type_assertions.Student
	value, ok := p.(Student)
	fmt.Printf("(%v, %T), ok: %v\n", value, value, ok)

	//Output:
	//({ 0 }, go_type_assertions.Student), ok: false
}
```  
런타임시 panic 발생을 피하려면 타입 단언 시 ok 반환 값을 추가로 받으면 된다.  
ok 변수를 통해서 타입 T가 인터페이스 i를 구현했는지, i가 실제 타입 T를 갖고 있는지 확인할 수 있다.  
모두 만족하면 ok는 true를 반환하고 아닌 경우에는 false를 반환한다.  

### 다른 인터페이스가 타입 T를 구현하지 않고 있는 경우  
```
type Animal interface {
	walk()
}

func Example_TypeAssertion_다른_인터페이스가_타입_T를_구현하지_않고_있으면_panic이_발생한다() {
	var p Person = Student{"Frank", 13, "1111"}
	//value := p.(Animal) //panic: interface conversion: go_type_assertions.Student is not go_type_assertions.Animal: missing method walk
	value, ok := p.(Animal)
	fmt.Printf("(%v, %T) %v\n", value, value, ok)

	//Output:
	//(<nil>, <nil>) false
}
```  
Student 구조체는 Animal 인터페이스를 구현하지 않았기 때문에 p.(Animal) 타입 단언시 panic이 발생.  


## 출처  
https://2kindsofcs.tistory.com/13  
https://iamjjanga.tistory.com/47#recentComments  
https://blog.advenoh.pe.kr/go/%ED%83%80%EC%9E%85-%EB%8B%A8%EC%96%B8-Type-Assertion/  

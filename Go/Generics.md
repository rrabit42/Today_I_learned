# Generics  
역시나 Go 1.18 버전에서 추가되었다.  

제네릭 프로그래밍: 타입 파라미터를 통해서 하나의 함수나 타입이 여러 타입들에 대해서 동작할 수 있도록 하여 코드 재사용성을 늘리는 기법  
* 하나의 함수나 타입이 여러 타입에 대해서 동작할 수 있도록 정의 가능  
* 하나의 코드로 여러 타입에 대해서 재사용 가능  

ex)  
```
func add(a, b int) int {
    return a + b
}
```  
Go는 강타입 언어이기 때문에 add() 함수는 오직 int 타입에서만 동작하게 됨  
```
var a float32 = 3.14
var b float32 = 1.43

c := add(a, b)            // build failed
```  
그래서 위와 같이 float32 인자로 add() 함수를 호출하면 에러가 발생하게 됨  
이 문제를 해결하기 위해서는 float32에서 동작하는 새로운 함수를 다음과 같이 만들어야 함  
```
func addFloat32(a, b float32) float32 {
    return a + b
}
```  
이처럼 각 타입별로 함수를 만드는 것은 매우 귀찮은 작업이고, 함수 동작이 바뀐다면 여러 함수를 모두 변경해야하기 때문에 유지보수도 좋지 않음.  
제네릭 프로그래밍을 사용하면 이 문제를 다음과 같이 해결할 수 있음.  

```
package main

import (
    "constraints"
    "fmt"
)

// 타입 파라미터를 사용해서 제네릭 함수를 정의하는 부분  
func add[T constraints.Integer | constraints.Float](a, b T) T {
    return a + b
}

func main() {
    var a int = 1
    var b int = 2
    fmt.Println(add(a, b))

    var f1 float64 = 3.14
    var f2 float64 = 1.43
    fmt.Println(add(f1, f2))
}
```  

## type parameter(타입 파라미터)  
```
['식별자' '타입 제한자', '식별자' '타입 제한자']
```
* 식별자: 함수 혹은 구조체 내에 쓸 타입의 식별자  
* 타입 제한자: 제네릭에서 새롭게 추가된 요소로, 들어갈 타입의 범위를 제한하는 역할  

## 제네릭 vs interface{}  
* 제네릭에서의 타입 추론  
* Go 제네릭을 사용하지 않고도 빈 인터페이스를 사용해 비슷하게 사용할 수는 있다.  
* 빈 인터페이스를 사용하면 타입의 제한 없이 파라미터를 전달할 수 있다.  
* 하지만 아래 예제와 같이 빈 인터페이스가 가지는 한계가 존재하며 제네릭만의 장점이 있다.  

```
package main

import "fmt"

func foo1(a interface{}) interface{} {
    return a
}

func foo2[T any](a T) T {
    return a
}

func main() {
    var (
        a int = 10
        b int = 20
        c int
    )
    c = foo1(a).(int)   // 리턴 타입이 interface{} 이다.
    fmt.Println(c)
    c = foo2(b) // 리턴 타입이 int이다.
    fmt.Println(c)
}
```  
* foo1 함수는 빈 인터페이스를 반환하기 때문에 이를 int 타입에 넣기 위해서는 형변환이 필요  
*  foo2 함수는 파라미터와 반환 타입이 any로 되어 있고, foo2 함수에 int 타입의 파라미터가 전달되면 자동으로 반환도 int 타입으로 되어 형변환이 필요 없다.  
*  제네릭은 타입 추론이 된다. foot2 함수의 경우는 **전달된 파라미터 타입을 통해 반환할 타입이 추론**된다.  
이는 컴파일 시점에 정해지며 작성된 타입별로 내부의 코드가 생산되는 것이다.  
foo2 함수에 int 타입의 파라미터를 전달하면 반환되는 파라미터도 int 타입이고, 파라미터 타입이 string 이라면 반환되는 타입도 string이 되는 것이다.  
형변환이 없으니 약간의 성능상의 이점도 있다.  

> 빈 인터페이스를 이용하는 경우, 모든 타입 값을 가질 수 있으나 그 값을 사용할 때 실제 타입 값으로 타입 변환을 해야 하고, 넣을 때 값의 타입과 뺄 때 값의 타입을 정확히 알고 있어야 함  
> 제네릭 타입을 사용하는 경우, 타입 파라미터에 의해서 필드 타입이 결정되므로 값을 사용할 때 타입 변환이 필요 없음  

```
var v1 int = 3
var v2 interface{} = v1             // boxing
var v3 int = v2.(int)               // unboxing
```  
> 성능차이  
> 기본 타입 값을 빈 인터페이스 변수에 대입할 때 Go에서는 빈 인터페이스를 반들어서 기본 타입 값을 가리키도록 함  
> 박스에 넣는 것과 같다고 해서 이를 박싱(Boxing)이라고 함  
> 다시 값을 꺼낼 때는 박스에서 꺼내는 것과 같다고 해서 언박싱(Unboxing)이라고 함  
> 박싱할 때 빈 인터페이스 객체를 사용하게 됨  
> 참고로 박싱을 한 v2와 v1은 서로 다른 주소값을 가지고 있으므로 서로 다른 객체임  

![image](https://user-images.githubusercontent.com/46364778/198950073-1eaffbdb-f438-4cc6-9e84-b50c3d13323c.png)  
> 박싱한다는 것은 이 그림처럼 빈 인터페이스 박스 안에 실제 값인 int 타입 값인 3을 넣는다고 볼 수 있습니다. 즉 값을 감싸는 박스 객체를 만들어야 합니다.  
> 이 값을 감싸는 빈 인터페이스 박스는 크기가 매우 작기 때문에 성능상 큰 문제가 되지 않지만, 많아지면 박싱, 언박싱을 위해서 임시로 사용되는 박스 수가 늘어나기 때문에 문제가 될 수 있습니다.  
> 하지만 제네릭 프로그래밍을 사용하면 타입 파라미터에 의해서 타입이 고정되기 때문에 박싱, 언박싱이 필요없습니다. 그에 따라 값을 감싸는 임시 박스도 필요없어서 성능상 이득이 발생합니다.  

> 그럼 제네릭을 사용하는 게 무조건 이득일까요? 꼭 그렇지는 않습니다.  
```
func add[T constraints.Integer | constraints.Float](a, b T) T {
    return a + b
}

add(1, 3)
add(3.14, 1.43)
```  
> 14.2절에서 살펴보았던 제네릭 함수 add()를 살펴보겠습니다. add(1, 3)과 add(3.14, 1.43)이 마치 하나의 함수를 서로 다른 타입으로 두 번 호출한 것처럼 보이지만, 사실은 그렇지 않습니다.  
> add(1, 3)은 사실 add[int](1, 3) 함수를 호출한 것이고 add(3.14, 1.43)은 사실 add[float64](3.14, 1.43) 함수를 호출한 것입니다.  
> 제네릭 함수나 타입의 경우 하나의 함수나 타입처럼 보이지만 실제로는 **컴파일 타임에 사용한 타입 파라미터별로 새로운 함수나 타입을 생성해서 사용**하게 됩니다.  
> 따라서 제네릭 프로그래밍을 많이 사용할 경우 컴파일 타임에 생성해야 할 함수와 타입 갯수가 늘어나고 컴파일 시간도 더 걸리게 됩니다.  
> 또 생성된 코드양이 증가되어 실행 파일 크기가 늘어납니다. 실행 파일 크기는 일반적인 프로그램에서는 문제가 되지 않지만 용량의 제한이 있는 임베디드 프로그램에서는 문제가 될 수 있습니다.  

## 타입 제한자  
* Go 제네릭은 인터페이스와 유사하게 타입 제한자로서 기능을 제공  

```
package main

import "fmt"

func min[T any](a, b T) T {
    if a < b { // 문법 오류가 발생. any는 < 연산을 지원하지 않는다.
        return a
    }
    return b
}

func main() {
    var (
        a int = 10
        b b   = 20
    )
    fmt.Println(min(a, b))
}
```  
* 비교를 위해 사용한 빈 인터페이스의 alias인 any는 < 연산자가 없다. < 연산자를 가지는 타입을 타입 제한자로 사용하면 min 함수를 완성할 수 있다.  

```
package main

import "fmt"

func min[T int | int16 | int32 | int64 | float32 | float64](a, b T) T {
    if a < b { // 위 타입들이 < 연산자를 지원하기 때문에 문법 오류가 없다.
        return a
    }
    return b
}

func main() {
    var (
        a int     = 10
        b int     = 20
        c int16   = 10
        d int16   = 20
        e float32 = 3.14
        f float32 = 1.14
    )
    fmt.Println(min(a, b))
    fmt.Println(min(c, d))
    fmt.Println(min(e, f))
}
```  
* < 연산자를 가진 여러 개의 타입을 파이프(|) 연산자로 합쳤다.  
* 타입 제한자는 파이프 연산자로 여러 개를 쉽게 추가가 가능하다.  

* 제네릭 함수의 타입 파라미터는 그 함수가 호출되는 입력 인자에 따라 달라짐  
```
func Print(a, b int) {
    fmt.Println(a, b) 
}
```  
* Print(1,2)에서 T는 int 타입, Print("Hello", "World")에서 T는 string 타입으로 동작.  
* 하지만 Print(1, "Hello")는 서로 다른 타입의 두 인자 모두 T 타입으로 정의되어 있기 때문에 T 타입을 하나의 타입으로 정의할 수 없어 에러 발생  
* 이렇게 여러 개의 다른 타입에서도 동작하게 만들고 싶을 때는 각 타입 갯수에 맞는 함수 파라미터를 정의해줘야함  
```
func Print[T1 any, T2 any](a T1, b T2) {
    fmt.Println(a, b) 
}
```  
* 근데 이렇게 타입파라미터 쓸 바에야 그냥 인자로 interface{} 를 넣어주겠어!!  

### 타입 제한자 선언  
* 매번 타입 제한자를 만드는 것은 비효율적이므로 타입 제한자를 interface 키워드로 선언하여 사용이 가능하다.  
```
package main

import "fmt"

type ComparableNumbers interface {
    int | int16 | int32 | int64 | float32 | float64
}

func min[T ComparableNumbers](a, b T) T {
    if a < b { // 위 타입들이 < 연산자를 지원하기 때문에 문법 오류가 없다.
        return a
    }
    return b
}

func main() {
    var (
        a int     = 10
        b int     = 20
        c int16   = 10
        d int16   = 20
        e float32 = 3.14
        f float32 = 1.14
    )
    fmt.Println(min(a, b))
    fmt.Println(min(c, d))
    fmt.Println(min(e, f))
}
```  
* 반복되는 타입 제한자 묶음을 하나로 만들어 재사용이 가능하고 가독성도 높게 되었다.  


```
package main

import "fmt"

type Integer interface {
    int | int16 | int32 | int64
}

type Float interface {
    float32 | float64
}

type ComparableNumbers interface {
    Integer | Float
}

func min[T ComparableNumbers](a, b T) T {
    if a < b {
        return a
    }
    return b
}

func main() {
    var (
        a int     = 10
        b int     = 20
        c int16   = 10
        d int16   = 20
        e float32 = 3.14
        f float32 = 1.14
    )
    fmt.Println(min(a, b))
    fmt.Println(min(c, d))
    fmt.Println(min(e, f))
}
```  
* 타입 제한자끼리 합치는 것도 가능하다.  
타입 제한자 Integer와 Float를 합쳐서 ComparableNumbers 타입 제한자를 만들었다.  

### 타입 제한자 vs 인터페이스  
* 인터페이스는 타입 제한자로 사용이 가능하지만, 타입 제한자는 인터페이스로 사용이 불가능하다.  
* 타입 제한자와 인터페이스를 합치는 것도 가능하다. 다만, 타입 제한자와 인터페이스를 합치면 타입 제한자로만 사용할 수 있다.  
```
package main

import (
    "fmt"
)

// 인터페이스. 타입 제한자로 사용 가능
type ToString interface {
    String() string
}

func PrintCat[T ToString](a, b T) {
    fmt.Printf("%s-%s", a.String(), b.String())
}

// 타입 제한자. 인터페이스로 사용 불가능
type Integer interface {
    ~int8 | ~int16 | ~int32 | ~int64 | ~int
}

// 문법 오류 발생
func PrintMin1(a, b Integer) {
    if a < b {
        fmt.Println(a.String())
    } else {
        fmt.Println(b.String())
    }
}

// 타입 제한자+인터페이스 ==> 타입 제한자. 인터페이스로 사용 불가능
type Stringer interface {
    Integer
    ToString
}

func PrintMin2[T Stringer](a, b T) {
    if a < b {
        fmt.Println(a.String())
    } else {
        fmt.Println(b.String())
    }
}

// 문법 오류 발생
func PrintMin3(a, b Stringer) {
    if a < b {
        fmt.Println(a.String())
    } else {
        fmt.Println(b.String())
    }
}

type MyInt int

func (m MyInt) String() string {
    return fmt.Sprintf("%d", m)
}

func main() {
    var a MyInt = 10
    var b MyInt = 100
    PrintMin(a, b)
    PrintCat(a, b)
}
```  
* 타입 제한자도 인터페이스도 뒤에 interface라는 키워드를 통해 선언한다.  
얼핏 보아서는 헷갈릴 수밖에 없다. 새로운 키워드를 만들어내지 않는 이유는 Go 언어의 철학 때문이라고 생각되는데 Go 언어는 최대한 간결한 문법을 원한다. 그래서 새로운 키워드의 도입이 아닌 기존에 사용하던 interface 키워드를 같이 사용한 것으로 생각된다.  
* 타입 제한자가 인터페이스 키워드를 같이 사용하기보다는 인터페이스가 타입 제한자의 역할도 같이 하기 때문에 인터페이스의 확장이라고 보는 것이 좋을 것 같다.  

* 타입 제한자가 interface 키워드를 사용하기 때문에 일반 인터페이스처럼 메서드 조건까지 더할 수 있음  
```
package main

import (
    "fmt"
    "hash/fnv"
)

type ComparableHasher interface {           // ❶
    comparable
    Hash() uint32
}

type MyString string                           // ❷

func (s MyString) Hash() uint32 {
    h := fnv.New32a()
    h.Write([]byte(s))
    return h.Sum32()
}

func Equal[T ComparableHasher](a, b T) bool {  // ❸ 
    if a == b {
        return true
    }
    return a.Hash() == b.Hash()
}

func main() {
    var str1 MyString = "Hello"
    var str2 MyString = "World"
    fmt.Println(Equal(str1, str2))
}
```  
* ❶ ComparableHasher라는 이름의 타입 제한을 정의했습니다.  
comparable은 ==, != 를 지원하는 타입들을 정의한 Go 내부 타입 제한입니다.  
그리고 이 제한에는 Hash() uint32 메서드를 포함하도록 제한했습니다.  
그래서 ComparableHasher는 ==와 !=를 지원하고 Hash() uint32 메서드를 포함한 타입만 가능하게 됩니다.  
* ❷ MyString이란 string 별칭 타입을 정의하고 Hash() uint32 메서드를 포함하도록 했습니다.  
이로써 MyString은 ComparableHasher 제한에 만족한 타입이 됩니다.  
* ❸ ComparableHasher 제한을 사용하는 Equal()이라는 제네릭 함수를 정의했습니다.  
이 함수는 먼저 == 연산자로 둘이 같은지 확인하고 만약 다를 경우 Hash() 메서드 호출 결과로 다시 한번 확인해서 같은지 확인하는 함수입니다.  
이처럼 타입 제한에 일반 인터페이스와 같이 특정 메서드를 포함하도록 제한을 추가할 수 있습니다.  

> 타입 제한에 메서드 조건을 포함시킬 수 있고, 같은 interface를 사용하지만, 둘은 같지 않고 서로 다른 개념.  
> 타입 제한은 제네릭 프로그래밍의 타입 파라미터에서만 사용될 수 있고 일반 인터페이스처럼 사용할 수 없음  
```
func Equal(a, b ComparableHasher) bool { // Error
```  
> ComparableHasher는 타입 제한을 포함하고 있기 때문에 일반 인터페이스처럼 사용할 수 없어서 에러 발생.  
> 타입 제한을 포함한 인터페이스는 반드시 타입 파라미터로 정의되어야 함. 그래서 다음 코드는 에러가 발생하지 않습니다.  
```
func Equal[T ComparableHasher](a, b T) bool { // OK
```  


### constraints 패키지  
[Go 1.18 Release Notes](https://tip.golang.org/doc/go1.18)  

#### type Ordered  
```
package main

import (
    "fmt"
    "golang.org/x/exp/constraints"
)

func min[T constraints.Ordered](a, b T) T {
    if a < b {
        return a
    }
    return b
}

func main() {
    var (
        a int     = 10
        b int     = 20
        c int16   = 10
        d int16   = 20
        e float32 = 3.14
        f float32 = 1.14
    )
    fmt.Println(min(a, b))
    fmt.Println(min(c, d))
    fmt.Println(min(e, f))
    var (
        h = "Hello"
        i = "World"
    )
    fmt.Println(min(h, i))
}
```  
* 순서가 있는 타입들을 선언한 타입 제한자  
* constraints.Ordered 은 크기 비교가 가능한 타입 제한자입니다.  
새로 추가된 comparable 키워드는 == 또는 != 연산이 가능한 타입 제한자입니다.  

```
type Float interface {
    ~float32 | ~float64
}
```  
* float32 앞에 ~(틸트)가 붙어 있습니다. 이 토큰도 Go 1.18에서 새로 추가된 문법으로 "확장된" 이란 의미를 가집니다.  
* ~는 해당 타입을 기본으로 하는 모든 별칭 타입까지 포함한다는 얘기  

```
package main

import (
    "fmt"
)

type Integer interface {
    int | int8 | int16 | int32 | int64
}

type MyInt int

func min[T Integer](a, b T) T {
    if a < b {
        return a
    }
    return b
}

func main() {
    var (
        a int = 10
        b int = 20
    )
    fmt.Println(min(a, b))
    var (
        c MyInt = 10
        d MyInt = 20
    )
    fmt.Println(min(c, d))    // possibly missing ~ for int in constraint Integer
    // type Integer 안에 'int' 앞에 틸트(~)를 붙여워야 에러가 안난다. '~int'
}
```  
* MyInt는 int를 확장한 타입입니다. Integer 타입 제한자에서 이를 수용하기 위해서는 int 앞에 ~(틸트)를 붙여줘야 합니다.  

```

import (
    "fmt"
)

type Integer interface {
    ~int | int8 | int16 | int32 | int64
}

type MyInt int

func min[T Integer](a, b T) T {
    if a < b {
        return a
    }
    return b
}

func main() {
    var (
        a int = 10
        b int = 20
    )
    fmt.Println(min(a, b))
    var (
        c MyInt = 10
        d MyInt = 20
    )
    fmt.Println(min(c, d))
}
```  
* 변수 c, d는 MyInt를 사용하고 Integer 인터페이스에는 ~int 가 있어서 int를 확장한 MyInt를 사용할 수 있다.  


## 제네릭 타입  
* Go 제네릭은 함수와 구조체에서 사용이 가능. 아직 메소드에서 사용은 불가능  
```
package main

import "fmt"

type Node[T any] struct {
    val  T  // struct 의 value 타입을 T로 사용한다.
    next *Node[T]
}

func NewNode[T any](v T) *Node[T] { // 새로운 Node를 만들 때도 제네릭이 필요하다.
    return &Node[T]{val: v}
}

/*
Node의 메소드인 Push에 제네릭 T가 포함된다. 하지만 이곳에서 새로운 다른 제네릭을 선언하거나 사용하는 것은 문법 오류이다.
문법오류: func (n *Node[T]) Push[F any](f F) * Node[T] 
*/
func (n *Node[T]) Push(v T) *Node[T] {
    node := NewNode(v)
    n.next = node
    return node
}

func main() {
    node1 := NewNode(1) // *Node[int]
    node1.Push(2).Push(3).Push(4)

    for node1 != nil {
        fmt.Println(node1.val)
        node1 = node1.next
    }

    node2 := NewNode("hello") // *Node[string]
    node2.Push("how").Push("are").Push("you").Push("?")

    for node2 != nil {
        fmt.Println(node2.val)
        node2 = node2.next
    }
}

/*
1
2
3
4
hello
how
are
you
?
*/
```  
* Node 구조체는 제네릭 변수를 가지고 있다.  
* NewNode 함수에 전달된 파리미터 타입에 따라 컴파일 시점에 Node 구조체의 타입이 결정되며 타입별로 내부 코드가 생성되는 것이다.  
작성자 입장에서는 1개의 Node 구조체이지만 컴파일된 코드는 타입별로 만들어진다.  
그래서, main()의 NewNode 함수에 전달되는 int 파라미터에 의해 Node[int] 구조체가 만들어지고, string 파라미터에 의해 Node[string] 구조체가 만들어진다.  
타입 추론이 되므로 Push로 Node를 추가할 때 동일한 타입만 받는다.  

## 제네릭 구조체  
```
type Node[T any] struct {
    val T
    next *Node[T]
}
```  
* Node 구조체는 타입 파라미터를 사용해서 val 필드 타입이 어떤 타입이든 가능하도록 정의하고 있음.  
* 
## 제네릭 함수  
* 위 예제들에서 보았듯이 함수에서 제네릭 사용이 가능  
```
package main

import (
    "fmt"
    "strings"
)

func Map[F, T any](s []F, f func(F) T) []T {
    rst := make([]T, len(s))
    for i, v := range s {
        rst[i] = f(v)
    }
    return rst
}
func main() {
    doubled := Map([]int{1, 2, 3}, func(i int) int {
        return i * 2
    })
    fmt.Println(doubled)

    uppered := Map([]string{"Hello", "world", "abc"}, func(s string) string {
        return strings.ToUpper(s)
    })
    fmt.Println(uppered)

    toString := Map([]int{1, 2, 3}, func(i int) string {
        return fmt.Sprintf("str%d", i)
    })
    fmt.Println(toString)
}

/*
[2 4 6]
[HELLO WORLD ABC]
[str1 str2 str3]
*/
```  

* 

## 요약  
* 제네릭 프로그래밍은 타입 파라미터를 통해서 하나의 함수나 타입이 여러 타입에 대해서 동작할 수 있도록 해준다.  
* '타입 제한자'를 이용해 타입 파라미터로 사용되는 타입을 제한한다.  
* 인터페이스도 타입 제한자로 사용이 가능하다.  
* 적용할 수 있는 곳  
1. 함수  
2. 타입 제한자  
3. 제네릭 타입(구조체)  
* 적용하면 좋은 곳  
자료구조. 타입에 관계없는 슬라이스, 맵, 채널; 일반적인 타입에 대해서 같은 동작을 보장해야 하기 때문에 제네릭 사용하기 좋음  
다양한 타입에 대해서 다양한 동작을 하는 경우  
* 사용하기 좋지 않은 곳  
객체의 타입이 아닌 객체의 기능이 강조되는 곳에서는 제네릭이 아닌 인터페이스를 사용하는게 좋음  

제네릭 프로그래밍은 여러 타입에 대해서 같은 동작을 하는 코드를 하나의 제네릭 함수나 타입으로 표현할 수 있기 때문에 코드 재사용성에 도움이 됨  
하지만 너무 많이 사용하면 코드 가독성이 떨어짐  
"동작하는 코드 먼저, 제네릭은 나중에"  
"성급한 최적화가 프로그래밍에서 모든 죄악의 뿌리이다."  

## 출처  
https://go.dev/blog/when-generics  
https://meetup.toast.com/posts/320  
https://go.dev/doc/tutorial/generics  
https://goldenrabbit.co.kr/2022/01/28/%EC%83%9D%EA%B0%81%ED%95%98%EB%8A%94-go-%EC%96%B8%EC%96%B4-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-go-%EC%A0%9C%EB%84%A4%EB%A6%AD%EC%9D%98-%EC%9D%B4%ED%95%B4/  



# Variadic Functions  
변수의 개수가 가변적인 함수  
ex) fmt.Println  

* 데이터 타입 앞에 "..." 붙여주기
* Variadic 함수에서 매개변수는 슬라이스로 다룬다.  

```
package main

import "fmt"

func sum(nums ...int) {
    fmt.Print(nums, " ")
    total := 0
    for _, num := range nums {
        total += num
    }
    fmt.Println(total)
}

func main() {
    sum (1,2)
    sum (1,2,3)
    
    nums := []int{1,2,3,4,5}
    sum(nums...)  // 이렇게 슬라이스를 직접 사용해도 됨
}
```  

## 출처  
https://www.joinc.co.kr/w/GoLang/example/variadicFunction  


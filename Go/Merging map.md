# Merging map  
Go 1.18부터 Copy 함수를 이용해 손쉽게 map 을 merge할 수 있게 되었다. [공식문서](https://pkg.go.dev/golang.org/x/exp/maps)  

```
package main

import (
    "fmt"

    "golang.org/x/exp/maps"
)

func main() {
    src := map[string]int{
        "one": 1,
        "two": 2,
    }
    dst := map[string]int{
        "two":   2,
        "three": 3,
    }
    maps.Copy(dst, src)
    fmt.Println("dst:", dst)
    fmt.Println("src:", src)
}
```  

* 결과  
```
dst: map[one:1 three:3 two:2]
src: map[one:1 two:2]
```  
Copy 함수의 첫번째 map에 두번째 map이 추가된다. 이 때 key 값이 이미 존재하면, 두번째 map의 value으로 덮어씌워진다.  
따라서 map의 key값은 concrete 해야 한다.  


## 출처  
https://stackoverflow.com/questions/22621754/how-can-i-merge-two-maps-in-go  

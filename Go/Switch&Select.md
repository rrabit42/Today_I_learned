# Switch & Select  

## Switch  


## Select  
여러 채널을 손쉽게 사용할 수 있도록 select 분기문 제공  


## 차이점  
select is only used with channels.  
switch is used with concrete types.  
switch can also go over types for interfaces when used with the keyword .(type)  
```
var a interface{}
a = 5
switch a.(type) {
case int:
     fmt.Println("an int.")
case int32:
     fmt.Println("an int32.")
}
// in this case it will print "an int."
```  

select will choose multiple valid options at random . 
switch will go in sequence (and would require a fallthrough to match multiple).  


## 출처  
* https://stackoverflow.com/questions/38821491/what-is-the-difference-between-switch-and-select-in-go  
* https://velog.io/@moonyoung/golang-channel-with-select-%ED%97%B7%EA%B0%88%EB%A6%AC%EB%8A%94-%EC%BC%80%EC%9D%B4%EC%8A%A4-%EC%A0%95%EB%A6%AC%ED%95%98%EA%B8%B0  
* https://hamait.tistory.com/1017  


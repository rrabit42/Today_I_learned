# 명령줄 옵션  

프로그램을 만들다보면 실행할 때 옵션 설정하고 싶을 때 있음.  
Go 언어의 main 함수에는 매개변수가 없음. 그래서 명령줄에서 설정한 옵션을 가져오려면 os.Args 슬라이스를 사용. ex) os.Args[0], os.Args[1] 등등  
os.Args의 문자열을 일일히 분석하여 사용하기에는 매우 불편. 그래서 GO 언어에서는 명령줄 옵션을 편리하게 사용할 수 있도록 flag 패키지 제공.  

#### flag 패키지에서 제공하는 함수  
매개변수는 (옵션명, 기본값 설명) 순서, 옵션 값이 저장될 포인터가 리턴된다.  
* func String(name string, value string, usage string) *string: 명령줄 옵션을 받은 뒤 문자열로 저장  
* func Int(name string, value int, usage string) *int: 명령줄 옵션을 받은 뒤 정수로 저장  
* func Float64(name string, value float64, usage string) *float64: 명령줄 옵션을 받은 뒤 실수로 저장  
* func Bool(name string, value bool, usage string) *bool: 명령줄 옵션을 받은 뒤 불로 저장  
* func Parse(): 명령줄 옵션(os.Args)의 내용을 각 자료형별로 분석  
* func NFlag() int: 명령줄 옵션의 개수를 리턴  
* var Usage = func() {}: 명령줄 옵션의 기본 사용법 출력  

flag.Parse() 함수를 실행하면 각 옵션 값 포인터에 명령줄에서 설정한 옵션 값이 저장됨.(모든 플래그가 선언된 후에 반드시 Parse 함수를 호출해야한다.  
실제 옵션값을 얻기 위해서는 포인터를 역참조하면 됨.  


# 출처  
http://pyrasis.com/book/GoForTheReallyImpatient/Unit59  

# GIN Framework  
Gin이란 Go 언어에서 사용되는 웹 프레임워크로 Python과 Flask의 관계와 비슷함. Node로 치면 Express 같은 미들웨어 지향 API를 제공  
지원하는 핵심 기능들은 다음과 같다.  
- Radix tree 기반의 라우팅으로 메모리 소요가 적으며 reflextion이 필요 없어 API 성능이 예측 가능.  
- 미들웨어를 지원하여 개별 HTTP Request에 수행되는 비즈니스 로직을 분리할 수 있음.  
- 요청에 대한 Panic 을 Recover하기 때문에 안정적인 서빙이 가능.  
- 요청의 Json을 Validate 할 수 있음. (와우! 라네요!)  
- Route를 그룹화하여 효율적으로 관리할 수 있으며, nesting이 가능.  
- 미들웨어를 이용해 Error를 편리하게 관리할 수 있음.  

## Gin 웹 서버 만들기  
```
package main

import "github.com/gin-gonic/gin"

func main() {
  r := gin.Default()
  r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{
      "message": "pong",
    })
  })
  r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```  
> ```import "net/http"``` : ```http.StatusOk``` 사용할 경우 import  

```
package main

import (
	"fmt"
	"log"
	"net/http"
	"time"
	"github.com/gin-gonic/gin"
)

func main() {

	// router := gin.Default() // default로 해도 기본적인건 다 된다

	router := gin.New() // 커스텀을 하고 싶으면 이걸로.

	router.Use(gin.LoggerWithFormatter(func(param gin.LogFormatterParams) string { // 커스텀 로그 (아파치에서 출력하는 형식)
		return fmt.Sprintf("%s - [%s] \"%s %s %s %d %s \"%s\" %s\"\n",
			param.ClientIP,
			param.TimeStamp.Format(time.RFC1123),
			param.Method,
			param.Path,
			param.Request.Proto,
			param.StatusCode,
			param.Latency,
			param.Request.UserAgent(),
			param.ErrorMessage,
		)
	}))

	router.Use(gin.Recovery()) // go panic -> recover 하는 미들웨어.

	router.LoadHTMLGlob("view/*") // view 폴더 설정

	// METHOD GET
	router.GET("/", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.html", gin.H{ // Go로 HTMl 자주 뿌리진 않겟지만 쓰려면 이렇게.. view에서 읽을 땐 html 참고, 템플릿 엔진은 핸들바랑 비슷 (.빼고)
			"message": "Golang",
		})

		/*
			http 패키지에 상태를 나타내는 변수 모음 몇가지만.
			http.StatusOK = 200
			http.StatusBadRequest = 400
			http.StatusForbidden = 403
			http.StatusNotFound = 404
			http.StatusInternalServerError = 500
			http.StatusBadGateway = 502
		*/
	})

	// 리디렉션.
	router.GET("/re", func(c *gin.Context) {
		c.Redirect(http.StatusMovedPermanently, "http://localhost:3000/")
	})

	// 라우팅을 그룹으로 묶을 수 있다. 버전별 API 만들 때 좋은듯.
	v1 := router.Group("/v1")
	{
		v1.GET("/path/:param", readPathParam)
		v1.GET("/query", getQueryString)
		v1.POST("/login", loginV1)
	}

	// version v2
	v2 := router.Group("/v2")
	{
		v2.POST("/login", loginV2)
	}

	// router.Run(":3000") // 기본 실행? 안의 인자는 포트번호.

	server := &http.Server{ // http 설정을 커스텀하고 싶으면 요렇게.
		Addr:           ":3000", // port
		Handler:        router,
		ReadTimeout:    10 * time.Second,
		WriteTimeout:   10 * time.Second,
		MaxHeaderBytes: 1 << 20,
	}

	server.ListenAndServe() // http config 설정 후 기동.
}

/*
	METHOD GET, URL path를 파라미터로 쓰는 경우.
	특이하게 path param이 비어 있으면 자동으로 403으로 응답한다. 예외 처리를 안해도 됨.
*/
func readPathParam(c *gin.Context) {
	param := c.Param("param")

	c.JSON(http.StatusOK, gin.H{
		"code":    200,
		"message": param,
	})
}

// 쿼리스트링
func getQueryString(c *gin.Context) {
	id := c.Query("id")                           // 원하는 쿼리스트링 값 읽기
	isGuest := c.DefaultQuery("isGuest", "false") // 쿼리스트링이 비엇을 시 기본 값 대체하는 법 따로 조건처리 안해도 되는군..

	if len(id) == 0 { // 문자열로 들어오기 땜에 nil과 비교가 안된다.. 길이로 비교해야 함..
		c.JSON(http.StatusBadRequest, gin.H{
			"code":    403,
			"message": "parameter check",
		})
		return // return을 명시한 이유는 여기서 멈추기 위해.. 안 그러면 아래 응답을 이어서 보내버린다.. 나머지 return도 마찬가지.
	}

	data := map[string]interface{}{
		"id":      id,
		"isGuest": isGuest,
	}

	c.JSON(http.StatusOK, gin.H{
		"code":    200,
		"message": "success",
		"data":    data,
	})

}

// User - 구조체
type User struct {
	ID    string `json:"id" binding:"required"`
	Name  string `json:"name" binding:"required"`
	Phone string `json:"phone"`
}

// User2 - 구조체
type User2 struct {
	ID    string `json:"id"`
	Name  string `json:"name"`
	Phone string `json:"phone"`
}

// METHOD POST 인 경우.
func loginV1(c *gin.Context) {
	var params User // 위에 선언한 Test 구조체임.

	if err := c.ShouldBindJSON(&params); err != nil { // 바인딩할 떄 required에 빈 값이 드러온 경우.
		c.JSON(http.StatusBadRequest, gin.H{
			"code":    403,
			"message": "parameter check",
		})

		log.Print(err.Error())

		return
	}

	fmt.Println("params", params)

	c.JSON(http.StatusOK, gin.H{
		"code":    200,
		"message": "success",
		"version": "v1",
	})
}

func loginV2(c *gin.Context) {
	var params User2 // 위에 선언한 Test 구조체임.

	c.BindJSON(&params) // 바인딩을 안할 시 body json 받는 법.

	fmt.Println("params", params)

	c.JSON(http.StatusOK, gin.H{
		"code":    200,
		"message": "success",
		"version": "v2",
	})
}
```  

## Gin 이해하기  
### 1. Gin 엔진 만들기  
1) logger & recovery 사용할 경우  
Default 사용하면 logger와 recovery를 사용하게 된다.  
```r := gin.Default()```  

2) logger & recovery 사용하지 않을 경우  
logger와 recovery를 사용하지 않을 경우 New로 생성한다.  
```r := gin.New()```  

> recovery: go panic을 recover하는 미들웨어(Recovery middleware recovers from any panics and writes a 500 if there was one.)
> logger: GIN_MODE=release로 하더라도 Logger 미들웨어는 gin.DefaultWriter에 로그를 기록한다. 기본값은 gin.Default(Writer = os.Stdout(Logger middleware will write the logs to gin.DefaultWriter even if you set with GIN_MODE=release. By default gin.DefaultWriter = os.Stdout)  

### 2. Routing 설정  
1) URL 바인딩  
```
r.GET("/url/path", handleFunc)
r.POST("/url/path", handleFunc)
r.HEAD("/url/path", handleFunc)
r.OPTIONS("/url/path", handleFunc)
r.PUT("/url/path", handleFunc)
r.DELETE("/url/path", handleFunc)
r.Handle("GET", "/url/path", handleFunc)
```  

2) Parameter 추가  
```
r.GET("/user/:id", handleFunc ) // ----> /user/1, /user/2, ...
r.GET("/user/:id/*action", handleFunc ) // ----> /user/1/info, user/1/info/age, ...
r.GET("/user/groups", handleFunc ) // ----> /user/groups
```  

### 3. 핸들러 함수  
```
package main

import (
  "github.com/gin-gonic/gin"
)

func main() {

   r := gin.Default()

   r.GET("/ping", handleFunc)
 
   r.Run(":5000")
}
// 핸들러 함수
func handleFunc(c *gin.Context) {
  c.JSON(200, gin.H{
    "message": "pong",
  })
}

// 리턴 값 없는 핸들러 함수
// func handleFunc(c *gin.Context) {
//  return
// }
```  

**익명함수**  
```
package main

import (
  "github.com/gin-gonic/gin"
)

func main() {
  r := gin.Default()

  r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{
      "message": "pong",
    })
  })

  r.Run(":5000")
}
```

## 출처  
https://www.saichoiblog.com/go-gin-framework/  
https://hidelryn.github.io/2019/03/01/go-study-gin/  
https://riverandeye.tistory.com/entry/3-Go-Gin-%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC  





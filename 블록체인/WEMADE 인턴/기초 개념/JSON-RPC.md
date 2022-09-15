[출처](https://itdar.tistory.com/395)  

# JSON-RPC  
RPC, 근데 이제 Json을 곁들인.. (Json-RPC)  

* RPC 란 무엇인가?  
* Json 이란 무엇인가?  
* RPC 가 사용되는 이유  
* RPC 동작 방식  
* Json-RPC 특징  
* 추가: gRPC는?  
* HTTP 위에 얹는 Json-RPC 스타일  

## RPC 란 무엇인가?  
**Remote Procedure Call (원격 프로시져 호출)**  
언어나 환경 등에 종속되지 않고 서비스 간 프로시저를 호출하도록 해주는 개념  

* 분산컴퓨팅 환경에서 많이 사용됨
* 프로세스 간 통신 (Inter-Process Communication) 의 한 형태
* 클라이언트-서버 관계로 요청-응답 시스템
* 클라이언트에서 서버로 필요한 파라미터를 보내어 프로시저를 동작시킨다.

## Json 이란 무엇인가?
JavaScript Object Notation

* 데이터 교환 형식의 포맷
* Key / Value 쌍으로 콜렉션 타입으로 구성됨
```
{
  "widget": {
    "debug": "on",
    "window": {
      "title": "Sample Konfabulator Widget",
      "name": "main_window",
      "width": 500,
      "height": 500
    },
    "image": {
      "src": "Images/Sun.png",
      "name": "sun1",
      "hOffset": 250,
      "vOffset": 250,
      "alignment": "center"
    },
    "text": {
      "data": "Click Here",
      "size": 36,
      "style": "bold",
      "name": "text1",
      "hOffset": 250,
      "vOffset": 100,
      "alignment": "center",
      "onMouseUp": "sun1.opacity = (sun1.opacity / 100) * 90;"
    }
  }
}
```

## RPC 가 사용되는 이유

* 로컬/리모트 환경에 관계없이 똑같이 코딩 할 수 있음
* 환경에 구애받지 않고 동일 코딩으로 동일 프로시저 동작 시킬 수 있음
* HTTP 는 어플리케이션 레이어 (7계층) 에서 동작하는 것과 다르게, RPC 는 TCP (4계층) 위에서 동작한다.
따라서 더 다양한 프로토콜 사용 가능하다 (TCP, Socket, HTTP 등..)  
CRUD 외에 다양한 동작 표현 (동일한 엔드포인트 호출로 메서드와 파라미터만 바꿔서)  

## RPC 동작방식
![image](https://user-images.githubusercontent.com/46364778/190297967-8366e2a3-b95f-46ec-a7c5-bb0cd5cc870a.png)


* 클라이언트는 클라이언트 Stub 을 호출한다.  
스택에 매개변수를 푸시하는 로컬 프로시저 콜

* 클라이언트 Stub은 메시지로 매개변수를 패킹하고 메시지를 전송하는 시스템 호출.  
이 때, 매개변수 패킹하는 것을 마샬링(Marshalling)이라 한다.

* 클라이언트 로컬 OS 는 클라이언트에서 서버로 메시지 보낸다.
* 서버 로컬 OS 는 들어오는 패킷을 서버 Stub 으로 보낸다.
* 서버 Stub 은 메시지에서 매개변수 압축을 언패킹 한다.  
이 때, 매개변수 압축 푸는 것을 언마샬링(Unmarshalling) 이라 한다.
* 서버 Stub 은 서버 프로시저를 호출한다.
* 응답과 반환패킷은 반대 방향으로 동일한 단계를 거친다.

## Json-RPC 특징  
* RPC 를 Json 포맷으로 표현한 것
* 2.0부터 Request-Response 뿐만 아니라 응답없는 Notification도 지원한다.
* 다수의 호출이 서버로 전송되고 순서 없이 응답되는 것을 허용한다.  
* 4계층 TCP 에서 동작하여 Http 프로토콜을 얹을 필요없이, Json 만 전달하여 동작 가능하다.  
더 가볍고 빠르다.

## + gRPC
구글에서 만든 gRPC 를 많이들 쓰는 것으로 보임  

* protobuf 통해서 binary 로 마샬링 (더 빠름)
* IDL 통해서 여러 언어로 제너레이팅 가능
* HTTP 2.0 지원

## HTTP 위에 얹는 Json-RPC 스타일
* HTTP 프로토콜을 사용하되, 요청을 1개로 통일
* POST 의 Body 등으로 json 을 넘기는데, 이 때 JsonRPC 포맷을 따른다. (id, method, params)
* 이렇게 하면.. 컨트롤러 1개로 모든 요청을 받고, 메서드를 호출해서 응답을 할 수 있다.
* 간단한 구현은 편해서 좋은점도 있다고 하는데.. 흠?


## Reference
https://en.wikipedia.org/wiki/Remote_procedure_call  
https://en.wikipedia.org/wiki/JSON-RPC  
https://www.jsonrpc.org/  
https://www.json.org/json-en.html  
https://www.geeksforgeeks.org  

- - -

# 왜 JSON-RPC를 사용할까?  
[출처](https://www.getoutsidedoor.com/2019/08/10/%EC%99%9C-json-rpc%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C/)  

## Intro
최근에 web3.js 코드를 살펴볼 일이 있었는데 이 때 JSON-RPC를 처음 접했다. 그리고 JSON-RPC 공식 홈페이지에서 JSON-RPC 2.0 스펙을 보았는데 가장 먼저 든 생각은 ‘왜 이걸 쓸까?’ 였다. 그래서 이번 포스트에서는 JSON-RPC가 어떤 것이고 어떤 장점이 있는지 정리하려고 한다.  
우선 JSON-RPC가 등장한 시기를 살펴보면 2000년을 시작으로 REST 방식이 등장하고 2000년대 중순에 JSON-RPC 등장했고 마지막으로 2015년에 gRPC가 나왔다.  
RPC와 gRPC에 대해서 궁금하다면 [여기](https://www.getoutsidedoor.com/2019/07/11/what-is-grpc/)서 살펴볼 수 있다.  
여러 글들에서 과거엔 어떤 서비스를 만들 때 가장 표준이 되었던 REST 방식을 사용했지만 최근에는 JSON-RPC도 많이 사용하고 있다고 한다.  
그렇다면 JSON-RPC와 REST을 비교했을 때 JSON-RPC는 어떤 특징을 가지고 있을까?  

## REST vs JSON-RPC
### TCP 위에서 동작한다
REST는 HTTP(S) 프로토콜 위에서 동작하는 표준인 반면에 JSON-RPC는 TCP 위에서 동작하는 표준이기 때문에 좀 더 다양한 프로토콜에서 사용할 수 있다.  
web3에서도 클라이언트와 서버가 웹소켓을 통해서 통신을 하는데 여기서 JSON-RPC를 사용할 수 있는 이유는 JSON-RPC가 HTTP 프로토콜뿐만아니라 TCP 위에서 동작할 수 있기 때문이다.  
또한 REST는 다른 리소스를 얻기 위해서 다른 URL에 접근해야하는 반면에 JSON-RPC는 하나의 엔드포인트 URL에서 모든 요청과 응답을 받는다.  
그리고 REST는 요청을 보낼 때 여러 HTTP Method를 통해서 보내는 반면에 JSON-RPC는 (HTTP의 경우) 하나의 Method를 통해서 통신하게 된다.  

## 다양한 action을 나타낼 수 있다.  
REST는 JSON-RPC에 비해 표현할 수 있는 operation의 범위가 비교적 한정적이다. 왜냐하면 REST 방식은 어떤 객체에 대해서 CRUD operation을 하는 것에 적합하게 설계되었고 반면에 JSON-RPC는 CRUD를 포함한 다양한 action을 나타내는 operation을 표현할 수 있다.  
조금 추상적이니 예를 들어보자. 우리가 차를 얼마의 기간동안 렌트하려고 할 때 그 비용을 API를 통해 구하고 싶다. REST API를 설계하는 입장에서 우리는 어떤 URL을 뽑아내야할까?  
몇 가지를 뽑아보면 일단 GET /car/rent 이 떠올랐을 수도 있다. 그런데 GET /car/rent 는 느낌이 이 API를 호출하면 rent라는 객체가 반환될 거 같은 느낌이다. 그리고 이후에 POST를 통해 rent를 추가할 수 있을 거 같기도 하다. POST /car/calculate_rent 가 떠올랐을 수도 있다.  
하지만 POST /car/calculate_rent 는 URL이 명사로 이루어져있지 않기 때문에 RESTful 방식이 아니다.  
또 다른 상황으로 주문을 검증하는 API를 만들어야한다고 생각해보자. GET /order/validate 가 그럴듯해보이지만 이는 마찬가지로 명사가 아니기 때문에 REST 방식이 아니다.  
이처럼 REST 방식은 CRUD 기능을 나타내는데 적합하기 때문에 구체적인 동작을 나타내기에는 쉽지 않다.  
반면에 위와 같은 API들을 JSON-RPC 방식으로 구현한다고 생각했을 때 각각의 메소드들은 다음과 같이 표현할 수 있다.  
> car.rent.calculate_fee: 차를 렌트하는데 요금을 계산하는 메소드
> car.rent.create: 차를 렌트하는 메소드
> order.validate: 주문에 대해서 검증하는 메소드

## 통일된 parameter 전달방식  
REST가 JSON-RPC에 비해 번거로운 점 중 하나는 parameter를 전달하는 방법이 다양해서 이것을 핸들링할 수 있는 방법도 다양하다는 것이다.  
일반적으로 GET method에 쿼리 조건을 붙이기 위해서 querystring을 붙이는 방법이 있고 POST로 데이터를 추가할 때는 보통 body에 데이터를 실어서 보낸다. PUT, DELETE에는 특정 객체의 상태를 바꾸기 위해서 URL에 id나 다른 인자를 붙여서 보낸다.  
반면에 JSON-RPC는 parameter를 전달하는 방법이 한가지 밖에 없다. HTTP의 경우 body에 데이터를 싣는 방법이다. 핸들링하는 코드도 훨씬 단순해질 것이고 구조도 단순해질 수 있다.  

## 심플하다
마지막으로 JSON-RPC에 대해서 찾아보던 중 인상깊은 글이 있어서 가져왔다.  

> I’ve been a big fan of REST in the past and it has many advantages over RPC on paper. You can present the client with different Content-Types, Caching, reuse of HTTP status codes, you can guide the client through the API and you can embed documentation in the API if it isn’t mostly self-explaining anyway.  
> But my experience has been that in practice this doesn’t hold up and instead you do a lot of unnecessary work to get everything right. Also the HTTP status codes often don’t map to your domain logic exactly and using them in your context often feels a bit forced.  
> But the worst thing about REST in my opinion is that you spend a lot of time to design your resources and the interactions they allow.  
> And whenever you do some major additions to your API you hope you find a good solution to add the new functionality and you didn’t design yourself into a corner already.  
> This often feels like a waste of time to me because most of the time I already have a perfectly fine and obvious idea about how to model an API as a set of remote procedure calls. … Our programs are based on calling procedures so building a good RPC client library is easy, …  
> https://stackoverflow.com/a/37823128/6433772

이처럼 JSON-RPC와 달리 REST에서는 한정된 operation으로 원하는 서비스를 제공해야하므로 불필요한 모델들을 추가하고 ‘어떻게 CRUD 내에서 기능을 제공할 수 있을까’에 대해서 고민해야한다. 그리고 새로운 기능을 추가할 때 그 기능에 대해서 기존의 REST API와 어떻게 하면 seamless하게 가져갈 수 있는지 고민도 필요할 것이다.  
JSON-RPC는 action-based operation에 적합하기 때문에 내가 도메인 모델을 정해놓고 나면 그에 대한 행동을 묘사하는 메소드를 뽑아내는 것은 훨씬 간단한 일이다. 더불어 HTTP의 경우 HTTP에서 기본적으로 표준을 정해놓은 Status code를 RPC의 응답값과 힘들게 매핑시킬필요도 없다.  

## Reference
https://blog.thriftly.io/know-your-api-protocols
https://joost.vunderink.net/blog/2016/01/03/why-we-chose-json-rpc-over-rest/
https://www.simple-is-better.org/rpc/
https://www.radixdlt.com/post/json-rpc-vs-rest/
https://stackoverflow.com/questions/15056878/rest-vs-json-rpc

# Web3.js  
Ethereum 네트워크는 여러 사용자(EOA)들이 연결되어 있음.  
또한 특정 역할을 수행하는 smart contract들로 구성되어 있음.  
우리가 이 smart contract를 실행하기 위해서는 아래의 내용들을 전송해야함.  


스마트 컨트랙트의 주소
실행하고자 하는 함수, 그리고
그 함수에 전달하고자 하는 변수들
이더리움 노드들은 JSON-RPC라고 불리는 언어로만 소통할 수 있기 때문에, 이는 일반적으로 사람들이 읽고 분석하기에는 굉장히 어려움.  

예시를 보여드리겠습니다.  

```
//JSON_RPC example
{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{"from":"0xa89e8ea54c3d12ba8054cbcab988870e02923434","to":"0xd46e8dd67c5d32be8058bb8eb970870f02335324","gas":"0x76c0","gasPrice":"0x9184e72a000","value":"0x9184e72a","data":"0xd46e8dd67c5d32be8d46e8dd67c5d32be8c23e54eb970870f072445675058bb8eb970870f072445675"}],"id":1}
```  

우리가 DApp을 만들기 위해서는 해당 서비스를 이용하는 사람들간의 smart contract를 통해 거래, 혹은 ether 획득 등을 handling해야하는데 JSON-RPC를 통해 모든 것을 구현하기에는 어려움이 따름.  
그래서 Web3 js가 등장. JavaScript 기반 모듈. (Web3 j라고 Java 기반도 존재)  
DApp 생태계를 그림으로 표현하면 다음과 같습니다.  
![image](https://user-images.githubusercontent.com/46364778/197669872-de6955c6-7f4c-44ff-9f99-2853909c3fbc.png)  

이더리움 블록체인 네트워크와 통신하기 위해서 MetaMask, Infura라는 인터페이스가 존재하고 DAPP을 개발하는 개발자들은 Web3 API를 사용하여 웹과 모바일을 개발할 수 있음.  
이 Web3 js를 사용하여 infura에 연결하고, 특정 address의 0.2ether를 또다른 address로 송금하는 코드를 작성해보고자 한다. (Ropsten test net을 활용할께요.)  



## 출처  
https://velog.io/@kysung95/BlockChain-Web-3-js%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EC%9E%90  


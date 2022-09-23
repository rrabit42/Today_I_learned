# Sharmir's secret sharing scheme  
인턴업무 중에 SSS 암호 기법이 나와 공부해보고자 한다.  
> keystore 를 생성할 때 Samir's Secret Sharing(이하 SSS)을 이용하여  
> 키스토어의 passphrase 조각화하여 여러명의 사용자가 지정한 정족수 이상 참여하여만 Unlock을 해제할 수 있는 보안이 강화된 keystore를 생성하는 기능을 지원.  

즉, 반드시 정해진 인원이 모여야지 비밀을 확인할 수 있도록 설계된 수학 모델.  

블록체인에서 개인키를 보관할 때, 하나의 키를 나눠서 관리하는 방식 등으로 사용 가능(하나는 서버, 하나는 오프라인, 하나는 신뢰가능한 제3자 등으로 나눠서 관리할 수 있다.)  
하지만 SSS 방식의 경우, 무조건 한번은 개인키가 노출 되는데(Share를 모아서 합치는 경우에) 결국 single-point-of-failure가 존재한다.  
이 single-point-of-failure를 없애기 위해 TSS(Threshold Signature Scheme) 방식을 적용시킬 수 있다.  

## 기본 골격  
Secret Sharing의 기본 구조  
![image](https://user-images.githubusercontent.com/46364778/191919936-b919321a-8442-41c9-ade1-bd17014b0be6.png)  
1. Secret을 나눠가질 구성원이 n명이라고 할 때, f1,...,fn 즉 n개의 함수와 s' = f(s)인 s'인 s'을 각각 구성원에게 배부한다. (s는 공융하는 비밀)  
2. 구성원 모두가 모여야지만 f의 역원을 활용해서 s를 도출할 수 있도록 설계한다.  

각 Secret Sharing은 어느 정도의 인원이 모여야 비밀을 알 수 있을지에 대한 인원수를 정의할 수 있는데,
이러한 Scheme을 표현하기 위해 **(t,n)-Threshold Scheme** 이라는 표현을 쓴다.  
이때 t는 Threshold 인원수, n은 전체 구성원 수  

즉, 비밀 정보 s를 n개의 조각 혹은 지분(share)으로 나누는데,  
n보다 작거나 같은 t개의 share로 s를 복원할 수 있다.  

## (t, n) sharing scheme  
이스라엘 수학자 Adi Shamir 은 RSA를 설계한 인물이기도 하다.  
이 Shamir 가 1979년 논문(( How to share a secret", Communications of the ACM, 22 )) 으로 제시한 이론이며, 가장 대중화 되어있는 Secret Sharing Scheme 이다.  
이 Shamir's Secret Sharing Theme 은 굉장히 강력하며, 이론적으로 Secure 하다고 알려져있다.  

## 사전 지식  
2차 다항식 𝑓(𝑥)=𝑎𝑥^2+𝑏𝑥+𝑐의 계수와 상수를 모두 구해서 본래의 2차 다항식을 찾으려면?  
다항식을 지나는 점들이 필요 (ex.  (1,𝑓(1)), (2,𝑓(2)), …)  
방법: 연립방정식, 소거법  
> 𝑓(1)=𝑎+𝑏+𝑐  
> 𝑓(2)=4𝑎+2𝑏+𝑐  
> 𝑓(3)=9𝑎+3𝑏+𝑐  

계산은 생략…  
구해야 할 계수가 a, b, c 3개이기 때문에 3개의 점이 필요함  
-> 임의의 2차 다항식의 계수와 상수를 모두 구해서 본래의 2차 다항식을 찾으려면 3개의 점이 필요함  

일반화: **n차 다항식의 계수를 모두 알아내려면 n+1개의 점이 필요함**  

앞서 말한 “n차 다항식의 계수와 상수를 모두 알아내려면 n+1개의 점이 필요함”을 기본개념에 맞춰 표현하면,  
-> **k-1차 다항식의 계수와 상수를 모두 알아내려면 k개의 점이 필요함**  

개념 확인 : 비밀정보 S를 n개의 share로 나누고, k개의 share가 모이면 S를 알아낼 수 있음  
조금 변형 : S를 n개의 ‘점’으로 나누고, k개의 ‘점’을 이용하면 S를 알아낼 수 있음  
다시 강조 : k-1차 다항식의 계수와 상수를 모두 알아내려면 k개의 점이 필요함  
상수항을 S로 가지는 임의의 k-1차 다항식을 생성하면, k개의 점을 이용해서 상수 S를 구할 수 있음  

 
### 1. Secret Distribution  
우선 Secret을 배부해줄 딜러를 선정  
Public Parameter:
- p: n+1<p인 소수 p  
- x1,...,xn: n개의 값을 Zp에서 고른다. 그리고 각각의 x들을 n명에게 배부한다.(Zp는 p-1 이하의 자연수)  
- a1,...,a(t-1): t-1개의 값을 Zp에서 고른다. 이 값들을 딜러만이 알고 있는다.  

#### Secret Sharing Function  

![image](https://user-images.githubusercontent.com/46364778/191921158-d584a151-abac-47b8-9885-fd11be7a6421.png)  
 
위 식에서 yi = a(xi)인 yi 값들을 대응하는 각각 n명에게 배부한다.  

> Q. 이 때 딜러가 선택한 xi가 0이면 어떻게 될까?  
> A. 당연히 안된다. 그렇게 되면 0을 받은 i번째 사람은 비밀 s를 p로 나눈 나머지를 알게 되므로, 노가다로 s를 쉽게 얻을 수 있다. 그래서 0이 아닌 xi를 배부해야 한다.  


### 2. Secret working  
1) 방데르몽드 행렬(Vandermonde matrix)  
배부 후에, 구성원 n명은 모두 각각 해당하는 xi와 yi을 받게 되었다.  
명심할 것은 반드시 딜러만이 함수의 계수(ai)들을 알고 있다는 것.  

이제 비밀을 풀기 위해서 구성원 중 t명이 모였다고 가정하자.  
각각 구성원이 가지고 있는 x값으로 아래 행렬을 만들 수 있는데, 이런 행렬을 **방데르몽드(Vandermonde) 행렬**이라고 하며, 역행렬이 존재하는 행렬이다.  
![image](https://user-images.githubusercontent.com/46364778/191921786-254a0d69-4e5b-449b-85a3-a51551417140.png)  

위 방데르몽드 행렬을 만들고 아래 방정식을 세우면 이제 비밀 s를 풀 수 있는 기미가 보이기 시작한다.  
![image](https://user-images.githubusercontent.com/46364778/191922088-b86ba044-9f40-409d-8d4a-fdef768c11d6.png)  

방데르몽드 행렬의 역행렬을 양변에 곱하면, 주어진 방정식을 풀이함으로써 s를 구할 수 있다.  

2) 라그링주 보간법(Langrange interpolation)  
* 연립 방적식을 풀지 않고 다항식 결정  
* 계산 시간이 비교적 짧고, 간단하며 자료의 구간에 관계없이 사용 가능  

앞서 연립방정식을 사용했던 방법에서는 보간 다항식을  
> 𝑔(𝑥)=𝑎0+𝑎1𝑥+𝑎2𝑥+…+𝑎(𝑘−1)𝑥^(𝑘−1)  
와 같이 정의하여 사용했지만,

라그랑주라는 사람은 보간 다항식을  
<img width="204" alt="image" src="https://user-images.githubusercontent.com/46364778/191926189-f73b3516-a21f-4e4c-b222-717acf868947.png">  

와 같이 정의함  
여기서 𝑙𝑗(𝑥)는 Lagrange basis polynomial(라그랑주 기초 다항식)이라고 하고, 다음과 같이 정의됨  
<img width="241" alt="image" src="https://user-images.githubusercontent.com/46364778/191926231-57356fd9-c01b-4f62-bee7-63b59e71f62d.png">  

<img width="1083" alt="image" src="https://user-images.githubusercontent.com/46364778/191926479-80e847bf-0c0f-4db1-8a07-d43daadbbdf1.png">  

<img width="1082" alt="image" src="https://user-images.githubusercontent.com/46364778/191926509-e79718dc-0a86-4307-891e-14716fcf48a6.png">  


### 3. Secret sharing prohibition  
그렇다면 t-1명이 모였을 때, s를 구할 수 없음을 보여보자.  
마찬가지로 아래 행렬을 만들어서 상황을 표현할 수 있다.  
![image](https://user-images.githubusercontent.com/46364778/191922542-0ff19e58-2956-4131-9d45-f0076621ba5c.png)  

참고로 위 행렬은 정사각 행렬이 아니기 때문에 역행렬을 구할 수 없고 따라서 방데르몽드 행렬이 아니다.  
그러면 식을 조작해서 우변을 방데르몽드 행렬의 형태로 만들어서 해결해보자.  
![image](https://user-images.githubusercontent.com/46364778/191922661-9ad3143a-0df8-4d39-bd08-3d511246b24c.png)  

![image](https://user-images.githubusercontent.com/46364778/191922739-ccb2ab6a-4175-47c3-85d5-a6651cc63cfe.png)  

![image](https://user-images.githubusercontent.com/46364778/191922769-26d12a16-e6a0-471b-825b-4cfa68a48012.png)  

B의 역행렬을 양변에 곱하면 연립방정식을 도출할 수 있다.  
하지만 여기서 우리가 구해야하는 것은 s다.  
모든 a의 값들이 0이 아니기 때문에, s값에 따라 a값들이 변한다는 것을 알 수 있다. 따라서 여기서는 s를 구할 수 없다.  

이렇게 Shamir's Secret Sharing Scheme의 동작을 확인할 수 있다.  
반드시 정해진 t명 이상이 모여야지만 비밀 s를 확인할 수 있었다.  

#### 추가  
앞서 SSS을 다시 상기해보면, s가 상수항으로 있는 것을 알 수 있다.  
꼭 상수항에 s가 있어야만 할까? s가 굳이 어느 계수로 있던지 상관 없는 것 아닌가?  
=> 정답은, s는 상수항 혹은 최고차항의 계수로만 존재할 수 있다.  
우선 위에서 B행렬로 보였듯이, t-1명이 모였을 때 s를 구할 수 없어야 한다.  

* s가 일반항의 계수로 있을 때  
![image](https://user-images.githubusercontent.com/46364778/191923424-75c344d1-3b65-48dc-bef1-13eb50a949c7.png)  

* s가 최고차항의 계수로 있을 때  

## [참고] Chinese Remainer Theorem  
중국인의 나머지 정리로도 (n, n) Secret Sharing을 해볼 수 있다.  
> 중국인의 나머지 정리(CRT): 3으로 나누었을 떄 2가 남고, 5로 나누었을 때 3이 남고, 7로 나누었을 때 2가 남는 수는?  
![image](https://user-images.githubusercontent.com/46364778/191924091-13bdc0a6-acee-4ff9-8ffe-fe0cd7261d18.png)  

> k개의 합동식으로 이루어진 아래 연립합동식이 주어졌고, n1~nk에서 어떤 쌍을 뽑아도 서로소일 때, 즉 N = n1 * n2 * ... * nk가 이들의 최소공배수일 때,  
> 0이상 N미만의 정수 중에 이 연립합동식의 해가 정확히 한 개 존재한다.  

이 정리를 활용해서 구성원 각각에게 숫자 n 과 비밀 s를 n 으로 나눈 나머지 r, 즉 (n,r) 을 나누어 준다.  
아이디어에 따르면, 모든 구성원들이 모였을때, 중국인의 나머지 정리를 통해서 s를 복원할 수 있다는 것이다.  
참고로 중국인의 나머지 정리는 위의 예시에서는  
x= mod5 part + mod7 part + mod3 part  로 나누어서 계산할 수 있다.  
하지만 n명이 굳이 모두 모이지 않더라도 나머지를 이용한 힌트를 얻을 수 있기때문에, 완벽하지 않은 Secret Sharing 이다.  


## 출처  
https://sidneywl2018.tistory.com/53  
https://m.blog.naver.com/kks227/221635322468  

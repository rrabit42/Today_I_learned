# Klaytn 클레이튼 스마트계약과 탈중앙앱  

교육 자료: [Klaytn 클레이튼 스마트계약과 탈중앙앱](https://www.inflearn.com/course/klaytn-%EC%8A%A4%EB%A7%88%ED%8A%B8%EA%B3%84%EC%95%BD%EA%B3%BC-%ED%83%88%EC%A4%91%EC%95%99%EC%95%B1#curriculum)  


## 0. 블록체인 기본  

- **블록체인**이란? 정보를 **블록**이라고 하는 단위로 젖아하여 저장된 블록들을 **체인**형태로 묶은 저장 기술  
데이터들은 일정한 순서로 정렬되어 묶음으로 저장되는데 이를 '블록'이라고 함. 첫번째 블록(Genesis Block)에서 시작되어 블록들은 서로 체인으로 연결되고 이 순서는 유지가 됨.  
Linked List와 비슷한 형태지만 앞의 블록을 기억하는 방법이 다름(해시 함수 사용). 

- **블록, 블록헤더, 해시포인터**?  
자료구조: 누가 누굴 기억하느냐가 중요(뒤가 앞을 기억? 앞을 뒤가 기억?), 메모리 주소에 데이터를 매핑하므로 메모리 주소를 기억  
블록은 헤더와 바디로 구분. 헤더는 블록을 설명하는 정보와 이전 블록의 해시 포함. 이전 블록의 해시(hash pointer)를 가지기 때문에  
(1) 어떤 블록이 앞에 와야하는지 결정적으로 알 수 있고 (2) 이를 바탕으로 블록의 순서를 결정할 수 있음  
바디는 정보의 묶음. 이 데이터를 설명해주는 헤더(이 묶여진 일련의 데이터들을 어떻게 검증해야하는지, 검증에 무엇이 필요한지 등의 정보)  

- **블록높이, 블록생성주기**?  
블록체인을 처음에 구상한 사람들은 수직으로 0번이 바닥에 깔리고 그 위로 쌓이고, 0번을 빼버리면 모든게 흔들려 버린다고 생각. 그래서 차곡차곡 쌓아 올린다고 생각. -> 높이 개념 등장  
블록들을 이전 블록이 아래에 최근 블록이 위로 오도록 정렬하면 블록이 생성됨에 따라 체인의 높이가 늘어난다. 블록의 순서를 그 블록이 위치한 '높이'(block height)라 부른다. 첫번째 블록은 편의상 높이를 0이라 한다.  
다음 블록을 생성하기까지 걸리는 시간을 '블록생성시간'이라 하고, 블록생성시간이 비교적 일정한 경우 '블록생성주기'란 표현을 사용. (대부분은 주기마다 만듦, 비트코인은 10분, 이더리움 15초 정도에 수렴, 클레이튼 1초)  
블록생성주기는 아주 중요하다. 데이터가 블록에 들어가게 되는걸 '체결'. 체결시간이 짧을 수록 서비스에 적합하기 때문. (클레이튼은 서비스 지향적인 블록체인)  

- **블록체인 네트워크, 노드**?  
보통 우리가 아는 Request-Response(Client-Server) 구조. 연산력이 부족하거나 컨텐츠가 부족한 클라이언트가 반대로 연산 능력이 충분하거나 콘텐츠가 충분한 서버에게 요청하고 응답을 받음.  
그러나, 블록체인은 기본적으로 P2P 네트워크(Peer-to-Peer Network) ex) 토렌트, 소리바다(누구 한명에게 의존하는 구조 X. 나와 같은 데이터를 가진 다른 사람이 그 데이터를 줄 수 있음. 빠진 데이터 채우기 -> 탈중앙화)  
블록체인은 한명 이상의 참여자(노드)가 있는 네트워크에서 관리.  
네트워크 참여자 전원은 모든 블록을 동일한 순서로 저장하여 모두 같은 블록체인을 유지.  

- **합의(Consensus)**?  
중앙이 원장을 가지고 있고, 우리는 모두 중앙을 믿고 의존함. ex) Alice의 잔고가 실제로 100불인거 맞아? 중앙은행한테 물어보고 은행이 검증해줌. 은행이 모든 사람들의 돈이 적힌 원장을 가지고 있으니까.  
블록체인은 모두가 원장을 가지고 있고, 모두가 기억을 할 수 있으므로 모두가 검증을 할 수 있음. 그러므로 모두의 합의가 필요한 상황이 있음 ex) Alice가 50불을 Bob에게 보낼 수 있는지 없는지 맞아?  
자격이 있는 참여자는 블록을 제안(propse)할 수 있음: 제안자(Proposer)  
블록 제안 자격은 네트워크마다 상이 (e.g., PoW: 제안자의 익명성은 지켜지지만 제안자의 work을 통해 올바른 참여자라고 생각하여 그 블록을 받아들여줌)  
노드들은 제안자가 올바른 자격을 취득했는지, 제안된 블록이 올바른지 **검증** 뒤 블록을 자신의 체인에 추가. ex) 나는 Alice가 50불을 보낼 수 있는게 맞는 것 같아.  
정족수 또는 정해진 기준을 만족하는 수의 노드가 블록을 자신의 체인에 추가하면 합의가 이뤄졌다고 판단.  ex) 누군가가 이 데이터를 다음 블록이라고 하자 -> 나는 그 액션에 동의해(이 과정이 합의)  
그러나 합의가 어려운 이유: **[비잔틴 장군 문제](http://wiki.hash.kr/index.php/%EB%B9%84%EC%9E%94%ED%8B%B4_%EC%9E%A5%EC%95%A0_%ED%97%88%EC%9A%A9)**  


> 정리: 블록체인의 성질
> - 블록체인은 한명 이상의 참여자가 있는 네트워크에서 관리
> - 네트워크 참여자 전원은 모든 블록을 동일한 순서로 저장하여 모두 같은 블록체인 유지
> - 자격이 있는 참여자는 블록을 제안할 수 있음; 블록 제안 자격은 네트워크마다 상이
> - 블록이 체인에 추가됨 = 참여자들이 새 블록을 자신의 체인에 추가
> - 따라서 새로운 블록이 체인에 추가되려면 네트워크의 합의가 필요; 합의방법은 네트워크마다 상이  
> (어느 한 주체가 단독으로 결정하는 구조가 아닌, 여러 참여자가 합의를 통해 결정하기 때문에 블록체인은 탈중앙화되어 있다고 표현)  
> - 참여자 전원은 이전 블록들을 저장하고 있으므로 새로운 블록의 무결성을 확인 가능  
> - 새롭게 제안되는 블록은 참여자들이 검증 및 합의할 수 있는 형태여야 함(투명성)  -> 그럼 모두가 이전 기록을 알 고 있어야 하니까 투명하다!  
> - 한번 쓰여진 블록은 이전의 합의를 번복할 수 있지 않는 한 변경될 수 ~~없음~~(불변성) -> 거의 불가능하다. 정말 많은 사람이 알고 있으니 변경이 힘들다!  

- - -

### **합의 알고리즘**  

구분|PoW|PoS|BFT-variants
---|---|---|---
제안자격 취득 방법|계산이 어려운 문제를 풀 것|플랫폼 토큰을 보유한 양과 기간에 따라 결정적으로 또는 확률적으로 뽑힐 것|정해진 순번 또는 정해진 확률에 의해 뽑힐 것
네트워크 참여 제한|없음|없거나 낮음|높음
합의에 필요한 연산량|높음|낮음|낮음
위협|전체 연산량의 51%를 한 참여자가 소유할 경우 중앙화됨|전체 토큰의 51%를 한 참여자가 소유할 경우 중앙화 됨|전체 참여노드의 1/3 이상이 담합할 경우 합의 불가, 전체 참여노드의 2/3 이상이 담합할 경우 중앙화됨
대표적인 블록체인|Bitcoin, Litecoin, Etherum, Monero, QTUM|Etherum FFG&CFG, EOS(dPoS)|Klaytn, Tendermint, Hyperledger Fabric, Ontology

PoW: Nonce를 추가해서 해시를 하는데 가장 작은 값을 구했을 때(앞의 0의 개수가 가장 많을 때), GPU 마이닝, 참여 제한은 없지만 참여가 힘들다(해시파워)  

longest chain 구조: 블록을 빠르게 생성하는 사람이 다음 블록 결정 -> majority attack  
장점: 누구나 참여할 수 있다 -> 네트워크를 비동기화 상태에 두어도 합의를 이끌어낼 수 있다  
단점: 연산에 쓰는 비용이 비싸다  

PoS: 토큰의 총량이 있음, 따라서 누군가 토큰을 매집해 버리면 공격 가능

BFT-variants: 많이 느렸지만 지금 개선이 되어서 빨라짐(Klaytn은 1초에 TPS가 4000, 1초에 4000개의 tx를 블록에 담을 수 있다).  
네트워크가 동기화 되어있다.(네트워크에 참여하는 사람이 누군지 모두가 알고 있고 바뀌지 않는다.  
누가 추가되기가 어려운게 추가가 되면 하던일 멈추고 모두가 새로운 사람을 다 아는 상태가 된 후에 시작).  
통신량이 많아서 참여자가 많을 수록(20명만 되도) 느려짐(즉, 소규모 그룹에 적합, 15명 이상부터 느려짐).  
따라서 PoW, PoS보다 참여자들의 높은 신뢰성이 담보되어야 함.  

- - -

- **블록체인의 비교** Public vs Priate?  
퍼블릭과 프라이빗의 구분은 다음을 수행할 수 있는지 확인하여 결정:  
(1) 누구든지 기록된 정보(블록)를 자유롭게 읽을 수 있는지?  
(2) 명시적인 등록 또는 자격취득 없이 정보를 블록체인 네트워크에 기록할 수 있는지?  
블록체인의 정보가 공개되어 있고 네트워크가 정한 기준(e.g., gas fee)에 따라 정보를 기록요청할 수 있다면 그 블록체인은 퍼블릭/ 공개형이라 한다.  
이와 반대로 정보가 공개되어 있지 않고 미리 자격을 득한 사용자만이 정보를 기록할 수 있다면 그 블록체인은 프라이빗/비공개형이라 한다.  

- **블록체인의 비교** Permissionless vs Permissioned?  
일반적으로 네트워크의 참여가 제한된 경우 'permissioned', 그렇지 않은 경우 'permissionless'라 정의  
네트워크의 참여의 정의  
-(넓은 의미) 블록체인 P2P 네트워크에 참여  
-(좁은 의미) 합의과정의 참여  

> Public/Private 개념이 정보의 접근성(Access)와 관련이 있다면  
> Permisionless/Permissioned는 정보의 제어(Control), 즉 무엇이 블록에 포함되는지를 결정하는 지에 더 밀접한 개념  
> ex) PoW, PoS는 네트워크에 참여하면 합의에 참여도 가능한데, 비잔틴은 네트워크에 참여(정보 읽고 쓰기...)는 해도 합의(다음 블록이 무엇인지 정할 수 있는 권한)에 참여는 어려움.

- - -

### **공개키 암호화와 전자서명**  
블록체인에서는 비대칭키암호(공개키) 사용.  
공개키와 비밀키는 한쌍으로 묶여있는 아주 큰 숫자들. 비밀키로부터 공개키를 도출하는 것은 쉬움. 공개키로부터 비밀키를 찾는 것은 매우 어려움.(브루트 포스해도 어려움)  
전자서명: 비대칭키암호는 지정된 사람만 정보를 확인할 수 있도록 도움(privacy). 전자서명은 누가 정보를 보냈는지 알기 위해 사용(non-repudiation).  
해시: 변조 여부 판단 가능  

### **블록체인에서 사용되는 암호화 기법**
블록체인과 공개키 암호: 암호학적으로 주소의 소유권 확인을 위해(왜냐하면 주소 생성 때 신원 확인 등의 관여를 안하니까)  
블록체인은 암호학적 기법을 토대로 만들어진 기술  
비트코인은 네트워크 참여자 모두가 같은 '원장'을 공유함으로써 투명한 거래가 가능  
원장은 어느 주소에 BTC가 있는지 기록하지만 그 주소가 누구에게 속하는지는 기록하지 않음(anonymity)  
비트코인은 공개키암호를 사용하여 명시적인 비밀교환과정 없이 BTC 소유권 증명을 실행  

### **공개키 암호화를 사용한 소유권 증명**  
대부분의 블록체인 주소는 공개키로부터 도출된 값  

- Bitcoin: Hash160 of a public key where Hash160 = RIPEMD160 + SHA256. 
임의의 주소 X에 10 BTC가 있다고 가정할 때 Alice는 X에서 또 다른 임의의 주소 Y로 5 BTC를 전송(i.e., transfer 5 BTC from X to Y)하는 거래를 성사시키기 위해 X로 변환되는 공개키와 짝을 이루는 비밀키로 해당 거래를 서명할 수 있어야 한다.  

- Ethereum: Rightmost 160 bits of Keccak hash of a public key.  
임의의 주소 X에 위치한 어카운트의 잔고에 10 ETH가 있다고 가정할 때 Alice는 X에서 또다른 임의의 주소 Y에 위치한 어카운트로 5 ETH 전송(i.e., transfer 5 ETH from the account at X to the account at Y)하는 거래를 성사시키기 위해 X로 변환되는 공개키와 짝을 이루는 비밀키로 해당 거래를 서명할 수 있어야 한다.  

tx를 비밀키로 서명하고 이 서명과 함께 공개키를 같이 보냄. 이 공개키로 서명 검증, 또 공개키를 160 비트로 줄여봤더니 주소와 동일.  
But, 그럼 공개키도 같이 따라가야 해서 네트워크 페이로드 낭비(전자서명 256bit + 공개키 256bit), tx이 많아지면 비효율적으로 됨.  
이더리움이 서명으로부터 공개키를 도출하는 기술(오!)를 사용.  

- - -

### **구현 방법으로 나눠본 블록체인**

**UTXO(Unspent Transcation Output) 기반 블록체인**  
블록체인에 사용 가능한 토큰(e.g., Bitcoin) - UTXO들과 사용 자격검증방법을 기록  
일반적인 자격검증방법은 UTXO의 정보와 일치하는 공개키로 검증가능한 전자서명을 제출하는 것  
Bitcoin이 대표적인 UTXO 기반 블록체인  
-> 돈에다가 주소를 준 것, 돈이 그 자체로 암호화 되어 있음, 그 돈을 쓸 때 전자서명을 줌  
-> 보안성 강화, 병렬화 가능(돈과 전자서명 매칭)  

**어카운트 기반 블록체인(Account-based Blockchain)**  
어카운트는 블록체인을 구성하는 주체(entity)를 표현하며 상태를 기록  
사용자는 어카운트를 사용할 때마다 어카운트 공개키로 검증 가능한 전자 서명을 생성  
상태를 기록할 수 있기 때문에 스마트 컨트랙트를 구현하기에 용이  
Etherium, Klaytn이 대표적인 어카운트 기반 블록체인  
-> 어카운트에 주소를 준 것. 그 어카운트가 balance라는 상태 값 가짐, 어떤 어카운트를 사용하기 위해 전자서명을 줌  
-> 어카운트 기반이라 병렬화 불가능(순서대로 해야함)  
-> 대신 상태라는게 생김(상태가 없으면 그냥 일회성 프로그램)  

- **Ethereum 어카운트, 주소, 상태**?  
Ethereum의 어카운트는 Ethereum의 주체(entity)를 표현하고 그 상태를 기록하는데 사용  
어카운트는 EOA(Externally Owned Account)와 스마트 컨트랙트로 구분  
Ethereum 사용자는 EOA를 사용  
사용자는 임의의 공개키와 비밀키 쌍(Key Pair)을 생성한 뒤 공개키를 어카운트 주소로 변환하여 EOA를 생성  
-> 별도의 승인 과정이 필요없으며 Ethereum 네트워크와 통신도 필요 없음  
-> 위 과정으로 인해 어카운트는 특정 키페어에 종속  
사용자의 상태(state)는 어카운트 주소로 찾을 수 있는 블록체인 저장공간에 기록  

+) 비밀키로 공개키가 나오고 그 공개키를 주소로 바꿀 수 있다. 주소는 컴퓨터 메모리와 같은데, 그 주소에 어떤 사용자의 어카운트가 표현되어 있다.
거기에 status(상태)가 있는데 거기에 밸런스도 있고, 지금까지 tx 몇 개 발생시켰는지 등이 나옴.  

- - -

### **트랜잭션(Transaction, TX)**  
블록체인의 저장 구조에서 보면 저장의 단위, 정보의 단위.  
데이터는 트랜잭션 단위로 저장이 되고, 트랜잭션들은 일정한 순서로 정렬되어 묶음으로 저장
- 블록은 트랜잭션을 일정한 순서로 정렬하여 저장하는 컨테이너  
- 트랜잭션은 어카운트의 행동  
- 트랜잭션의 순서는 중요!!; TX_1 -> TX_2와 TX_2 -> TX_1는 다름 ex) 돈을 이미 줘서 잔고가 없는데 tx 뒤바뀌면 말이 안되게 되잖아!  
- 블록체인 참여자들은 블록을 검증할 때 트랜잭션들이 올바른 순서대로 정렬되었는지를 확인 후 합의  
- 각각의 트랜잭션들은 어카운트에 연결된 공개키로 검증가능한 서명을 포함  

> Alice가 tx를 블록체인 노드에 보내줌. 노드는 이 트랜잭션을 최대한 빨리 들어가게 하기 위해 다른 노드에게도 tx를 보내줌. 다른 애가 블록을 더 빨리 생성할 수 있으면 이 tx이 빨리 들어갈 수 있으니까.  
> 어떤 노드가 블록을 제안했는데 그때 Alice의 tx가 들어가면, 그래서 체인에 tx가 추가되면  
> 이 tx이 "1 confirmation 받았다"고 표현함. 즉, 체결 되었습니다!  
> 해당 블록 뒤에 블록이 체결될 때 마다 confirmation 수가 들어감. 즉, 높이 4인 블록에서 이 tx은 1 confirmation, 높이 5인 블록에서는 2 confirmation. 
> 즉, tx이 포함된 블록은 점점 아래에 묻히게 됨

> **이게 중요한 이유!**  
> 블록은 어느 순간 번복될 수 있음.  
> -> 비트코인, 이더리움 같이 탈중앙화되어 있는 한쪽 end node 다른 end node 사이의 네트워크 딜레이가 크다. 둘 다 거의 비슷한 시간에 문제를 풀었을 때, 주변 동네에서는 그 블록들이 각각 인정 받음.  
> -> 중간 지점에는 헷갈림. 누가 먼저인가?! 판단이 어려우니 경쟁이 시작, 나는 A 블록이 맞다고 생각하니 A를 기반해서 블록을 쌓아 나갈 거야 등  
> -> longest 체인이 더 빨리 되기 위해 경쟁을 함, 해시파워가 큰 쪽이 이기게 됨. longest chain만 선택 받음. PoW를 가장 긴 체인의 마지막 블록 기준해서 돌려야 내 블록이 포함될 수 있음. 그래야 내가 리워드를 받으니까.(경제적으로 맞는 선택)  

- - -

### **Understanding Bitcoin's 6 Confirmations Rule**  
- Confirmation 숫자는 트랜잭션이 블록에 포함된 이후 생성된 블록의 숫자 ex) 임의의 트랜잭션 T가 포함된 블록의 높이가 100, 현재 블록높이가 105라면 T의 confirmation 숫자는 6  
- PoW를 사용하는 블록체인들은 finality가 없기 때문에 confirmation 숫자가 중요 (e.g., 약간의 네트워크 딜레이로 1-2개 차이는 날 수 있는데, 몇 개 이상 차이나기는 확률적으로 어려움. Bitcoin은 6개, Ethereum은 7개 제한)  
- Finality란 블록의 완결성을 의미(합의를 통해 생성된 블록이 번복되지 않을 경우 완결성이 존재)  
- PoW 기반 합의는 확률에 기반하기 때문에 경우에 따라 블록이 사라질 수 있으므로 완결성이 부재함  
-> PoW 블록체인은 수학적으로 복잡한 퍼즐을 풀어 블록을 제안할 자격을 얻는 구조  
-> 만약 두명의 서로 다른 참가자가 동시에 퍼즐을 풀어 두개의 올바른 블록을 생성한다면 두 블록 중 하나는(eventually) 사라지게 됨  
-> 이 때문에 블록이 확률적 완결성를 갖기까지 일정 갯수 이상의 블록이 생성되기를 기다려야 함.  
즉, 블록에 완결성이 없다 = 그 블록이 사라질 수 있다.  

네트워크 시차로 인해 생성된 우연한 복수의 블록들 가운데 하나가 선택되는데 필요한 블록은 두어개 정도 -> 2~3 confirmations  
퍼즐을 빠르게 풀 수 있는 악의적인 참여자(공격자)가 있을 경우 그 참여자의 해시능력(hash power)에 따라 필요한 confirmation 숫자가 달라짐  
- 해시능력이 높을 수록 퍼즐을 푸는 속도도 빠르기 떄문에 주어진 문제를 먼저 풀 확률이 높아짐  
- 해시 능력이 높은 참여자는 longest chain을 임의로 선택 또는 생성할 수 있음  
- 따라서 해시능력을 감안하더라도 임의의 블록체인을 변경하지 못할 정도로 충분히 많은 블록이 생성되기를 기다려야할 필요가 생김  
- Bitcoin의 6 confirmation 법칙은 공격자가 전체 해시능력의 약 25%를 가질 때를 가정한 숫자
(공격자가 존재할 때를 가정해서, 대단한 해시파워를 가진 사람이 악의적으로 블록을 추가할 때 몇 번의 confirmation을 받아야 안전하게 내 tx이 블록에 포함되었는지 확신할 수 있는가)  
- 99.9%의 확률로 내 tx이 포함된 블록을 final 함. (0.1% 문제는 여전히 있는 것 -> 따라서 비트코인, PoW의 finality를 확률적 finality라고 함)  

> #Confirmations to wait if...  
> PoW 블록체인이 공격자가 전체 해시능력의 x%를 가질 때 99.9% 완결성을 확보하려면 다음과 같은 confirmation 숫자가 필요:  

x|# confirmations|기다려야하는 시간
---|---|---
10|4|40
25|7|1시간 10분
33|10|1시간 40분
49|170|2시간 50분

여전히 담합의 여지가 있다.  

- - -

### **BFT 기반 블록체인**  

많은 통신이 이루어지는 대신 합의해서 만든 블록은 만들어진 그 시점에 모두 final 함.  

BFT기반 블록체인은 블록의 완결성이 보장됨.  
- 네트워크가 동기화되어 있기 때문  
- 블록 생성이 PoW에 비해 빠르고 경제적  

하지만 네트워크 동기화의 필요로 인해 참여자의 숫자가 제한됨(~~중앙화되기 쉬움~~)  
- 네트워크 참여자 구성이 고정되어 있어야 합의가 가능  
- 구성이 변경될 경우 모든 네트워크 참여자가 새로운 구성을 인지하기까지 합의 불가능  
- 합의 알고리즘이 네트워크 동기화를 가정하고 짜여졌기 때문에 네트워크 사용량이 높음  
- 참여자가 많아질 경우 네트워크 오버헤드로 인해 합의가 느림  

- - -

## 1. 블록체인 상태와 트랜젝션  

### Klaytn BFT  
- Klaytn은 확장가능한 BFT을 사용(네트워크에 참여하는 전체 집합을 대상으로 BFT를 실행하지 않음!)  
> N개의 노드 가운데 S개의 부분노드 집합을 확률적으로 선택(where N is large, and S is sufficiently small). 
> 전체집합을 거버넌스 카운실(Governance Council),  
> 부분집합을 커미티(Committee)로 정의  
> 커미티 선택은 VRF(Verifiable Random Function)로 구해진 무작위값에 기반  

- 매 블록마다 새 커미티를 뽑아 BFT를 실행  
- 기존의 BFT에 비해 확장성을 크게 개선

Klaytn이 BFT를 선택한 이유  
PoW가 너무 느리고, 비용도 많이 들기 때문에  
PoS는 누군가가 과반을 점유하기 너무 쉬운 구조라서(자금을 많이 가진 사람)  

따라서 PoW로 먼저 시작하고 다양한 사람이 코인을 소유하면 그 때 PoS로 전환함(PoS로 먼저 시작하는 코인은 없음)  

- - -

### 블록체인의 상태(Blockchain State)  

* (어카운트 기반) 블록체인의 상태  
블록체인은 트랜잭션으로 변화하는 상태 기계(State Machine)  

초기값(initial), 최종값(final): 초기값을 변경(Change)을 했더니 최종값이 나옴  
![image](https://user-images.githubusercontent.com/46364778/190264489-08bd5e14-a733-4a7c-835d-712acfe7f485.png)

블록을 생성하는 사람에게서 토큰을 줄 때, 특정 account(보통 자신의 account)에 토큰을 주게 되고, 항상 from(source)와 to가 있음  
source가 coinbase: 이 사람이 mining 했구나(여기서는 Alice가 Coinbase로 부터 토큰을 받았으므로, Alice가 이 블록을 캤구나, 블록을 제안해서 받아들여졌구나 알 수 있음)  
앞의 블록의 최종값이 다음 블록의 초기값!  
즉 초기값은 이전 블록의 상태이고, 최종값은 현재 블록의 상태이다!  
변경 사항들을 계속 적용하면서 상태를 유지해 나감  
변경 사항 = Transaction

* 상태 기계  
블록체인은 초기 상태에서 변경사항을 적용하여 최종 상태로 변화하는 상태 기계  
이전 블록의 최종 상태는(final state)는 현재 블록의 초기 상태(initial state)  
Gen block의 경우 임의의 초기값들이 설정되는데 이것이 gen block의 초기상태이자 최종상태  

* (어가운트 기반) 블록체인의 상태  
TX는 어카운트를 생성하거나 변경
> e.g., Alice가 기존에 존재하지 않던 주소 X에 ETH를 전송하면 새로운 EOA가 생성  
> e.g., Alice가 새로운 스마트 컨트랙트를 배포(컨트랙트도 어카운트)  
> e.g., Alice가 Bob에게 5 ETH를 전송하는 TX가 체결되면 Alice의 Bob의 잔고가 변경  
항상 같은 결과를 보장하기 위해 하나의 TX가 반영되는 과정에서 다른 TX의 개입은 제한됨.  

+) 블록체인에서는 명시적으로 회원가입이라는 프로세스가 없음. 어카운트를 만드는 것은 key pair를 만드는 것. 그 key pair와 연관되어 있는 TX을 만들고, 그 TX이 블록체인에 변경을 더해줘야지만 그 account의 존재가 블록체인에 알려지는 것.(물론 변경된 결과는 모두가 동의해야지)  

- - -  

* (Recall) Ethereum 어카운트의 종류  
1. External Account: 사용자(end user)가 사용하는 어카운트 (a.k.a. EOA)  
2. Contract Account: 스마트 컨트랙트를 표현하는 어카운트  

-Ethereum은 EOA와 스마트 컨트랙트의 상태를 기록 및 유지  
스마트 컨트랙트는 특정 주소에 존재하는 실행 가능한 프로그램  
프로그램(코드+데이터)은 상태를 가지기 때문에 Ethereum/Klaytn은 스마트 컨트랙트를 어카운트로 표현  

-EOA는 블록에 기록되는 TX를 생성  
블록에 기록되는 TX들은 명시적인 변경을 일으킴(e.g., 토큰 전송, 스마트 컨트랙트 배포/실행)  


### 트랜잭션(TX)과 가스(Gas)  

* TX의 목적은 블록체인의 상태를 변경하는 것  
TX는 보내는 사람(sender, from)과 받는 사람(recipient, to)이 지정되어 있으며  
to가 누구냐에 따라 TX의 목적이 세분화  
> To가 External Account(사용자)면, 그냥 토큰 전송한 것(value transfer)  
> Sender가 External Account인데 To가 Contract Account면, 컨트랙트가 실행이 됨  
> To가 지정이 안되는 경우, 컨트랙트를 배포한다고 봄.  

* Gas: TX를 처리하는데 발생하는 비용  
TX를 처리하는데 필요한 자원(computing power, storage)을 비용으로 전환한 것이 가스(gas)  
Sender는 TX의 처리를 위해 필요한 가스의 총량과 같은 가치의 플랫폼 토큰을 제공해야함  
이때 지출되는 플랫폼 토큰을 가스비(Gas Fee)라 정의(블록체인의 유틸리티를 쓰기 위해 비용을 지불하는 것)    
가스비는 블록을 생성한 노드가 수집  

transaction fee를 높이 쓸 수록 채굴자가 먼저 처리해줌. 즉, tx이 블록에 빠르게 들어감(이더리움은 느리니까)  
연산 비용과 실제 토큰을 분리해 놓은 것. 연산 비용을 이더리움에서는 가스라고 부름.  

클레이튼: 그럼 네트워크 자체가 빠르면 될 거 아니야? 우리는 충분히 빠르니까 가스 price 쓸 수 없게 하자! 그래서 사용자들끼리 경쟁하는 일은 없음, 그냥 FIFO

### 트랜잭션과 서명  
* 플랫폼은 sender가 TX가 처리되는데 필요한 가스비를 가지고 있는지 확인  
가스비 확인은 구현에 따라 상이  
Ethereum/Klaytn은 노드가 TX를 수신함과 동시에 가스비 이상의 balance가 있는지 확인  
TX의 체결과 동시에 sender의 balance에서 가스비를 차감  

* TX는 sender의 서명(v, r, s)이 필요  
어카운트의 balance를 사용하기 때문  
서명의 증명은 구현마다 상이  
> Ethereum: 서명 -> 공개키 노출 -> 어카운트 주소 도출 -> 어카운트 존재유무 확인  
> Klaytn: from 주소 확인 -> 저장된 공개키 불러오기 -> 서명 직접 검증  
> 이더리움은 서명으로 공개키를 뽑아냄(이더리움이 그런 함수(서명 -> 공개키)를 개발, 채굴자가 뽑아내서 검증, 이 함수가 연산량이 늘어나는 주범!)  

* 트랜잭션 예시  
![image](https://user-images.githubusercontent.com/46364778/190269797-8187ea73-d9b5-49c3-ac6c-025eaf811386.png)  

nonce: (nonce가 tx에 있는건 account 기반 블록체인의 특징, sequential 하게 만듦) account가 몇 번째 tx를 보내는지 말해줌.  
> 블록체인의 병렬화를 막는 원인. 비트코인은 병렬화가 가능하지만 이더리움은 불가능하다고 누군가 말하면 논스를 말하는 것  

![image](https://user-images.githubusercontent.com/46364778/190270282-8dcd896d-0f91-4f0b-bcfb-8a4a664a5644.png)  

이더리움은 from이 없음 -> 256 비트 아끼려고  
대신 가스비가 있음. gas price와 gas를 곱하면 내가 이 tx를 위해 지불하는 금액이 됨.  
V, R, S가 전자 서명임.  

![image](https://user-images.githubusercontent.com/46364778/190270497-970456ec-8787-4dbf-9135-7a3c617d30ad.png)  

type: 뭐하는 tx인지 설명(eth 같이 type이 없으면 to가 누구냐를 확인하기 전까지는 이 tx이 뭐하는 tx인지 모름. to를 보고 짐작)  
gasPrice는 고정값. 사용자가 바꿀 수 없음  

> 클레이튼의 가스비는 이더리움의 1/10 미만이 되도록 유지 함  
> 이 값은 전체 노드들이 합의하면 바뀔 수 있음.  

### Transaction Journey  
![image](https://user-images.githubusercontent.com/46364778/190270934-2a00493c-c65b-4fc7-a4d8-ced95fc601fd.png)  

Receipt는 컨트랙트를 배포할 때 가장 유용. 컨트랙트가 어디에 배포되었는지 주소를 영수증에 적어줌.(사용자 레벨에서 확인이 가능하지만 번거로우니까)  

사용자와 노드는 같은 프로토콜로 통신해야함.  

**Alice와 Node 사이 통신**  
**Alice -> Node**  
-Alice는 TX를 생성, 서명하여 Node에게 전달  
-이때 데이터 구조를 온전하게 전달하고자 RLP 알고리즘으로 TX를 직렬화  
-Alice와 Node가 같은 프로토콜로 통신하는 것이 중요  

**Node -> Alice**  
-올바른 TX 수신 시 TX 해시를 반환  
-TX 체결 시 Receipt를 반환; 소요된 Gas, TX 해시, input 등이 기록  

- - -

### Smart Contracts  

블록체인에 저장되어 있는 프로그램. 어디에 저장되어 있는지 알기 위해 주소를 씀.  
실제로 이더리움 코드 까보면, 어카운트 라는 코드가 있고 그 안에 코드 해시라는 값이 있음. 이걸 가지고 블록체인에 있는 코드를 불러올 수 있는 구조.  
블록체인의 상태를 바꿀 수 있는건 스마트 컨트랙트밖에 없음.  

* 특정 주소에 배포되어 있는 TX로 실행 가능한 코드  
스마트 컨트랙트 소스코드는 **함수**와 **상태**를 표현; 컨트랙트 소스코드는 블록체인에 저장  
함수는 상태를 변경하는 함수, 상태를 변경하지 않는 함수로 분류  
사용자(end user, EOA owner)가 스마트 컨트랙트 함수를 실행하거나 상태를 읽을 때 주소가 필요  

* 스마트 컨트랙트는 사용자가 실행  
상태를 변경하는 함수를 실행하려면 그에 맞는 TX를 생성하여 블록에 추가(TX 체결 = 함수의 실행)  
상태를 변경하지 않는 함수, 상태를 읽는 행위는 TX가 필요 없음(노드에서 실행)  


### Solidity  
Ethereum/Klaytn에서 지원하는 스마트 컨트랙트 언어  
Klaytn은 Solidity 버전 0.4.24, 0.5.6을 지원  
일반적인 프로그래밍 언어와 그 문법과 사용이 유사하나 몇가지 제약이 존재(e.g., 포인터의 개념이 없기 때문에 recursive type의 선언이 불가능)  

* Contract = Code + Data  
Solidity 컨트랙트는 코드(함수)와 데이터(상태)로 구성  
Solidity 함수는 코드 안에 변수로 선언된 상태를 변경하거나 불러옴  
아래 예시에서 set, get은 함수, storedData는 상태  

```
contract SimpleStorage {
  uint storedData;
  
  function set(uint x) public {
    storedData = x;
  }
  
  function get() public view returns (uint) {
    return storedData;
  }
}
```

* Solidity 예제 - Coin 컨트랙트  
[컨트랙트 예제](https://docs.soliditylang.org/en/v0.6.1/introduction-to-smart-contracts.html#subcurrency-example)  

```
// Solidity로 간단한 포인트 시스템을 구현  
// [Coin 컨트랙트]
// 컨트랙트 생성자가 관리하는 포인트 시스템 컨트랙트로 포인트 시스템 고유의 주소공간(address space)을 가지며 각 주소의 포인트 잔고를 기록한다.
// 컨트랙트 생성하는 사용자 주소(e.g., 0xALICE)에 포인트를 부여할 수 있고 사용자는 다른 사용자에게 포인트를 전송할 수 있다 (e.g., 0XALICE -> 0XBOB, 10 Coins)

// Solidity 버전을 지정
pragma solidity ^0.5.6

// N.B. 컨트랙트 함수는 함수를 실행한 TX의 정보를 받을 수 있는데 해당 정보를 msg 변수로 접근
contract Coin {
    // "minter"는 address 타입으로 선언된 상태; address 타입은 Ethereum에서 사용하는 160 bit 주소
    // "public" 키워드는 상태를 다른 컨트랙트에서 읽을 수 있도록 허용
    address public minter;
    
    // "balances"는 mapping 타입으로 address 타입 데이터를 key로, uint 타입 데이터를 value로 가지는 key-value mapping
    // mapping은 타 프로그래밍 언어에서 사용하는 해시테이블 자료구조와 유사
    // uninitialized 값들은 모두 0으로 초기화되어 있는 상태
    mapping (address => uint) public balances;
    
    // event로 정의된 타입은 클라이언트(e.g., application using a platform-specific SDK/library)가
    // listening 할 수 있는 데이터로 emit 키워드로 해당 타입의 객체를 생성하여 클라이언트에게 정보를 전달
    // usage:
    // /* in Solidity */
    // emit Sent(an_address, another_address, 10);
    // /* in Web3.js */
    // Coin.Sent().watch({}, '', function(err, result) { ... });
    event Sent(address from, address to, uint amount);
    
    // 생성자 함수는 컨트랙트가 생성될 때 한번 실행
    // 아래 함수는 minter 상태 변수에 msg.sender 값을 대입 (함수를 실행한 사람의 주소)
    constructor() public {
        minter = msg.sender;
    }
    
    // [omitted state variables, event definitions, and constructors for brevity]
    // receiver 주소에 amount 만큼의 새로운 Coin 부여
    // require 함수는 입력값이 true일 때만 다음으로 진행할 수 있음 (타 언어의 assert와 유사)
    // require 함수는 특정 조건을 만족할 경우에만 함수를 실행할 수 있도록 강제할 때 사용
    functoin mint(address receiver, uint amount) public {
        require(msg.sender == minter);  // 함수를 실행한 사람이 minter(i.e., 컨트랙트 소유자)일때만 진행
        require(amount <1e60);          // 새로 생성하는 Coin의 양이 1 * 10^60개 미만일 때만 진행
        balances[receiver] += amount;   // reveiver 주소에 amount만큼을 더함
    }
    
    // msg.sender가 receiver에게 amount 만큼 Coint을 전송(from을 인자로 받지 않아도 msg.sender로 유추할 수 있다)
    function send(address reveiver, uint amount) public {
        require(amount <= balances[msg.sender], "Insufficient balance."); // 잔고가 충분한지 확인
        balances[msg.sender] -= amount;           // 잔고 차감
        balances[revceiver] += amount;            // 잔고 증가
        emit Sent(msg.sender, receiver, amount);  // 이벤트 생성
    }
}
```

msg.sender: 최초의 사용자
tx의 sender: 누구든지 될 수 있음. 바로 앞의 컨트랙트 등...

* Solidity 소스코드 컴파일링  
Solidity 소스코드는 배포에 앞서 EVM에서 실행 가능한 형태로 컴파일(변환) 되어야 함
**solc** - Solidity 컴파일러

npm(linux, macos, Windows)으로 light version을 설치 가능(solcjs, partial feature)
```npm install -g solc```

apt(debian linux), brew(macos) 등으로 binary 설치 가능(solc, full feature)
```
(debian)
sudo add-apt-repository ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install sloc

(macos)
brew tap ethereum/ethereum
brew install solidity
```

* Solidity 소스코드 컴파일링
SimpleStorage 컨트랙트를 정의하는 test.sol이 있다고 가정
다음과 같은 방법으로 test.sol을 컴파일
<img width="675" alt="image" src="https://user-images.githubusercontent.com/46364778/190287821-b3a5dc61-a78e-4b78-a413-6c6c4928ddce.png">  

### Bytecode & ABI  
Solidity 소스코드(.sol 파일)를 컴파일하면 Bytecode(.bin 파일)와 ABI(.abi 파일)가 생성  

* **Bytecode**  
컨트랙트를 배포할 때 블록체인에 저장하는 정보  
Bytecode는 Solidity 소스코드를 EVM이 이해할 수 있는 형태로 변환한 것  
컨트랙트 배포시 HEX로 표현된 Bytecode를 TX에 담아 노드에 전달  

* **ABI(Application Binary Interface) a.k.a JSON interface**  
ABI는 컨트랙트 함수를 JSON 형태로 표현한 정보로 EVM이 컨트랙트 함수를 실행할 때 필요  
컨트랙트 함수를 실행하려는 사람은 ABI 정보를 노드에 제공  
~~ 프로그램 헤더가 빠진 상태로 들어가 있어서 별도로 이렇게 말을 해줘야 함~~

- - -

### Using Klaytn SDK  
Klaytn은 BApp 개발을 위해 필요한 SDK를 제공  
caver-js는 Node.js로 Klaytn BApp을 만들 때 필요한 라이브러리를 제공  
다음 [온라인 문서](https://docs.klaytn.com/sdk/caverjs)에서 사용 방법을 확인  

* Baobab 테스트넷에 연결  
```
const Caver = require('caver-js');
const caver = new Caver('https://api.baobab.klaytn.net:8651/');
```  
테스트넷의 토큰은 쉽게 구할 수 있도록 되어 있음. -> Faucet

* klay.getBlockNumber()  
```
const Caver = require('caver-js');
const caver = new Caver('https://api.baobab.klaytn.net:8651/');
// getBlockNumber() returns a Promise object returning Number
caver.klay.getBlockNumber(function(err, blockNumber) {
 console.log(blockNumber);
});
// alternatively
caver.klay.getBlockNumber().then(console.log);
7092446
```

* klay.accounts.wallet  
account들을 담아놓기 위한 월렛, 컨테이너  
```
const account = caver.klay.accounts.create();
// in-memory wallet
const wallet = caver.klay.accounts.wallet;
wallet.add(account);
console.log(wallet.length); // wallet에 저장된 어카운트 갯수를 리턴
console.log(wallet[account.address]); // 해당 주소를 가지는 어카운트를 불러옴; 없을 경우 undefined
console.log(wallet[0]); // 저장된 첫번째 어카운트를 불러옴; 없을 경우 undefined
1
{ address: '0x38aed90665...c4fb53faae', privateKey: '0xfca84955...a96e5c5f', index: 0 }
{ address: '0x38aed90665...c4fb53faae', privateKey: '0xfca84955...a96e5c5f', index: 0 }
```

* 토큰전송TX 생성 & 서명  
```
wallet.clear(); wallet.create(2); // in-memory wallet 초기화 & 어카운트 2개 생성
const tx = {
  type: "VALUE_TRANSFER", // Klaytn은 TX 타입을 지정
  from: wallet[0].address, // 첫번째 어카운트 주소
  to: wallet[1].address, // 두번째 어카운트 주소
  value: caver.utils.toPeb('1', 'KLAY'), // 1 KLAY 전송
  gas: 300000 // TX가 사용할 수 있는 가스총량
};
// 첫번째 어카운트의 비밀키로 서명
caver.klay.accounts.signTransaction(tx, wallet[0].privateKey).then(console.log);

{ messageHash: '0xc95641e147622735347d69e33ce8d5704f1dd240c86ba078d8118b2881b81742',
 v: '0x07f5', r: '0x1de39143f3...19e991d31e', s: '0x53d86c54...bf00e6db',
 rawTransaction: '0x08f87f808505d21d...ed0f06bf00e6db',
 txHash: '0xb57dd4ec...1f509ea2', senderTxHash: '0xb57dd4ec...1f509ea2' }
```

* 서명된 TX 전송  
```
const tx = { … };
(async () => {
    const signedTransaction = await caver.klay.accounts.signTransaction(tx, sender.privateKey);
    await caver.klay.sendSignedTransaction(signedTransaction.rawTransaction)
        .on('transactionHash', function(txhash) {
            console.log('hash first', txhash);
        })
        .on('receipt', function(receipt) {
            console.log('receipt later', receipt);
        })
        .on('error', function(err) {
            console.error('something went wrong');
        });
})();
```

* 토큰전송TX + sendTransaction  
```
const tx = { … };
caver.klay.sendTransaction(tx) // 서명 + 전송
    .on('transactionHash', function (txhash) {
        console.log("hash first", txhash);
    })
    .on('error', function (err) {
        console.error('something went wrong');
    })
    .on('receipt', function (receipt) {
        console.log("receipt later", receipt);
    });
```

* 스마트 컨트랙트 배포  
Solidity 코드를 컴파일해서 생긴 Bytecode를 블록에 저장  
Truffle을 쓰는게 편함  
```
// 앞서 예제에서 본 SimpleStorage 컨트랙트의 ABI와 Bytecode를 사용
const abi = [ … ];
const contract = new caver.klay.Contract(abi);
contract.deploy({ data: '6080604052348015...0029' })
    .send({
        from: wallet[1].address,
        gas: 3000000,
        value: 0
    })
    .on('receipt', function (receipt) {
        console.log('contract deployed at', receipt.contractAddress); // 컨트랙트 주소가 receipt에 포함
    })
```

* 스마트 컨트랙트 실행(mutation)  
상태를 바꾸는 함수들 
```
const contract = new caver.klay.Contract(abi, '0x20e199c44768F2C39Cb771D2F96B13fE6D63a411');
contract.methods.set(100) // SimpleStorage의 set 함수를 실행; 상태를 바꾸는 함수이기 때문에 TX로 실행
 .send({
    from: wallet[1].address,
    gas: 300000
 })
 .on('error', function (hash) { … })
 .on('transactionHash', function (hash) { … })
 .on('receipt', function (receipt) { … });
```

* 스마트 컨트랙트 실행(constant)  
```
const contract = new caver.klay.Contract(abi, '0x20e199c44768F2C39Cb771D2F96B13fE6D63a411');
// call 함수는 상태를 바꾸는 함수가 아니기 때문에 노드에서 바로 실행
contract.methods.get().call(null, function (err, result) {
 if (err == null) {
    console.log(result);
 } else {
    console.error(err);
 }
});
```

- - -

## 3. 블록체인 어플리케이션(Blockchain Application, BApp)  
사실 BApp에서 할 수 있는 일은 다른 일반 App에서도 다 할 수 있음. 그렇다면 왜 BApp이 필요한가?  
신뢰에는 비용이 든다. ex) 공증, 은행 등  
그러나 블록체인은 제 3자를 신뢰할 필요가 없음 -> 기존에 하기 어려웠던 일을 손쉽게 할 수 있음  

### Blockchain Application (BApp)  
* 블록체인 어플리케이션(BApp)은 블록체인을 사용하는 어플리케이션  
기존의 기술로 풀기 어려운 문제들을 블록체인의 특성을 활용하여 풀어내는 것이 목적  

* **불변성**과 **투명성**이 대표적인 블록체인의 특성  
한번 기록된 정보는 변경할 수 없으며  
정해진 규칙(e.g., 블록생성 등 프로토콜이 가진 규칙, 컨트랙트로 구현된 규칙)에 따라 상태를 변경  
기록의 내역이 블록에 공개되어 있으므로 누구든지 정보의 진실여부를 확인 가능  

### BApp들이 블록체인을 사용하는 유형  
* As a Payment Channel  
토큰을 사용한 결제  
* As a Storage  
블록체인을 안전한 저장소로 인식  
* As a World Computer  
모든 노드가 동일한 연산을 수행  
어느 한 노드에 의존하지 않는 탈중앙화된 실행 환경  
스마트 컨트랙트 이용  

### BApp의 유형  
블록체인의 불변성과 투명성을 이용해 데이터의 무결성을 획득했는데, 이 무결성을 어떻게 전달하느냐  
전통적인 방식과 같음 -> semi decentralized  

* Fully decentralized  
사용자(클라이언트)가 직접 블록체인과 통신  
완벽한 탈중앙화 but 완벽하게 블록체인을 사용하려면 나도 블록체인의 정보를 모두 알고 있고 스스로 검증할 수 있어야함.  
할 수 없다면 누군가에게 물어봐야함. 중개인이 대신 블록체인과 통신해주는데 거짓말을 할 리스크도 있음.  

* Semi-decentralized with centralized proxy  
클라이언트가 블록체인과 통신하기 위해 중개 서버와 통신  
블록체인 기반으로 만들어진 서비스가 있고 그 서비스를 사용자들이 사용하는 형태  
클라이언트 <=> 중개서버 <=> 블록체인  

### Fully Decentralized  
* 장점:  
높은 투명성
신뢰 형성에 필요한 비용 없음(블록체인을 직접 쓰니까)  
경우에 따라 사용자의 익명성 보장 가능(key pair를 만들고 이 키페어로 tx 잘 만들 수 있으면 블록체인을 잘 사용할 수 있음)    
(설치형 BApp의 경우) 관리 비용 낮음

* 단점:  
사용자 책임 증가, 어려운 UX
로직 변경 어려움  
경우에 따라 사용자가 블록체인에 상시 접속할 필요 및 블록을 복제할 필요 있음(제네시스 블록부터 현재까지 모두 복제 필요, 스토리지 비용, (마이닝 참여 시)연산 비용이 발생)  

### Semi-decentralized with centralized proxy  
* 장점:  
(기존의 서비스들과 동일한) 높은 수준의 UX  
사용자가 블록체인과 직접 통신할 필요 없음  
로직 변경 비교적 쉬움  

* 단점:
신뢰비용 발생  
서비스가 Single Point of Failure(SPoF)가 됨(그 서버가 다운되면 모든 게 다 실패한다. 걔가 하나의 점이다.)  
관리 비용 높음  

- - -

### BApp 개발  
* 프론트엔드(Frontend)  
사용자가 직접 사용하는 프로그램(e.g., mobile app, web page/app)  
User Interface(UI), 통신, 이벤트 처리 등을 사용자 환경을 고려하여 개발  
TX 생성, 서명, 전송 등을 프론트엔드에서 처리  

* 백엔드(Backend)  
사용자에 눈에 보이지 않는 상시 동작하는 서비스  
프론트엔드가 사용자 요청을 전달하면 백엔드가 처리하는 구조  
블록체인 동기화 등 컴퓨팅 리소스가 많이 필요한 일을 처리하는데 적합  
블록체인 동기화, 블록 파싱, TX 전달, 가스비 대납 등을 백엔드에서 처리  
+) 컨트랙트 개발  

* Fully decentralized = Frontend + Blockchain. 
> Web + Klaytn  
> Android + Klaytn  
> Windows + Klaytn  

* Semi-decentralized = Frontend + Server + Blockchain  
> Web + Java Server on AWS + Klaytn  
> Android + Node.js Server on Azure + Klaytn  

- - -

### 프론트엔드 개발  
* BApp이 실행되는 환경에 따라 개발방법이 달라짐  
실행환경: Web, Mobile(Android, iOS), Native(Windows, Linux, macOS)
어느 환경에서 개발하느냐에 따라 개발 언어와 UI/UX 디자인, 사용 SDK가 달라짐  
Klaytn은 Javascript, Java(Android, Native) SDK를 제공  
Javascript는 Web, Native (with Node.js support)에서 사용  
Java는 Android, Native (with JRE)에서 사용  

* 프론트엔드 개발에 영향을 끼치는 실행환경 중 하나가 지갑  
지갑의 존재유무에 따라 개발방법이 변경  
특정 지갑을 사용할 경우 해당 지갑이 제공하는 라이브러리를 사용  

### 지갑(Wallet)  
* TX를 서명하려면 키가 필요  
키 -> 어카운트  
서로 다른 키는 다른 어카운트에 매핑  
하나의 어카운트로 여러 BApp을 사용하려는 사용자의 니즈가 존대  

* 지갑 = 키를 관리하는 프로그램  
키를 보관하고 BApp이 요청할 때마다 보관 중인 키로 TX를 서명  
여러 유형의 지갑이 존재(브라우저 플러그인, Dapp 브라우저 내장 지갑, 클라우드 지갑, 디바이스 지갑)  

### 지갑을 고려한 BApp 개발  
* 어떤 지갑을 사용하느냐에 따라 사용자 환경이 변화  
> 어떤 형태로 BApp을 만들 것인지? 웹앱? 모바일 웹? 모바일 네이티브? 데스크탑?  
> BApp 지갑을 내장할 것인지 아니면 외부 지갑을 사용할 것인지  
> 외부 지갑을 쓸 경우 개발하려는 BApp 형태와 맞는 지갑이 어떤 것인지?  
> e.g., 웹앱의 경우 Metamask를 사용 가능(Ethereum)  
> e.g., 모바일 웹 또는 모바일 네이티브의 경우 삼성 블록체인 키스토어를 사용 가능 (Klaytn). 

**BApp의 목적 및 타겟 사용자를 분석하여 어느 형태로 키를 관리할지 결정**  

### 백엔드  
* 블록체인 프로토콜 이외의 정보를 관리할 경우 필요  
UX 향상 및 서비스 구현을 위해 TX 외 다른 정보가 필요할 경우 백엔드를 운영  
> e.g., 문서공증 BApp  
> 사용자가 문서 공증을 위해 BApp 사용  
> 문서의 해시를 포함한 공증기록은 블록체인에 기록  
> 정보의 누설을 원치 않을 경우 문서 원본은 블록체인에 기록하기 어려움  
> 문서를 클라이언트에 보관할 수 있으나 분실의 위험, 여러 장치에서 접근할 수 없는 점 등이 문제  
> 백엔드에서 안전하게 원본 문서를 관리하여 편리한 서비스 구현이 가능  

* 서비스 제공자가 실행환경을 결정  
개발 방법 선택이 비교적 자유로운 편이나 대부분의 경우 플랫폼 SDK 존재 유무에 따라 방향을 결정  

- - -

## 블록체인 응용사례  

목차 
1. Blockchain for Businesses  
a. Supply Chain  
b. Cross-border Settlement  

2. Blockchain for End-Users  
a. Electronic Medium of Exchange  
b. Unique Electronic Assets  

### Supply Chain  
* 공급망 관리 (Supply Chain Management)  
부품 제공업자, 생산자, 배포자, 고객에 이르는 물류의 흐름을 하나의 가치사슬 관점에서 파악하고 필요한 정보가 원활히 흐르도록 지원하는 시스템  

* SCM은 글로벌화된 시장에 필수  
글로벌 마케팅, 소싱, 제조, 지역별 특성화 및 전 세계 제품 전략 수립이 필요  
고품질, 저가격, 적기 납기의 중요성 증대  
제품가치의 60~70%가 제조 이외의 부분에서 발생하므로 전체 라인의 관리가 필요  

#### Walmart Food Trust Chain  
* 식품 병충해 발생 시 빠르게 근원지를 파악하는 것이 목적  
글로벌 소싱에 대응한 투명성을 강조한 시스템  
병충해 발생 시 문제가 되는 유통 경로를 다른 유통 경로들로부터 격리시켜 피해를 최소화  
* 2 건의 PoC 실행  
Hyperledger Fabric으로 구현  
미국내 망고 유통 추적과 중국내 돈육 유통 추적을 하는 시스템을 구축  
미국내 유통되는 망고 원산지 추적에 걸리는 시간 = 2.2초(기존 7일)  
중국내 유통되는 돈육의 인증서를 블록체인에 기록 => 신뢰도 향상  
* 현재 5개 공급채널로 유통되는 25개 제품을 블록체인으로 추적관리 중  

### Cross-border Settlement  
* 은행을 거친 국가간 지급결제  
환거래 은행을 거침  
복잡함 + 고비용 + 즉시성 떨어짐  
* 송금전문업체의 등장  
웨스턴유니언, 머니그램  
비교적 빠른 송금 가능  
* 결제 전문 서비스들의 등장  
환거래은행 배제
송금 불가능  

#### BoC(Bank of Canada) & MAS(Monetary Authority of Singapore)  

**BoC & MAS Cross-border Settlement**  
* 두개의 다른 네트워크 간 통신  
-BoC는 R3 Corda, MAS는 JPM Quorum  
-Payment versus Payment (PvP) 지급건마다 atomic swap 실행(hashed time-locked contract)  
-즉시 송금 가능 & 수수료 최소화  
-큰 액수의 결제에는 아직 사용되지 않음  

<img width="369" alt="image" src="https://user-images.githubusercontent.com/46364778/190584779-b0df650c-97cc-4d59-89cc-562492f0ede6.png">  

기업 -> 비용 절감(10%면 많은 것. 그 이상이면 굳)  
- - - 
소비자 -> 비합리적, 비용보다는 감성, 편의성, 물론 너무 또 비싸면 안됨  

### Electronic Medium of Exchange  
* 실물화폐에서 전자화폐로 변화하는 사회  
-효율성 증가를 위해 실물화폐(e.g., 지폐, 동전)에서 전자화폐로 전환하는 추세  
-신용카드의 사용증가(e.g., 대한민국), 앱결제 사용률 증가(e.g., 중국) 등이 변화를 가속화  
-중앙은행, 기업 등이 발행 및 원장 관리를 수행 => 불투명한 운영  
-위변조 위험 존재  

* 블록체인 기반 전자화폐  
-발행은 여전히 중앙화된 기관 또는 사업자가 수행  
-분산원장(=블록체인)에 기록함으로써 투명성 및 추적성 확보  
-빠른 정산 가능  

#### 김포 화폐  
* 지역화폐  
발급된 해당 지자체 관내 가맹점에서만 사용 가능한 화폐(또는 바우처)  
지역경제 활성화를 목적으로 사용  
* 김포페이  
김포시의 지역화폐로 모바일 앱 간편결제(가맹점), 실물 카드 사용(비가맹점 포함)을 허락  
김포페이 구입(충전) 시 구입 금액의 6~10%를 김포시가 지원 => 세금 투입 => 투명성 필요(책임을 져야하니까)  
KT의 블록체인 기반 지역화폐 플랫폼 '착한페이'를 통해 발행  

### Unique Electronic Assets  
* 블록체인의 불변성을 사용하여 복제불가능한 디지털 데이터를 생성  
-기존의 데이터는 복제 가능(e.g., MP3) -> 복제된 데이터는 원본과 동일한 효용을 가짐  
-블록체인의 불변성과 스마트 컨트랙트를 사용하면 한번 생성된 데이터와 동일한 데이터를 생성할 수 없도록 제한 가능(e.g., NFTs powered by ERC-721 on Ethereum) -> 희소성 부여  

* Non-Fungible Token(NFT)  
-NFT를 사용하여 중앙화된 관리 없이 각각이 유일한 한정수량 상품을 개발가능  
-상품권, 증명서, 바우처, Collectibles, 수량이 제한되어 있는 게임 아이템 제작 등  
*쪼갤 수도 합칠 수 없는 그 자체로 유일한 토큰*  
*위조, 복제하기 어려움(아예 불가능한건 아님. 너무 어려울 뿐)*  
*유일성이 부여되면 거래가 가능, limited edition*  

#### Crypto Kitties  
*얘 때문에 이더리움 서버가 터졌음. 처음으로 이더리움의 처리량을 넘어선 tx이 들어와서 처리가 안된 것*  
*처음으로 tx이 다음 블록에 안들어가기 시작, 가스비로 경쟁, tx confirm이 안나기 시작*  
* Ethereum 기반의 가상 펫 육성 게임  
고양이 캐릭터를 수집하고 교배  
Ether(ETH)를 사용하여 캐릭터를 거래 가능  
각각의 캐릭터는 NFT로 발급되어 희소성을 가짐 -> 수집 가치를 가짐  
* 게임성이 강조된 수집형 DApp  
Dragon이라는 CryptoKitty는 600 ETH(당시 170,000 USD)  
교배를 통해 새로운 Kitty를 생성(새로운 NFT)  
교배시마다 부모의 성질 + 무작위로 부여되는 새로운 성질 추가  
- - -


- - -

### 더 알아볼 것들  

* 리브라  
* DRM  
* ERC20 == Fungible Token  
> 토큰 1과 토큰 1이 있음 둘은 비교 못함. 합치면 토큰 2가 됨. 토큰 2가 어떻게 만들어진건지는 알 수 없음 그냥 1+1 해서 나온거니까.  
> 토큰 2를 또 쪼갤 수 있음 토큰 0.5와 토큰 1.5 -> 쪼갤 수도 합칠 수도 있는 토큰  




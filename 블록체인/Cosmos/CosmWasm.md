# Cosmwasm  
이화체인 1인1체인-코스모스 강의 준비기  
[강의자료](https://www.inflearn.com/course/dsrv-dev-playground)  
intellij  
rust 설치(https://webnautes.tistory.com/1663); Cargo 자동 설치됨  


## CosmWasm이란?  
### Wasm?  
웹 어셈블리; 최신 웹 브라우저에서 실행할 수 있는 새로운 유형의 코드    
네이티브에 가까운 성능으로 동작하며 컴팩트한 바이너리 포맷을 제공하는 저수준 어셈블리 언어  
여러가지 언어를 가지고 하나의 바이트코드 포맷으로 컴파일이 가능하다.  

ex) Figma가 웹 어셈블리(Wasm)로 돌아가는 대표적 사례  

### CosmWasm?  
- 코스모스 계열의 Wasm; 코스모스 SDK 위에서 웹 어셈블리 바이트코드를 실행할 수 있는 모듈; 코스모스 생태계 스마트컨트랙트 플랫폼  
- 웹 어셈블리라는 바이트코드를 이용해서 컨트랙트를 짠다!  
- 기본적으로 코스모스 SDK에 플러그인을 할 수 있는 모듈로 작성이 되어 있다.  
- Different Chain에 Same Smart Contract; 멀티체인 솔루션 -> 호스트하는 애플리케이션(여기서는 체인)에 제약을 거의 주지 않음  
- 코스모스는 IBC(Inter-Blockchain Communication), Cosmwasm은 Inter-Blockchain Contract라고 볼 수 있음.  
- 한 체인의 코드가 다른 체인에서 트랜잭션을 생성할 수 있게 함  
- 액터모델을 가지고 IBC 사용  
- IBC에 있는 블록체인 간 소통을 할 수 있는 메세지를 보낼 수 있는 어떤 형태
- 라이브러리 지향  
- 다양한 언어에서 Cosmwasm을 사용하는 것이 목표  

## 스마트컨트랙트 배포 방법  
<img width="794" alt="image" src="https://user-images.githubusercontent.com/46364778/201458399-57e8cbb2-9c28-49ca-80de-e5c03a060d58.png">  

1. 컴파일한 바이트 코드를 블록체인상에 업로드  
2. 올려진 바이트 코드를 가지고 컨트랙트라는 인스턴스를 만들어야함(instantiate)  
3. 인스턴스된 하나의 컨트랙트를 실행함  

**이더리움**  
모든 컨트랙트는 싱글톤한 오브젝트와 다를 바가 없음.  
각각의 컨트랙트는 각각의 바이트코드만 가지고 있고 서로 공유할 수 없음. -? 재사용이 안됨  

**코스모스**  
컴파일한 바이트코드를 업로드하는 것, 새로운 컨트랙트를 그 바이트코드를 이용해서 인스턴스화하는 과정이 분리되어 있음.  
서로 다른 컨트랙트가 같은 바이트코드 공유가 가능.  
따라서 컨트랙트를 인스턴스화 하기 위해서는 기존에 올라온 바이트코드를 알고 있어야함.  

## 강의 내용 메모  
### 1편  
proper_initialization  

instantiateMsg  
메세지 받아서 컨트랙트를 새로 인스턴스 생성
와즘D(코스모스 구현체) 해당 메세지의 초기값을 활용해서 컨트랙트의 상태 초기화  
하나의 바이트 코드를 가지고 서로 다른 상태를 가지는 서로 다른 인스턴스를 만들 수 있음.  
하나의 클래스 파일을 공유  
ㅁ따라서 컨트랙트를 인스턴스화 하기 위해서는 기존에 올라온 바이트코드를 알고 있어야함.  
라서 컨트랙트를 인스턴스화 하기 위해서는 기존에 올라온 바이트코드를 알고 있어야함.  

instantiate  
DepsMut; 다른 언어에서의 Context, 이걸 통해 다른 컨트랙트를 호출할 수 있는 API  
Env; 블록이랑 컨트랙트 관련된 정보  
MessageInfo; sender의 주소, sender에 대한 정보  
InstantiateMsg; 카운터가 몇이냐 등  

빈 response 구조체에 attribute 추가해서 구조체 구성  

State 구조체  
count; 32 bytes. 
owner; 컨트랙트를 만든 소유자의 주소  

Serde  
바이너리 형태로 표현이 되는, 직렬화, 역직렬화 관련 모듈  

그외 코즈모즘에서 제공하는 자료구조들  

STATE로 export, Item으로 State구조체 래핑, item은 단일 원소를 저장하는데 사용  

코슴와즘은 컨트랙트가 업그레이드가 가능하다는걸 전제로 깔고 있어서 이게 몇버전이냐 명시할 수 있도록 함  
cargo파일을 env 파일로 주입  

사용자가 메세지 객체를 보내오면 컨트랙트가 어떠한 함수를 실행할지 명시해줘야 함  
ExecuteMsg를 통해 가능; 실행을 해서 블록체인 컨트랙트의 상태값을 바꿀 수 있는 함수의 실행들을 명시  
executemsg를 받으면 컨트랙트에서 execute 실행  

try_increment  
DepsMut; 컨트랙트 스토리지에 접근할 수 있는 mutable한 참조를 갖고 있다. 상태를 변경할 수 있는 참조를 넘겨주고 있음  
상태 업데이트: STATE.update  

클로저는 단 한번만 실행됨  
OK로 리턴을 시켜줌  

reset
ExecuteMsg에 Reset  
컨트랙트 주인만 reset 가능  
unauth_info / auth_info  

try_reset  
실행하는 주체에 대한 validation  

컨트랙트를 execution해서 상태를 바꾸는 얘기  
이제는 컨트랙트에 쿼리보내는 얘기  

쿼리는 컨트랙트 상태 변경 X, 데이터를 store할 수 없음. 별도의 가스비를 receive하지 않음  
mgs.rs -> QueryMsg  
프론트에서 보낼 때는 snake_case로 보내게 존중해 주겠다.  

query  
query_count  
count값 추출하기 위해 STATE에서 로드  
DepsMut와 Deps의 차이  
execute에서는 DespMut를 넣고 query에서는 Deps를 넣음; immutable한 컨택스트 공유, 스토리지 읽기는 가능한데 쓰기는 안됨  

### 2편  
counter contract 배포  
당연히 컴파일부터!  
wasmd, cargo, rustup이 설치가 되어있어야함  

```rustup default stable```
어떤 stable 채널에 어떤 툴체인으로 컴파일 진행할건지 지정  
툴체인 -> 러스트의 컴파일러(stable, beta, nightly 3가지 버전있음)  
stable이 최근에 릴리즈해서 사용할 것  
rustup은 다양한 버전을 관리할 수 있는 패키지  

cargo; 러스트패키지 매니저; 를 이용해서 의존성 다운로드, 라이브러리 다운로드, 컴파일, 빌드 실행할 수 있음  
```cargo wasm```; config 옵션에 따라 명령어 수행하게 됨, 이 명령으로 빌드할 수 있게 됨 -> cargo.toml 파일 참고  
```rustup target add wasm32-unknown-unknown```  
cargo.toml  
relase 플래그를 붙이면 cargo에서 toml에서 지정한 값들이 설정이 됨  

컴파일된거 보려면 target가면 됨  
```cd taget/wasm32-unknown-unknown/release```에 cosmwasm.wasm이 있음  
```ls -lh```  
1.8M를 올리는건 불가능. 너무 용량이 큼. 용량을 줄일 필요가 있음  

rust compile 최적화 하는 방법! -> 가스 비용을 줄이기 위해  
cosmwasm 컴파일 최적화 안하면 exist limit error 이런거 남. 컴파일러는 최적화를 하는게 필수다.  
400KB가 넘어가면 배포가 안됨  

1. rust flag 붙여서 컴파일러보고 사용하지 않는 파일을 지워달라  
다시 cosmwasm 디렉토리로 와서  
``` RUSTFLAGS='-C link-args=-s' cargo wasm```  
rust compiler에게 옵션값 전달해주는건데 바이너리를 striping 해달라. 컴파일 후 쓰지 않는 코드들 삭제함으로써 파일의 용량을 줄여달라  
다시 가서 용량 확인해보면 153K로 용량이 줄어든 것을 확인 가능  

2. [rust-optimizer](https://github.com/CosmWasm/rust-optimizer) 툴 사용  

counter example에 대한 wasm 바이너리 코드를 cosmwasm을 지원하는 블록체인 네트워크를 통해 배포  
1. wasmd -> Go cli를 바탕으로 배포  
2. 프론트엔드 node module 이용해 배포, cosm.js 이용  
3. 오스모시스를 통해 osmosisd로 배포  

**오스모시스**  
코스모스SDK로 만든 AMM  

tesetnet에서 permissoinless로 배포 가능  
테스트넷은 오스모시스의 허가 받지 않고도 배포 할 수 있도록 권한 열어둠  

rust의 환경설정부터! osmosisd 오스모시스 네트워크에 접속할 수 있게하는 것  
```rustup default stable```해서 release 채널 만들기  
```rustup target add wasm32-unknown-unknown``` 와즘을 컴파일 타겟으로 바꿈  
cargo generate랑 run script 깔기 ```cargo install carog-generate --features vendored-openssl```, ```cargo install cargo-run-script```  

오스모시스 테스트넷 환결 설정  
```curl -sL https://get.osmosis.zone/install > i.py && python3 i.py```  
그러면 osmosis node installer가 뜸  

2번 선택. 클라이언트 노드  
2번 테스트넷 선택  
암호 입력  
그럼 설치가 진행됨  
디폴트 로케이션 선택  
노드 이름은 OSMO  
```source ~/.profile```  
(m1은 osmosisd를 사용하려면 https://docs.osmosis.zone/osmosis-core/osmosisd/ 참고)  
실행해서 업데이트 해줌  
osmosisd를 이용해서 월렛을 생성해보도록 하겠음  
```osmosisd keys add wallet```  
새로운 월렛이 추가됨  
오스모(기축코인)이 있어야 이용 가능  
allthatnode에 faucet이 있는데 여기서 테스트넷 address 입력해서 받으면됨(https://www.allthatnode.com/faucet/osmosis.dsrv)  
그럼 트랜잭션 해쉬가 뜸; ping.pub(코스모스 에코시스템 전체 블록체인 익스플로러)에서 확인 -> testnet.ping.pub에서 osmosis 찾아서 해시 입력  
```osmosisd keys show -a wallet```; wallet address 뜸  
```osmosisd query bank balances ${osmosisd keys show -a wallet)``` 그럼 osmo가 주어진 것을 볼 수 있음  

이제 배포를 해보자  
cd target/release 에 있는 cosmwasm.wasm을 배포해볼것  

rpc endpoint가 필요; 블록체인 네트워크에 접속할 수 있게하기 위한 API를 받아 넣어야함  
allthatnode 사이트에 가면 받을 수 있음(https://www.allthatnode.com/nodes.dsrv?protocol=osmosis)  
새로운 프로젝트 만들기 dashboard에서 API key확인 가능  
이걸 가지고 rpc 접근 가능. testnet의 rpc 주소  

release 폴더에서 ```export NODE=(--node {testnet rpc})```  
```echo $NODE``` 해서 확인 가능  
```export TXFLAG=($NODE --chain-id osmo-test-4 --gas-prices 0.025uosmo --gas auto --gas-adjustment 1.3)```  
testnet node id, 가스비 30% 더 낼 수 있음  
```echo $TXFLAG```  
cosmwasm 메인 디렉토리에서 실행  
```RES=$(osmosisd tx wasm store ./target/wasm32-unknown-unknown/release/coswasm.wasm --from wallet $TXFLAG -y --output json -b block)```  
gas estimate이 뜸  
이제 블록체인에 업로드함  

```echo $RES```하면 트랜잭션 receipt이 json형태로 볼 수 있음  
바이트코드를 업로드했기 때문에 코드 id라는걸 알아야함  
지금 바이트 코드가 몇번째 id일지가지고 우리가 인스턴스화 시킬 수 있음. 바이트 코드를 id를 통해 가리켜야 하니까  
```CODE_ID=$(echo $RES | jq -r '.logs[0].events[-1].attributes[0].value')```  
```echo $CODE_ID```이렇게 알 수 있음  

이제 컨트랙트 업로드한걸 알 수 있음.  
컨트랙트 인스턴스를 생성해보고 생성한 인스턴스로 컨트랙트를 실행해보자  

오스모시스d에서 인스턴스 생성하는 법  
1. osmosisd 이용  
2. osmosis gui 이용  

인스턴스를 만들기 위해서 초기화부터 하겠음  
```INIT='{"count":2}'```  
```echo $INIT```  
초기 상태 설정하기 위해 instantiate 명령 실행  
```osmosisd tx wasm instantiate $CODE_ID "$INIT" --from wallet --label "my first contract" $TXFLAG -y --no-admin```  
txhash 등등이 뜸  
ping.pub에서 확인; code_id, label 등 확인  
배포된걸 확인했으니까 배포된 컨트랙트 정보 가져오기  
```CONTRACT=$(osmosisd query wasm list-contract-by-code $CODE_ID --output json | jq -r '.contracts[0]')```  
```echo $CONTRACT```  
컨트랙트 주소알 수 있음  

배포한 컨트랙트 잘 동작하는지 확인해보자  
미디엄 참고
```osmosisd tx wasm execute $CONTRACT "$TRY_INCREMENT" --from wallet $TXFLAG -y --amount 0.01osmo```  
0.01 오스모랑, try increment 명령어를 함께 트랜잭션 날리면서 0.01 osmo 송금한것을 알 수 있음  
이 트랜잭션해시 보면 알 수 있음  


### 3편  



## 출처  
[1편 미디엄](https://bit.ly/3yEcNyx)    
[2편 미디엄](https://abit.ly/dsrv-cosmwasm-counter-deploy)  
[3편 미디엄](http://abit.ly/dsrv-cosmwasm-clicker-game)    









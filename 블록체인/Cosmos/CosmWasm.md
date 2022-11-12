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

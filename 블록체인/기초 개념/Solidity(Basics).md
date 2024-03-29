# Essence of Solidity in Depth  
솔리디티 공식문서에 있는 예제라고 함.  
컨트랙트는 한번 배포하면 끝.(블록체인은 불변하니까.)  
배포한 이후에 변경사항을 적용할 수 있도록 유연하게 만들 수 있음 -> 프록시를 쓴다.  
ex) 1번 기능은 앞으로 다른 컨트랙트에서 관리 합니다. 이렇게!  


## 1. Structure of a Contract  
### Things You can Declare within a Contract  
* State Variables  
> * 블록체인에 영구히 저장할 값들은 상태변수(State Variable)로 선언  
> 어떤 값들은 반드시 State Variable로 선언되어야 함(e.g., mapping)  
> * public 키워드를 사용하여 변수를 외부에 노출가능  
> 이 경우 자동으로 해당 변수 값을 돌려주는 Getter 함수가 생성됨  
```
contract Count {
    uint public count = 0;
    address public lastParticipant;
}
```

* Functions  
> * 함수(Fuction)은 실행 가능한 코드를 정의한 것  
> external, public, internal, private 중 하나로 visibility를 설정 가능  
> payable, view, pure 등 함수의 유형을 정의 가능  
```
contract Count {
  function plus() public {
     count++;
     lastParticipant = msg.sender;
  }
}
```

* Function Modifiers  
> * 함수의 실행 전, 후의 성격을 정의  
> 대부분의 경우 함수의 실행조건을 정의하는데 사용됨  
```
contract Ballot {
    constructor() public { chairperson = msg.sender; }
    address chairperson;
    modifier onlyChair {
        require(msg.sender == chairperson, "Only the chairperson can call this function.");
        _;
    }
    function giveRightToVote(address to) public onlyChair {
        // 'onlyChair' modifier ensures that this funcitno is called by the chairperson
    }
}
```

* Events  
> * 이벤트는 EVM 로깅을 활용한 시스템  
> 이벤트가 실행될 때마다 트랜잭션 로그에 저장  
> 저장된 로그는 컨트랙트 주소와 연동되어 클라이언트가 RPC로 조회 가능  

> Contract
```
contract Ballot {
  event Voted (address voter, uint proposal);
  function vote(uint proposal) public {
    ...
    emit Voted(msg.sender, proposal);
  }
}
```
> Client using caver-js  
event listening
```
const BallotContract = new caver.klay.Contract(abi, address);
BallotContract.events.Voted(
    { fromBlock: 0},
    function(error, event) {
      console.log(event);
    }
).on('error', console.error);
```

* Struct Types  
> * Solidity에서 제공하지 않는 새로운 형식의 자료를 만들 때 사용  
> 여러 자료를 묶어 복잡한 자료형(complex type)을 만들 때 유용  
> 자기 자신은 넣을 수 없다(recursive X)
```
contract Ballot {
  struct Voter {
    uint weight;
    bool voted;
    address delegate;
    uint vote;
  }
}
```
```
contract SocialMedia {
   struct Friend {
     uint id;
     mapping(uint => address) friends;
   }
}
```

* Enum Types  
> * Enum은 임의의 상수를 정의하기에 적합  
> e.g., 상태 => Active, Inactive  
> e.g., 요일 => Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday  
> 바뀌지 않은 값을 정의할 때 유용, 다만 값들의 set이 정해져 있을 때 (finite number value가 있을 때만!)  
> 실제 프로그램 상으로 기록될 때는 숫자로 들어감. 순서대로 0,1,2,3,4,5 .. Enum은 사람보기 좋으라고 쓰는 것.  

```
contract Ballot {
    enum Status {
        Open,
        Closed
    }
}
```


### 2. Data Types  
Solidity는 다음과 같은 자료형(Data Types)을 지원  

1) Booleans(bool)  
2) Integers(int/int8/int16/.../uint256/uint/uint8/uint16/.../uint256). 
3) **Address**  
> 어카운트 주소를 표현(공개키에서 주소 도출하기 위해 해시를 만드는데 해시의 마지막 20바이트가 주소)  
> 블록체인에서 저장할 수 있는 공간이 제한되어 있으니까 주소를 위한 자료형을 타이트하게 만든 것.(제일 많이 쓰는 자료형은 해시를 저장하기 위해 쓰는 32byte, 두번째가 주소!)  
> * Klaytn 주소의 길이는 20바이트; address => bytes20 형변환 가능  
> * **address vs. address payable**  
> address payable -> address (O)  
> address => address payable (X); uint160을 거쳐서만 가능  
> contract가 특정 주소에 클레이를 주거나, 특정 주소가 다른 주소에게 클레이를 주거나. 실제 클레이가 오고 갈 수 있는 기능은 address payable만 가능.  
> 실수로 어떤 주소에 보내는걸 막기 위해.  

4) **Fixed-size byte arrays (bytes1, bytes2, ..., bytes32)**  
5) **Reference Types**  
```
uint x = 10;
uint y = x;
string memory s = "Hello, world";
string memory t = s;
```
<img width="536" alt="image" src="https://user-images.githubusercontent.com/46364778/190934582-9e02150d-dd10-4cd9-94f3-8eb9dfe6790b.png">  

> * Reference Type은 structs, arrays, mapping과 같이 크기가 정해지지 않은 데이터를 위해 사용(데이터 사이즈가 바뀔 수 있으니까)  
> Value Type들은 변수끼리 대입할 경우 값을 복사  
> Reference Type들은 (같은 영역을 사용하는) 변수끼리 대입할 경우 같은 값을 참조  

#### Reference Type  
데이터는 저장되는 위치를 반드시 명시  
* memory : 함수 내에서 유효한 영역에 저장(storage가 아니면 무조건 메모리, 휘발성 데이터)  
* storage : state variables와 같이 영속적으로 저장되는 영역에 저장(상태변수들이 storage. 상태변수들은 전부 블록체인에 기록되잖아. 블록체인에 기록되는 값들을 storage라고 함)  
* calldata : external 함수 인자에 사용되는 공간  

> * 서로 다른 영역을 참조하는 변수 간 대입이 발생 시 데이터 복사  
> storage => memory / calldata  
> anything => storage  

6) **Arrays**  
Javascript에서 배운 배열과 개념은 같으나 사용법이 상이(+다른 언어의 array list랑 비슷함. +storage로 사용되는 array는 크기가 바뀔 수 있음, memory는 크기 고정해야함)  
> * State Variable로 사용할 때(i.e., 저장공간 = storage)  
>> T[k] x; => k개의 T를 가진 배열 x 선언  
>> e.g., uint[5] arr; => arr은 5개의 uint를 가진 배열; arr[0] => 첫번째 uint  
>> T[] x; => x는 T를 담을 수 있는 배열; x의 크기는 변할 수 있음(dynamic size)  
>> T[][k] x; => k개의 T를 담을 수 있는 dynamic size 배열 x를 선언  
>> T[][k] x가 주어질 때 x[i][j]는 (i-1)번째 배열의 (j-1)번째 T를 불러옴  
> * 모든 유형의 데이터를 배열에 담을 수 있음  
> mapping, struct 포함  

#### More on Arrays  
* .push(T item) and .length  
> .push(T item)은 배열에 데이터를 추가  
> .length는 배열크기를 반환  

* 런타임에 생성되는 (i.e., 함수 내에서) memory 배열은 new 키워드를 써서 선언  
> storage 배열과는 달리 memory 배열은 dynamic array 사용이 불가  
```
contract C {
    function f(uint len) public pure {
        bytes memory b = new bytes(len);  
        assert(b.length == len);  
    }
}
```  
한번 정해진 사이즈는 못바꿈. 더 큰 사이즈의 배열을 만들고 싶으면 배열 하나 더 만들고 예전 배열에서 복사해오기. 옛날 배열은 delete.  
delete 배열은 배열 안에 있는 값을 지우는것. 배열 자체를 지우지 않음.  

#### bytesN vs. bytes/string vs. byte[]
* (가능하면) 언제나 bytes를 사용  
byte[]는 배열 아이템 간 31바이트 패딩이 추가됨  
* 기본 룰  
임의의 길이의 바이트 데이터를 담을 때는 bytes  
임의의 길이의 데이터가 UTF-8과 같이 문자로 인코딩 될 수 있을 때는 string  
바이트 데이터의 길이가 정해져있을 때는 value type의 bytes1, ..., bytes32를 사용  
byte[]는 지양  

7) **Mapping Types**  
```mapping (K => V) table;```  
* 해시테이블과 유사, 배열처럼 사용  
storage 영역에만 저장 가능 (i.e., state variable로만 선언 가능) -> 즉 함수 안에서 변수 선언 못함. 밖에서 선언하고 함수 안에서 참조, 데이터 추가  
함수 인자, 또는 public 리턴값으로 사용할 수 없음  
```
contract MappingExample {
    mapping(address => uint) public balances;
    
    function update(uint newBalance) public {
        balances[msg.sender] = newBalance;
    }
}
```

8) Contract Types  


### 3. Special Variables and Functions  
1) Block and Transaction Properties  
<img width="907" alt="image" src="https://user-images.githubusercontent.com/46364778/190935435-4a73bffb-2392-40de-97ff-05fffdb7e690.png">  

blockhash -> 머클트리(exist 체크, inexist는 체크 어려움) 역산할 때 필요  
block.number -> 몇번째 블록을 때리면 뭘 하자 이럴 때. 어려움. 컨트랙트는 자기 자신을 호출하는 매커니즘이 없어서 외부에서 누가 계속 queue를 주긴 해야함. 컨트랙트가 그 때마다 wake up 됨.  
gasleft() returns -> 다른 컨트랙트의 무언가를 호출하려할 때 중요. 보통 한 컨트랙트 안에서 다 핸들링 다 되는데, 그럴 때가 있음. 가급적 컨트랙트는 간결하게 짜서 가스량 체크 안해도 되는게 좋음  
컨트랙트의 함수가 실행되고 있는데 그 시점의 그 함수를 실행한 사람: msg.sender  
함수의 identifier: msg.sig(보통 함수명을 해시해서 앞의 4바이트)  
tx에 담겨진 클레이의 양: msg.value  
tx는 한번 발생했을 때 다른 tx 일으킬 수 있음(internal tx). 그래서 최초의 tx을 누가 실행 했는지: tx.origin  


2) Error Handling  
* assert(bool condition):  
condition이 false일 경우 실행 중인 함수가 변경한 내역을 모두 이전 상태로 되돌림(로직 체크에 사용)  
* require(bool cond):  
condition이 false일 경우 실행 중인 함수가 변경한 내역을 모두 이전 상태로 되돌림(외부 변수 검증에 사용)  
* require(bool cond, string memory message):  
require(bool)과 동일. 추가로 메세지를 전달.  

3) Cryptographic Functions  
해싱을 하면 가스비가 많이 듦. 오프체인에서 해시 다 하고 그 값을 전송하는게 쌈.  
* keccack256(bytes memory input) returns (bytes32)  
주어진 값으로 Keccak-256 해시를 생성  
* sha256(bytes memory input) returns (bytes32)  
주어진 값으로 SHA-256 해시를 생성  
* ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)  
서명(v, r, s)으로부터 어카운트 주소를 도출(서명 => 공개키 => 주소)  

### 4. Expressions and Control Structure  
* Solidity 제어 구문  
대부분의 프로그래밍 언어가 지원하는 제어 구문을 지원  
if, else, while, do, for, break, continue, return  

* 예외 처리 기능이 없음  
i.e., try-catch 없음  
즉, 모든 함수는 실패하면 안된다..ㅋㅋㅋ  

### 5. Contracts  
#### Creating Contracts  
* 일반적인 컨트랙트를 생성 => 배포  
* 컨트랙트를 클래스처럼 사용  
컨트랙트를 객체지향 프로그래밍에서 사용하는 클래스로 취급할 수 있음  
new 키워드를 사용하여 컨트랙트를 생성하여 변수에 대입  
<img width="778" alt="image" src="https://user-images.githubusercontent.com/46364778/190936380-e99c7b0f-5dce-4510-8b8d-c5f4217fa7a7.png">  

이렇게 컨트랙트를 만들어서 상태변수로 집어넣을 수도 있고, 이미 만들어져 있는 컨트랙트를 참조할 때는 address를 컨트랙트로 쓸 수도 있음.(그 컨트랙트의 abi를 알고 있을 때만)  

#### Visibility and Getters  
함수의 visibility(공개정도)를 목적에 맞게 설정  
* external  
다른 컨트랙트에서 & 트랙잭션을 통해서 호출 가능  
internal 호출 불가능 (i.e., f()는 안되지만 this.f()는 허용됨, 마치 외부에서 부르는 것마냥 accessor를 이용해야함)  

* public  
트랜잭션을 통해 호출 가능, internal 호출 가능  

* internal  
외부에서 호출 불가능, internal 호출 가능, 상속는 컨트랙트에서 호출 가능  

* private  
internal 호출 가능  

#### Function Modifiers: pure vs. view  
함수 제약을 설정하여 정해진 scope에서 동작할 수 있도록 설정  
* pure  
State Variable 접근 불가 i.e., READ (X), WRITE (X)  
블록체인 이 함수를 올바르게 실행했다 라는 걸 볼 수 있음  
ex) 평균 내는 함수. 상태변수랑 상관도 없고 변경 못하게 pure로 올려서 로직만 실행  

* view  
State Variable 변경 불가 i.e., READ (O), WRITE (X)  

* (none)  
제약 없음 i.e., READ (O), WRITE (O)  

#### Fallback function  
따지자면 default 함수  

* 컨트랙트에 일치하는 함수가 없을 경우 실행 (i.e., no input/calldata)  
단 하나만 정의 가능 & 함수명, 함수 인자, 반환값 없음  
반드시 external로 선언  

* 컨트랙트가 KLAY를 받으려면 payable fallback function이 필요  
payable fallback이 없는 컨트랙트가 KLAY를 전송받으면 오류 발생  

```
contract Escrow {
    event Deposited(address sender, uint amount);
    // fallback function
    function() external payable {
        emit Deposited(msg.sender, msg.value);
    }
}
```

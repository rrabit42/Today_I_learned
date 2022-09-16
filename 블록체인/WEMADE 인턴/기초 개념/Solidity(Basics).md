# Essence of Solidity in Depth  
솔리디티 공식문서에 있는 예제라고 함.  

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
### 3. Special Variables and Functions  
### 4. Expressions and Control Structure  
### 5. Contracts  

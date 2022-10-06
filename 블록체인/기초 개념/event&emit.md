# 이벤트(event) 방출(emit)이 필요한 이유  

```
pragma solidity ^0.8.6;

contract EventTestContract {
  
  mapping(address => uint256) private _counts;
  
  event Increase(address indexed addr, uint256 oldValue, uint256 newValue);
  
  function increaseWithEvent() public returns (bool) {
    uint256 oldValue = _counts[msg.sender];
    _increase();
    uint256 newValue = _counts[msg.sender];
    emit Increase(msg.sender, oldValue, newValue);
    return true;
  }
  
  function increaseWithoutEvent() public returns (bool) {
    _increase();
    return true;
  }
  
  function _increase() private {
    _counts[msg.sender] += 1;
  }
  
  function count() public view returns (uint256) {
    return _counts[msg.sender];
  }
}
```  

increaseWithoutEvent, increaseWithEvent, count 등 3개의 외부에서 호출 가능한 함수가 있다.  
increaseWithoutEvent와 increaseWithEvent 함수 모두 하는 일은 내부에 mapping으로 주소별로 저장되는 count를 증가시키는 것.  
count함수는 증가된 count를 확인하기 위한 함수.  
차이는 increaseWithEvent에서만 increase 이벤트를 방출(emit)하는 것임.  

먼저 Remixx에서 컴파일하고 Rinkeby 테스트넷에 배포해서 호출해 보았다.
다음은 Rinkeby Etherscan에서 확인해 본 호출별 결과.  
<img width="669" alt="image" src="https://user-images.githubusercontent.com/46364778/194190049-ad7edab7-d2cb-4752-a45a-8e01602d5249.png">  


increaseWithEvent 호출 결과에서만 Logs 탭을 확인할 수 있고, Logs 탭을 눌러보면 다음과 같은 상세 내용을 확인할 수 있음.  
<img width="666" alt="image" src="https://user-images.githubusercontent.com/46364778/194190002-ffc5caa6-4089-4daf-9a4a-535d166e80ca.png">  

Data 부분에서 1에서 2로 증가한 값(increaseWithoutEvent 다음에 호출했기 때문에 초기값이 1)을 확인할 수 있고, Increase 이벤트에 addr 매개변수는 indexed로 지정되어 있어 etherscan에서 검색 시 필터로 사용이 가능.  

다른 차이는 increaseWithEvent 함수가 gas를 약간 더 사용하는 것, opcodes가 조금이라도 더 많으니 어쩔 수 없는 부분!  
따라서 event는 로그를 남기기 위해서 반드시 사용해야 하지만, 사용자에게 불필요한 gas를 부담시키지 않으려면 꼭 필요한 부분에만 사용하는 것이 바람직함.  


## 출처  
https://hichoco.tistory.com/entry/Solidity%EC%86%94%EB%A6%AC%EB%94%94%ED%8B%B0-%EC%9D%B4%EB%B2%A4%ED%8A%B8event-%EB%B0%A9%EC%B6%9Cemit-%ED%95%98%EA%B3%A0-%EC%95%88%ED%95%98%EA%B3%A0-%EC%B0%A8%EC%9D%B4  


# EVM Revert에 대한 고찰  

## Transaction Gas비 부족  
황당 그 잡채.
klaytn, caver 등등 공식 문서랑 github이랑 코드 다 뜯어봐도 하라는 대로 했는데 왜 transaction을 보내면 evm revert가 뜨는지 멘붕오고 의문이었는데. 
(심지어 블록체인 쪽 플젝 대부분 코드는 interface만 나와있고 내부 코드는 비공개여서 더 미치도 팔짝 뛸 노릇이었음). 
가스 양이 부족해서였을 때 심정을 서술하시오^^ 가스 양을 적당히 크게 늘려주었을 때 아주 잘 동작하더이다.  
EVM은 트랜잭션 처리에 필요한 가스양이 부족하면 바로 revert 에러를 반환한다고 한다.(아니 그럼 가스양 부족하다고 에러를 주면되잖아 이 코드 하나 추가하기가 그렇게 어렵니..?). 

아무튼 그런 김에 gas에 대해 더 공부해보면 좋을 것 같다!  
트랜잭션 실행을 완료하는데 필요한 gas 추정치를 계산하여 돌려주는 [caver 함수](https://ko.docs.klaytn.foundation/dapp/sdk/caver-js/api-references/caver.rpc/klay#caver-rpc-klay-estimategas)도 있으니 참고하자.  
실무에서는 해당 함수로 gas 양을 넣는다. 왜냐하면 트랜잭션이 반드시, 최대한 빠르게 완료가 되어야 하므로!  

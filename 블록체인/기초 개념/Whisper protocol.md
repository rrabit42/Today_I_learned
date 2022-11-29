# Whisper Protocol  
이더리움 전 CTO, 개빈 후드가 이더리움 초기 개발 단계에 댑(DApp) 간의 커뮤니케이션을 돕기 위해 설계한 메세징 프로토콜(Decentralized messaging protocol)  
2014.10.12에 위스퍼 프로포셜(Whisper Propasal)을 시작으로 꾸준히 업데이트, 현재 Go Ethereum 기준 위스퍼 v6이 가장 최신 버전  

* Ethereum P2P protocol 상에서 유저간 암호화 메세지 송수신 프로토콜  
* Ethereum blockchain과 동일한 네트워크 사용, whisper node 간 모든 whisper 메세지를 공유함  
* 공유되는 메세지는 암호키를 갖고 있는 수신자만 확인 가능함(메세지 자체는 모든 노드들이 공유하지만, 공유된 키가 있어야만 메세지를 송신할 수 있음=내용을 읽을 수 있다는 말 같음)  
* 1:1 또는 1:N 메세지 전송 가능   

> Dapp 상호 작용을 위한 P2P 커뮤니케이션 프로토콜, High level 프로토콜  
> 수신인, 발신인에 대한 완벽한 익명성 제공 X  
> 메세지 지속 시간(Time to Live) 설정 X  
> 저장된 값은 만료될 때까지 시스템에 존재, 저장된 값을 확인하는데 더 용이  
> 네트워크에 참여하는 노드들에 메세지를 보내고, 새로운 메세지는 새로운 노드가 네트워크에 참여하더라도 만료될 때까지 복제됨  

### Whisper 메세지 구조  
* 송신자는 목적지 주소로서 PublicKey 또는 SymKeyID를 가지고 있어야 하며, Topic, payload 메세지를 삽입하여 전송함  
* 수신자는 자신의 노드에 filter를 설정해둠으로써 PublicKey에 해당 되는, 또는 SymKeyID 매칭되는 메세지를 수신하여 callback function을 호출함  

### 장점  
* 메세지 지속 시간 내에 지속적인 메세지 전파로 메세지 수신자/발신자를 숨김  
* 가십(?)을 이용한 릴레이(relay) 방식으로 메세지 전송 중에 수신인, 발신인, 메세지 내용 등의 메타 데이터와 ip, 패킷을 100% 숨길 수 있음  
* 수신인의 SECP-256k1 공개키로 메세지 암호화, 수신인은 개인키로 복호화하는 신원 기반 메세징 시스템 -> 신뢰성 높음  

### 단점  
* 대규모 데이터 처리 어려움. 64KB 이하 처리에 최적화되어 있기 때문에 라이팅 되어(?) 시간에 민감한 어플리케이션에서 사용 어려움  

## 출처  
* http://wiki.hash.kr/index.php/%EC%9C%84%EC%8A%A4%ED%8D%BC_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C  
* https://medium.com/onther-tech/ethereum-whisper-%EB%85%B8%EB%93%9C-%EA%B5%AC%EC%B6%95-%EB%B0%8F-%ED%85%8C%EC%8A%A4%ED%8A%B8-1b04fa8b9abf  

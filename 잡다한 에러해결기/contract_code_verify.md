# Verifying Contract code  
remix ide나 klaytn ide로 배포하면 etherscan, klaytnscope 등등에서 contract verify를 따로 해줘야한다.  
> contract에 verify를 반드시 해줄 필요는 없는 것 같은데 우리가 구현하는 서비스는 contract를 불러오려면 해당 컨트랙트가 코드 verify가 되어 있어야 했다.  

openzepplin을 상속한 경우, 사이트에서 직접 하면 verify가 되지 않는다!  
처음으로는 Openzepplin을 상속할 수 없다고 하고,  
코드를 [flatten](https://forum.openzeppelin.com/t/verify-erc20-token-on-etherscan-that-was-deployed-through-remix-step-by-step-guide/9051)해서 하려고 하면 ABI가 불일치한다고 뜬다.  
(flattern 하면 ERC20과 내 토큰 contract 이렇게 2개 ABI 가 생성되는데 배포할 때 ABI는 1개 뿐이니 그런건가 싶다.)  

그래서 결국 vscode를 키고, 환경 세팅부터 배포까지 하게 되었다.(결과적으로는 더 많은 공부가 되었다.)  
배포는 hardhat 프레임워크를 이용해서 하게 되었고 [hardhat tutorial](https://hardhat.org/tutorial)을 보면서 컨트랙트를 쓰고, 테스트하고, 배포했다.  

hardhat으로 배포를 해도 verify는 따로 해줘야하는데, 다행히도 hardhat에 [verify]((https://hardhat.org/hardhat-runner/plugins/nomiclabs-hardhat-etherscan) 명령어가 있어 손쉽게 할 수 있다.  
hardhat이 이더리움과 폴리곤은 코드 verify까지 지원하는데, klaytn은 지원하지 않는다!!! 아악 (이더스캔으로 verify를 하는데 이더리움, 폴리곤은 이더스캔을 사용하는데 klaytn은 아니라서!)  

hardhat.config.js에서 etherescan 키에 customChains를 해줘도 ~~당연하게도~~ 동작하지 않는다.  
klaytn은 결국 다시 ide + flatten을 이용했는데 klaytnscope에서 여러번 rejected 뜨다가 컴파일러 버전을 최신으로 변경해줬더니 갑자기 success 떴다! ㅇㅅㅇ!  

암튼 어영부영 이더리움, 폴리곤, 클레이튼 기반 토큰 배포 성공!  

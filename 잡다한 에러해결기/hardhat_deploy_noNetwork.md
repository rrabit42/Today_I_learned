# Hardhat와 Alchemy 이용해서 배포 시 발생하는 noNetwork 에러 해결기  
[Hardhat Tutorial](https://hardhat.org/tutorial/)을 따라하며 코인을 배포하려고 하는데 아래와 같은 에러가 발생했다.  
```npx hardhat run --network gerli scripts/deploy.js```  
<img width="1144" alt="image" src="https://user-images.githubusercontent.com/46364778/194987135-f50023ff-c996-4b68-988e-5d4bb15bd81a.png">  

hardhat,config.js에서 alchemy_api_key와 메타마스크 지갑 private_key 설정도 잘 해줬고, network는 goerli 사용한다고 설정해줬는데 해당 에러가 나서 답답했다.  
튜토리얼과 같이 goerli 설정에는 accounts와 url만 있었는데 chainId를 추가해줬다.(goerli 테스트넷 chainId는 5)  
```
module.exports = {
  solidity: "0.8.17",
  // defaultNetwork: "goerli",
  networks: {
    goerli: {
      url: `https://eth-goerli.alchemyapi.io/v2/${ALCHEMY_API_KEY}`,
      accounts: [GOERLI_PRIVATE_KEY],
      chainId: 5
    }
  }
```  
그 후 다시 배포를 하려고 하면 self_signed_cert_in_chain 에러가 발생한다.  
<img width="537" alt="image" src="https://user-images.githubusercontent.com/46364778/194987684-06a77466-7ade-4336-99ce-a1bcd1d58e15.png">  

해당 에러는 구글링하면 여러 해결법이 나오는데 나는 ```export NODE_TLS_REJECT_UNAUTHORIZED='0'``` 명령어를 통해 TLS 증명서 검증을 꺼주고(?) 진행했다.  
그러면 튜토리얼에서 말한대로 배포가 아주 잘된다!  

## 오류 발생 원인?  
같이 인턴하는 언니가 typescript로 어제 bobab(클레이튼 테스트넷, 이더리움 기반)에 배포했을 때는 url만으로 잘된다고 했다.  
그걸 고려했을 때 javascript에서는 url 만으로는 goerli와 연결이 잘 안되는건가 싶다. chainId는 사실상 옵션인데 이걸 넣어주고 나서야 goerli 네트워크를 찾는 걸 보면 chainId를 꼭 넣어줘야 할 듯하다.  



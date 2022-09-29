# FT와 NFT  
Fungible Token(대체 가능 토큰): 블록체인 네트워크에서 누구나 발생할 수 있는 지역 화폐  
Non Fungible Token(대체 불가 토큰): 유니크한(유일한) 토큰  

인터넷에서 RFC(Request for Comment)라고 하면 개인 혹은 단체가 어떠한 새로운 아이디어를 제시하고 여러 사람들의 동의를 얻으면 '인터넷 표준'이 되는 것을 뜻함.  
마찬가지로 '이더리움'에서도 누군가가 새로운 아이디어를 제시하고 동의를 얻으면 '이더리움 표준'이 되고 그것을 ERC(Ethereum Request for Comment)라고 한다.  
그 것을 'ERC-N'이라고 부르며 현재까지도 새로운 안건이 추가적으로 올라오고 채택되고 있음.  

> Note that there’s a big difference between having two voting rights and two deeds of ownership:  
> each vote is equal to all others, but houses usually are not! This is called fungibility.  
> Fungible goods are equivalent and interchangeable, like Ether, fiat currencies, and voting rights.  
> Non-fungible goods are unique and distinct, like deeds of ownership, or collectibles.  
> In a nutshell, when dealing with non-fungibles (like your house) you care about which ones you have, while in fungible assets (like your bank account statement) what matters is how much you have.    


## FT  
예를 들어, 이더리움 네트워크를 사용하는 개발자는 이더리움 메인넷을 기반으로 하는 본인만의 플랫폼(Dapp)과 화폐 (FT) 를 개발할 수 있다.  
더 쉬운 예를 들어보자. 넥슨 안에는 여러 게임들이 있고 그 여러 게임들 안에서 통용되는 포인트, 화폐들은 다 다르다.  
그 게임들은 넥슨이라는 커다란 네트워크안에 속해 있고 넥슨이 존재하지 않는다면 게임이 존재하지 않게 되는 것이고 포인트들은 사용이 불가하게 될 것이다.  
여기서 넥슨은 이더리움에 대입되는 존재이고 게임은 개발자가 개발하는 플랫폼(Dapp), 그 플랫폼 내에서 통용되는 포인트는 개발자가 발행가능한 FT가 된다.  

이더리움 내에서 발행 가능한 FT 표준: **ERC-20**  
EIP-20 토론으로부터 생성되었으며, 깃허브와 Web3.js를 통해 인터페이스를 제공한다.  
ERC-20은 이더리움 기반의 가상 머신인 EVM(Ethereum Virtual Machine)에서 사용되는 언어 Solidity로 제공된다.  
ERC-20을 이용하면 Fungible Token(대체가능한 토큰; 지역화폐)을 발행할 수 있다.  
ERC-20의 대표적인 토큰은 Uniswap($UNI)이다.  

> fungible tokens:  
> any one token is exactly equal to any other token; no tokens have special rights or behavior associated with them.  


## NFT  
화폐처럼 동일한 토큰이 재발행될 수 없다. NFT는 발행되는 각 토큰마다 유일한 특징을 갖고 있다.  
쉽게 말하면 NFT는 미술품이다. 미술품은 각 작품마다 유일한 특징이 있다.  

이더리움 내에서 발행 가능한 FT 표준: **ERC-721**  
EIP-721 토론으로부터 생성되었으며, 깃허브와 Web3.js를 통해 인터페이스를 제공한다.  
ERC-721은 이더리움 기반의 가상 머신인 EVM(Ethereum Virtual Machine)에서 사용되는 언어 Solidity로 제공된다.  
ERC-721을 이용하면 Non-Fungible Token(대체 불가능한 토큰; NFT)을 발행 가능하다.  
ERC-721의 대표적인 토큰은 CryptoPunks가 있다.  


## ERC-20, ERC-721. 
이 두가지 표준으로 발행된 '토큰' 즉, 블록체인 상에서의 'Smart Contract'에 의해 발행된 토큰이기 때문에 일반적인 '토큰'과 달리 '블록체인 토큰'으로 구분지어 생각할 수 있음.  
ERC-20과 ERC-721 둘 다 어떠한 기초 자산에 대한 가치와 권리를 대신하여 나타내는 '토큰'에 대한 표준인 것은 동일.  
그렇지만 ERC-721 표준으로 발행한 '토큰'은 하나하나에 고유한 ID가 부여되어 있는 반면,
ERC-20로 발행한 '토큰'은 고유한 ID가 부여되지 안힉 떄문에 모두가 같은 '토큰'으로 취급되는 차이가 있다.  
따라서 FT의 경우 현금, 금, 기름과 같은 동일한 양이면 같은 가치를 가지는 성격을 지녔기 때문에 화폐인 '코인'으로 사용되기도 한다.  

ERC-20과 ERC-271의 큰 차이는 ‘토큰’에 고유한 ID가 부여되어 있는지 여부이며 그에 따라 실제 사용시에도 차이가 발생한다.  
‘Mint(발행)’할 때, ERC-20의 경우 발행할 ‘토큰’의 양을 입력 값으로 갖는 반면 ERC-721은 특정ID ‘토큰’ 하나(토큰 ID를 입력 값으로 받음)에 대해 작업을 수행.  
마찬가지로 ‘Transfer(전송)’과 ‘Burn(소각)’ 또한 ERC-20은 ‘토큰’의 총 양(amount)을 입력하는 반면 ERC-721은 특정ID ‘토큰’에 대해서만 작업을 수행.  


https://docs.openzeppelin.com/contracts/4.x/tokens#different-kinds-of-tokens  
https://velog.io/@chlvlftn22/FT-%EC%99%80-NFT  
https://www.2e.co.kr/news/articleView.html?idxno=302178  


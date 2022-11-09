# EVM Revert에 대한 고찰  

## Transaction Gas비 부족  
황당 그 잡채.
klaytn, caver 등등 공식 문서랑 github이랑 코드 다 뜯어봐도 하라는 대로 했는데 왜 transaction을 보내면 evm revert가 뜨는지 멘붕오고 의문이었는데  
(심지어 블록체인 쪽 플젝 대부분 코드는 interface만 나와있고 내부 코드는 비공개여서 더 미치도 팔짝 뛸 노릇이었음)  
가스 양이 부족해서였을 때 심정을 서술하시오^^ 가스 양을 적당히 크게 늘려주었을 때 아주 잘 동작하더이다.  
EVM은 트랜잭션 처리에 필요한 가스양이 부족하면 바로 revert 에러를 반환한다고 한다.(아니 그럼 가스양 부족하다고 에러를 주면되잖아 이 코드 하나 추가하기가 그렇게 어렵니..?)  
> 가스양을 터무니없이? 낮게 줄 때 가스양 부족하다는 에러가 출력되긴 한다..^^ 어처구니..(6000을 주니 에러를 정확히 주고, 60000을 주니 추상적이게 revert 줌ㅋㅋㅋ)  
> ```
> Error: Returned error: intrinsic gas too low
> ```

아무튼 그런 김에 gas에 대해 더 공부해보면 좋을 것 같다!  
트랜잭션 실행을 완료하는데 필요한 gas 추정치를 계산하여 돌려주는 [caver 함수](https://ko.docs.klaytn.foundation/dapp/sdk/caver-js/api-references/caver.rpc/klay#caver-rpc-klay-estimategas)도 있으니 참고하자.  
실무에서는 해당 함수로 gas 양을 넣는다. 왜냐하면 트랜잭션이 반드시, 최대한 빠르게 완료가 되어야 하므로!  

그리고 아까워서 올리는 나의 transaction 전송 삽질기.  
approve가 제대로 안먹는건지, signature 인식이 안되는 건지, 특정 함수에 버그가 있는건지 여러 경우의 수를 생각하며 진짜 다양하게 코드 짜봄^^^ 하하핳  
```
const sendTx = (toAddress, funcAbi) => {
    const tx = caver.transaction.smartContractExecution.create({
        from: wallet.address,
        to: toAddress,
        input: funcAbi,
        gas: '60000' // TODO:
        
    });
    return tx.sign(keyring)
    .then((signedTx)=>{
        console.log("=========SIGNEDTX=======");
        console.log(signedTx);
        return caver.rpc.klay.sendRawTransaction(signedTx); // TODO: 왜여기서 또 revert 생기냐!
    })
    .catch((err)=>{
        return err
    });
}

async function testFunction(toAddress, funcAbi) {
    // Create a value transfer transaction
    const valueTransfer = caver.transaction.smartContractExecution.create({
        from: wallet.address,
        to: toAddress,
        input: funcAbi,
        gas: '500000' // TODO:

    })

    // Sign the transaction via caver.wallet.sign
    await caver.wallet.sign(keyring.address, valueTransfer)

    const rlpEncoding = valueTransfer.getRLPEncoding()
    console.log(`RLP-encoded string: ${rlpEncoding}`)

    const receipt = await caver.rpc.klay.sendRawTransaction(rlpEncoding)
    console.log(receipt)
    return receipt
}

const approve = () => {
    // 1. 원래 하려다가 에러난 함수
    return wemix.send({ from: wallet.address, gas: BigNumber(60000) }, 'approve', gateway._address, tokenAmount);
    // 2. 처음엔 에러난줄 모르고 리팩토링을 했는데 에러남 암튼 두번째 시도
    const approveFuncAbi = caver.klay.abi.encodeFunctionCall({
        "constant":false,
        "inputs":[
            {"name":"spender","type":"address"},
            {"name":"value","type":"uint256"}
        ],
        "name":"approve",
        "outputs":[{"name":"","type":"bool"}],
        "payable":false,
        "stateMutability":"nonpayable","type":"function"
    }, [wallet.address, tokenAmount])
    return sendTx(wemix._address, approveFuncAbi);
    // 3. 그래서 sign하는 부분을 쪼갬
    return wemix.sign({ from: wallet.address, gas: BigNumber(60000) }, 'approve', gateway._address, tokenAmount)
    .then((res)=>{
        console.log(res)
        return caver.rpc.klay.sendRawTransaction(res);
    })
    // 4. 하핳 transaction 객체를 넣으면 안되나 싶어서 RLP encoding도 해봄
    const approveFuncAbi = caver.klay.abi.encodeFunctionCall({
        "constant":false,
        "inputs":[
            {"name":"spender","type":"address"},
            {"name":"value","type":"uint256"}
        ],
        "name":"approve",
        "outputs":[{"name":"","type":"bool"}],
        "payable":false,
        "stateMutability":"nonpayable","type":"function"
    }, [wallet.address, tokenAmount])
    return testFunction(wemix._address, approveFuncAbi);
}
```

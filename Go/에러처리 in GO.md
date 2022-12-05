# GO 코드에서 에러처리  
* 기존 코드  
```
req, err := http.NewRequest("GET", url, nil)
if err != nil {
  return nil, err
}

// Klaytn headers
if chainInfo.Chain == "Klaytn" {
  req.Header.Add("Authorization", "Basic "+key)
  req.Header.Add("x-chain-id", strconv.FormatUint(uint64(chainInfo.ChainId), 10))
}

client := &http.Client{}
resp, err := client.Do(req)
if err != nil {
  return nil, err
}
defer resp.Body.Close()
b, err := io.ReadAll(resp.Body)
if err != nil {
  return nil, err
}

respJson := map[string]interface{}{}
err = json.Unmarshal(b, &respJson)
if err != nil {
  return nil, err
}
return respJson, nil
```  

* Go스러운 에러처리  
```
client := &http.Client{}
if resp, err := client.Do(req); err != nil {
	return err
} else {
	defer resp.Body.Close()

	if b, err := io.ReadAll(resp.Body); err != nil {
		return err
	} else if err := json.Unmarshal(b, &resp); err != nil {
		return err
	} else {
		return nil
	}
}
```  
기존 언어들은 함수가 예외를 던질지 여부를 결코 알 수 없었음. 그래서 throws나 try catch 구문을 이용해 에러를 대비했었음. 예상되는 에러의 양이 증가하면 극도로 장황한 코드로 이어지게 됨.  
따라서 에러가 나지 않도록 함수를 짜는 것이 클린 코딩으로 권장되는 방법이었음.  

그러나 Go는 "모든 함수는 실패할 수 있기에 반드시 마지막 값으로 에러를 리턴해야 하고, 그걸로서 종료되어야 한다".  
에러를 예상치 못한 오류가 아닌 함수에서 반환된 "기본적인, 평범한" 값으로 다룸. 거의 모든 함수에서 에러를 반환하고 있음.  
에러를 다른 데이터 유형과 거의 동일한 방식으로 처리할 수 있어야 한다. 다른 언어에서 에러를 다루는 방식대로 다루면 코드가 저어엉말 지저분하게 됨.  

위의 두 코드를 비교해보면, Go스러운 예외처리에 감탄을 금치 못하게 됨.  
보통 if 문에서 메인은 함수의 성공적인 실행이었음. 조건문의 성공을 어느 방향으로 처리할 것인지가 고려 대상이었음.(여기서 내가 말하는 성공은 에러와 상반된 개념, 즉, 정상 작동)  
그러나 Go에서는 에러가 메인이 됨으로서 에러가 없으면 if문의 모든 조건문을 실행하게 됨. 코드 프로세스를 if문을 이용해 진행시킬 수 있게 됨!  
에러가 없다면 다음 if 조건문으로 넘어가고, 에러가 있다면 해당 위치에서 바로 처리됨. 너무나!! 창의적임!!  

Go에서 에러는 그저 값일 뿐임. error를 우리들의 패키지의 API의 한 부분으로 생각하고 다른 파트들을 다루듯이 다루어야 한다.  





## 출처  
나(내가 현재 하고 있는 프로젝트를 하면서 느낀점)  
https://latentis.tistory.com/m/69  
http://cloudrain21.com/golang-graceful-error-handling  
https://go.dev/blog/error-handling-and-go  
https://shortstories.gitbook.io/studybook/go/go-error-handling  
https://evilmartians.com/chronicles/errors-in-go-from-denial-to-acceptance?fbclid=IwAR2QLLZ4KrPPX7wQk5faERPOqMQNm4znUH7rvTLTw0GeZiYPnrR80TFWzbE  



# GO로 클라단에서 보낸 jwt 검증 및 파싱하기  
백엔드에서 다른 사이트로 요청 보낼 때, url에 사용자의 주소 정보가 필요했었다.  
나는 이걸 클라에서 oauth 서버와 설정할 때 헤더에 설정된 access token을 가져와서 검증 및 파싱을 진행하여 처리하려 했다.  
그 이유는 아래 주석에도 나와 있는데 보안 측면에서 안전할 것 같아서 그랬다.  
1. 사용자 주소가 노출되면 안된다고 생각했고 2. 요청을 보낸 게 클라이언트에서 로그인한 사용자와 동일한지 확인이 필요하다고 생각했다.  
돈을 보내는 요청인데다가 블록체인이니까 보안에 신경을 많이 써야 한다고 생각했다.  

그런데 검증을 하려면 jwt private key가 필요하다고 하는데, 프론트는 vue.js로 만들어져서 crypto-js를 이용해 jwt를 만드는데. 
그냥 패키지를 그대로 써서 그런지 jwt를 만드는데 필요한 private key가 명시적으로 나와 있지 않았다.(지금 생각하면 당연한게 oauth 서버에서 access token을 넘겨주는거니까 pk는 oauth 서버가 가지고 있겠지)  

그리고 파싱을 하는데 go 에서는 jwt를 파싱을 하려면 claim이라고 jwt payload가 어떤 구조로 이루어져 있는지 알려줘야 한다.  
근데 이 과정에서 내가 원하는대로 동작하지 않아서(그치만 해결은 가능한 수준) 고민하다가 퇴근했는데  

같이 인턴하는 언니가 사용자 주소는 당연히 누구나 볼 수 있고 그래도 된다면서 그냥 Post로 보내도 된다고 하길래  
생각해보니 access token을 내가 파싱할 필요는 없다고 생각했다. 보안적으로는 아직 확신이 가지 않지만 아무튼.  

그치만 지금까지 구현한게 아까우니까 여기다가 백업을 해둔다.  

```
func VerifyToken(tokenString string) (*jwt.Token, error) {
	token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
		//Make sure that the token method conform to "SigningMethodHMAC"
		if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
			return nil, fmt.Errorf("unexpected signing method: %v", token.Header["alg"])
		}
		return []byte(os.Getenv("ACCESS_SECRET")), nil
	})
	if err != nil {
		return nil, err
	}
	return token, nil
}

...

func GetTokenInfo(c *gin.Context) {
	...
	accessToken := c.GetHeader("Authorization") // 사용자 정보 가져오기 위함
	// or c.Request.Cookie("access-token")

	// TODO: jwt 검증(혹시 모르니?) & jwt에서 값 가져오기
	// jwt private key 필요.....
	// 공격자가 중간에 access token 바꿔치기 -> 주소를 다른걸로!
	// 부자 지갑에서 자기 지갑으로 옮기게!
	// 근데 어차피 tx 보내고 chain에서 sign 하는 과정에서 걸리려나?
	// signing method 검증
	token, err := VerifyToken(accessToken)
	if err != nil {
		fmt.Println(err)
		c.JSON(http.StatusUnauthorized, gin.H{})
		return
	}
	// 만료 기간 검증
	if _, ok := token.Claims.(jwt.Claims); !ok && !token.Valid { // TODO: 문법?
		fmt.Println("hihi")
		fmt.Println(err)
		c.JSON(http.StatusUnauthorized, gin.H{})
		return
	}

	// access token에서 사용자 정보 추출
	// TODO: 파싱을 다시 해야함. claim을 고칠것!!!
	parser := jwt.Parser{ValidMethods: []string{"HS256"}}
	claims := &Claims{}
	token, _, err := parser.ParseUnverified(accessToken, claims)
	if err != nil {
		fmt.Println(err)
		return
	}
	...
}
```

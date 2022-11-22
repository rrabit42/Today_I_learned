# SDK 사용기  
여기에 npm install 로 설치가 안되는 sdk가 있다.(npm에서 알 수 있듯 js로 쓰여진 sdk다) 우리는 해당 sdk의 소스코드를 가지고 있다면 직접 build를 통해서 해당 sdk를 사용할 수 있다.  
```npm install```, ```npm build```를 하면 번들링이 되는데 성공적으로 되었다면, dist 폴더가 생성되고 그 안에 ```webpack.config.js```의 output에 설정해준 이름대로 파일(여기서는 ```wemix.js``` 파일)이 생성되어있는 것을 확인할 수 있다.  
```wemix.js``` 파일은 ```webpack.config.js```의 entry 파일을 진입점으로 잡아 의존관계에 있는 모든 모듈들을 하나로 합친 파일이다.  

> npm run build -> 컴파일 + 링크의 느낌, 분석한 소스코드로 실행 가능한 프로그램을 만듦  
> 빌드는 package.json에서 build 스크립트 부분이 실행되고, 여기에 webpack이 적혀있다면 webpack이 실행됨.
> webpack은 파일을 모아 하나의 js 파일로 만들어줌(bundle.js)  
> js 파일을 수정할 때마다 Webpack 실행해 번들작업 해주면 시간도 걸리고 귀찮음. 그렇다면 webpack-dev-server 패키지를 이용해서 우리가 실제 빌드를 해 bundle.js 파일을 만들지 않아도  
> 메모리 상에 가상의 bundle.js 파일을 만들어 우리가 웹 사이트를 띄울 때 자동으로 번들된 js 파일을 띄워주게 할 수 있음. 게다가 소스가 수정될 때 마다 업데이트된(번들링된) bundle.js 파일을 띄워주고 화면도 새로고침 해줌.  

그냥 이대로 웹팩으로 번들링된 SDK를 필요한 프로젝트 파일에 사용하려고 하는데 script 태그가 두개 이상이 되니 충돌이 발생하는 것 같았다.  
어떨 때는 SDK 변수들을 제대로 import 해오는데 어떨 때는 해당 변수가 undefined 라고 떴다.  
```
<script src="../dist/wemix.js"></script>
<script>
    // you can use mymodule
</script>
```  
~~아니 정석대로 왜 안되는데~~  

script 충돌때문이라고 원인을 추측한 나는 script로 import해주는 대신 ES2015의 import 문을 사용해서 우리의 원래 script 태그 안에 넣어야겠다고 생각했다.  
```
<script>
  ...
  import Web3 from "web3";
  import Caver from "caver-js"
  import mymodule from '../dist/js/wemix'; // 이렇게 사용!
  ...
</script>
```  
이런 식으로!  

이렇게 라이브러리 형식으로 사용하고 싶어서 [찾아보니](https://kishu.gitbooks.io/webpack/content/) ```output.library``` 와 ```output.libraryTarget```을 설정하면 된다고 하더라.  
[공식문서](https://webpack.kr/configuration/output/#outputlibrarytarget) 에서 더 자세한 설명이 나와있다.  

```
//webpack.config.js
..
output: {
    library: "mymodule",
    libraryTarget: "umd"
}
..
```  
나는 Target은 'umd'를 사용했는데 이는 모든 모듈 정의 아래에 라이브러리를 내보낸다는 뜻이다. CommonJS, AMD 및 전역 변수와 함께 작동할 수 있다.  
즉 <script> 로드 뿐만 아니라 모든 방식의 로더에서 모듈을 사용할 수 있다.  
  
이렇게하니 제대로 sdk 함수들을 사용할 수 있었다^^!  


## 더 읽어보면 좋을 자료  
* [10분 만에 웹팩 배우기](https://serzhul.io/JavaScript/learn-webpack-in-under-10minutes/)  
> 웹팩 설정하기  
> Entry point — 모든 의존 객체들이 모인 웹팩의 시작점을 정의합니다. 이러한 의존들은 의존 그래프를 생성합니다.  
> Output — 어느 곳에 빌드 과정에서 JS와 정적 파일들을 모을지 정의합니다.  
> Loaders — 웹팩이 다양한 파일 확장자를 다룰 수 있도록 도와주는 서드파티 확장 프로그램들입니다. JS가 아닌 파일들을 모듈로 바꿔줍니다.  
> Plugins — 웹팩의 동작 방식을 바꿔주는 서드파티 확장 프로그램들입니다.  
> Mode — 개발(development) 과 생산(production) 두 모드를 정의합니다. 기본은 생산(Production)입니다.  
  
* [Javascript 모듈에 대해](https://ui.toast.com/weekly-pick/ko_20190418)  
**꼭 읽어보자**  
> JS에는 ES 모듈, CommonJS 모듈, AMD 모듈 등 다양한 모듈 시스템이 공존하고, 각 모듈은 모두 다른 방식으로 모듈을 정의하며 객체를 내보내고 가져오도록 설계되어 있음.  
> 그동안 번들러를 사용해서 ES6 환경으로 개발을 할 때 CommonJS 모듈을 ES6의 import 문법으로 가져오더라도 에러가 발생하지 않는 게 의심스럽지는 않았는가?  
> ES 모듈에만 있는 방법인 default import(이하 기본값 가져오기)를 이용해서 다른 방식의 모듈을 가져와 사용할 수 있는 이유, 그리고 번들러와 트랜스파일러 그리고 타입스크립트는 어떤 방법으로 서로 다른 모듈끼리 가져오기를 지원하는지 알아보도록 하자.  
  

  


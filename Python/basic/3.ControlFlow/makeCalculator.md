````py
"""
textwrap: 줄 맨 앞의 공통 들여쓰기를 자동으로 제거. 코드 정렬은 잘 유지하면서 원하는 출력할 수 있게 함. 
textwrap.depent를 빼고 실행해보면 차이를 알수 있음.
"""
import textwrap
playing = True
while playing:
    f = int(input('Choose a number:'))
    s = int(input('Choose another one:'))
    op = input(textwrap.dedent("""
        Choose an operation:
            Options are: + , -, * or /
            Write 'exit' to finish
        """))
    if op=='+':
        print(f'Result: {f+s}')
    elif op=='-':
        print(f'Result: {f-s}')
    elif op=='*':
        print(f'Result: {f*s}')
    elif op=='/':
        print(f'Result: {f/s}')
    elif op=='exit':
        playing = False
```




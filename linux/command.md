# Command

## cat

```sh
# file에 stdin을 EOF가 입력될 때까지 쓴다.
cat > file_name << EOF
> Hello
> EOF
```

## head,tail

```sh
# 파일 앞 5글자 읽기 
head -c 5 file1

# 파일 뒤의 5글자 읽기
tail -c 5 file2
```

## find

```sh
# 현재 디렉토리부터 모든 하위 디렉토리 순회
find . -name 'file1'

# -mindepth 2 현재 디렉토리(1) 제외
# -maxdepth 1 현재 디렉토리만
```

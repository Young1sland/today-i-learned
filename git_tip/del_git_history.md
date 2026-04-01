# git history 및 파일 영구 삭제

```sh
# git-filter-repo 설치
$ sudo apt install -y git-filter-repo
$ git filter-repo --help

# 파일 및 히스토리 영구 삭제
# git filter-repo --path {file_path} --invert-paths
$ git filter-repo --path .env --invert-paths
$ git log --all -- .env
$ git push origin --force --all
$ git push oriign --force --tags

# RPC failed Error 발생 시
$ git config --global http.version HTTP/1.1
$ git config --global http.postBuffer 524288000

# 로컬 정리
$ git reflog expire --expire=now --all
$ git gc --prune=now --aggressive
```

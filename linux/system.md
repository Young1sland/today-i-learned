# inode

리눅스 파일 시스테은 각 파일들을 inode라는 메타 데이터로 관리하며 각 파일은 고유한 inode 가짐

## 하드링크

생성되는 파일의 inode를 링크하고자하는 파일에 직접 연결

- 파일을 연결하면 커널에서는 이를 체크하기 위해 inode 링크 개수를 1 증가시킴.
- 파일 A(원본파일)에 파일B를 하드링크로 생성하면 A,B는 같은 inode를 참조하며 refCount(reference count)가 2가 됨
- 파일 A를 삭제해도 refCount가 줄어 1로 0이 아니므로 inode가 사라지지 않고 B는 접근 가능

## 심볼릭 링크(소프트 링크)

심볼릭 링크로 생성된 파일은 원본파일 inode를 직접 가리키지 않고 원본 파일을 가리키는 고유한 inode를 새로 생성함.

- 파일 A(원본), 파일 B(심볼릭 링크)는 각자 inode를 가지고 refCount가 1임
- 원본 파일 A를 지우면 B는 접근 불가

```sh
ln file1 hardlink_f1
ln -s file1 symlink_f1
ls -ali # i옵션으로 inode 확인 가능
    # inode number | 권한 | refcount | ....
    7036874417949446 -rwxrwxrwx 2 root root    0 Apr 15 18:56 file1
    7036874417949446 -rwxrwxrwx 2 root root    0 Apr 15 18:56 hardlink_f1
    12666373952134600 lrwxrwxrwx 1 root root    5 Apr 15 18:56 symlink_f1 -> file1
```

- 하드링크로 생성하면 inode number가 같고 refcount가 2로 증가
- 심볼릭링크로 생성하면 inode number가 다르고 refcount는 1


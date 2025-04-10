
git fetch
git fetch를 실행하면
변경된 Git 객체를 .git/objects 폴더에 저장한다.
Git 객체는 다음과 같다.
Blob 파일의 내용자체만 저장하는 객체
Tree 어떤 파일이름이 있었는지, 어떤 폴더에 있었는지 폴더 구조를 저장
Commit 어떤 트리를 가리키는지와 메타데이터(작성자이름, 시간 등)을 저장
Tag

원격 Branch의 commit 위치 정보를 .git/refs/remotes에 업데이트한다.
remotes폴더에는 원격저장소 이름을 가진 폴더가 있고,
그 안에는 Branch이름의 텍스트파일이 저장되어있다.
텍스트파일 안에는 최신커밋의 해시값이 저장되어있다.

```
.git/
└─ refs/
   └─ remotes/
      ├─ origin/
      │  ├─ main
      │  └─ feature/login
      └─ upstream/
         └─ main
```

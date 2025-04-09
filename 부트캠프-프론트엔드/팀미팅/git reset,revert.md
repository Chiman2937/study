# 📝 주제
git reset과 revert의 차이점과 reset보다 revert사용이 권장되는 이유에 대해 설명해보세요.
<br></br>
## ✅ git reset vs revert
### 1. git reset
- git reset은 기존 commit을 취소하고 원래대로 되돌려버린다.

<br></br>
### 2. git revert
- git revert는 기존 commit을 취소하는 내용의 새로운 commit을 생성한다.

<br></br>
## ✅ 내가 면접에서 이 질문을 받았다면?
### 요약 답변
> 
> 
### 상세 답변
> git revert는 기존의 commit을 취소하는 새로운 commit을 생성하는 방식으로 이력이 유지되고 팀원들과의 협업에서도 안정적입니다.  
> 반면 git reset은 기존의 commit 이력을 되돌리고 지워버리는 방식이기 때문에, 이미 공유된 commit을 reset할 경우 다른 작업자의 이력과 충돌이 발생할 수 있습니다.  
> 이 때문에 협업 환경에서는 git reset보다 revert가 더 안전하고 권장됩니다.

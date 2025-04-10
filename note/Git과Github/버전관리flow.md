# 📝 Git
Git과 GitHub을 이용한 협업 프로젝트 관리 flow (Fork 방식)
<br></br>
## ✅ Git과 GitHub?
### 1. Git
 
### 2. GitHub

## ✅ 로컬저장소와 원격저장소

## ✅ Git과 Github을 이용한 버전관리 flow
![image](https://github.com/user-attachments/assets/0d9b3614-03c5-4a0f-99b5-5a3b6bd92c01)


<br></br>
## ✅ 예상 가능한 면접 질문?
### 질문
> Git과 GitHub를 이용해서 협업 프로젝트를 진행한 경험이 있다고 하셨는데요,  
> 팀원들과 협업할 때 Git 저장소를 어떻게 관리하셨나요?  
> 예를 들어 원격 저장소(origin, upstream) 관리, 브랜치 전략, 충돌 해결 등 전반적인 협업 흐름을 설명해주세요.  

### 내가 생각한 답변
> Git 저장소는 로컬 저장소와 원격 저장소로 구분할 수 있습니다.  
> 원격 저장소는 일반적으로 Upstream과 Origin이라는 이름으로 구분되는데,  
> Upstream은 프로젝트 원본 저장소, Origin은 Upstream을 clone하여 작업하는 개인의 원격저장소를 의미합니다.  
> 보통 협업을 시작할 때는 Upstream에서 fork하여 개인 origin 저장소를 만들고, origin을 clone하여 로컬에 clone하여 개발을 진행합니다.  
> 그 후 main혹은 develop 브랜치에서 분기해서 feature/기능명 hotfix/버그명 등으로 명명하여 브랜치를 관리하였습니다.  
> Merge전에는 충돌을 방지하기 위해, upstream의 최신 main 브랜치에서 내 작업 브랜치로 먼저 merge하여 충돌을 예방한 후 Pull Request를 통해 Upstream에 merge하는 방법을 이용했습니다.

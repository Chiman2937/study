# 📝 Git
Git과 GitHub을 이용한 협업 프로젝트 관리 flow (Fork 방식)
<br></br>
## ✅ Git과 GitHub?
### 1. Git
- Git은 분산 버전 관리 시스템이다.
- 프로젝트의 변경 이력을 관리하고 다양한 브랜치를 활용해 안정적으로 개발을 진행할 수 있도록 도와준다.
- Git을 사용하면 로컬 저장소와 원격 저장소를 연동해 팀원 간의 코드 공유 및 협업이 가능하다.
### 2. GitHub
- Git 저장소를 웹에서 호스팅하고 관리할 수 있도록 해주는 플랫폼이다.
- GitHub에서는 저장소 관리뿐만 아니라 Pull Request, 코드 리뷰, 이슈 트래킹, 협업 워크플로우 등 다양한 협업 기능도 함께 제공된다.
 
<br></br>
## ✅ 로컬저장소와 원격저장소
### 1. 로컬저장소
- 개인 PC에서 관리되는 Git 저장소
- 개발자가 코드를 작성하고 버전 관리를 진행하는 공간
### 2. 원격저장소
- GitHub와 같은 서버에 위치한 저장소
- 여러 개발자들이 공유하고 협업할 수 있도록 만들어준다
### 3. Upstream & Origin
- 일반적으로 원격 저장소는 Upstream과 Origin이라는 이름으로 구분해 사용한다.
- Upstream : 프로젝트의 원본이나 팀의 공식 저장소를 말한다.
- Origin : 내가 직접 소유하거나, Upstream에서 Fork해서 생성된 저장소를 말한다.
- Upstream과 Origin 모두 로컬 저장소와 연결하여 사용할 수 있으며, fetch, pull, push 등의 명령어를 통해 협업을 원할하게 진행할 수 있다.
  
<br></br>
## ✅ Git과 Github을 이용한 버전관리 flow
![git과 github를 이용한 협업 flow](https://github.com/user-attachments/assets/2c9a8107-7f35-460f-bcca-44e24dae6161)

## ✅ 커맨드 명령어 정리
### git clone
- `git clone`은 GitHub 등의 원격 저장소에 있는 프로젝트를 내 컴퓨터에 복사해서 가져오는 명령어이다.
```
git clone [<옵션>] <원격저장소Url>
git clone -b <브랜치이름> [<옵션>] <원격저장소 Url>
git clone --branch <브랜치이름> [<옵션>] <원격저장소 Url>
```
| 옵션 | 설명 |
| --- | --- |
| `--single-branch` | 지정한 Branch만 클론(다른 브랜치는 무시) |

- 예시
> 원격저장소에서 dev브랜치의 내용만 가져오기:
```
git clone -b dev --single-branch https://github.com/Chiman2937/study.git
```

<br></br>
### git push
- `git push`는 로컬 저장소에서 커밋한 내용을 원격 저장소에 반영하는 명령어이다.
- `git fetch`와 `git merge`을 한번에 실행하는 명령어이다.
```
git push [<옵션>] [<원격저장소 이름>] [<브랜치 이름>]
```
| 옵션 | 설명 |
| --- | --- |
| `-u`, `--set-upstream` | 로컬저장소와 원격저장소를 연결해서 이후 pull/push를 간편하게 할 수 있도록 만들어 줌 |
| `-f`, `--force` | 강제로 변경사항을 저장하고 히스토리를 덮어씀(merge도 무시) |

- 예시
> origin에 로컬저장소의 커밋이력 반영하기:
```
git push -u origin https://github.com/Chiman2937/study.git
```

<br></br>
### git pull
- `git pull`은 원격 저장소의 커밋을 가져온 후, 자동으로 로컬 브랜치에 병합하여 최신 상태로 만드는 명령어이다.
```
git pull [<옵션>] [<원격저장소 이름>] [<브랜치 이름>]
```
| 옵션 | 설명 |
| --- | --- |
| `--rebase` | merge 대신 rebase로 병합하여 커밋이력을 깔끔하게 만들어 줌 |

- 예시
> 로컬저장소 main branch에 origin의 커밋이력 반영하기:
```
git pull origin main
```

<br></br>
### git fetch
- `git fetch`은 원격 저장소의 커밋이력을 로컬로 가져오는 명령어이다.
- 하지만 로컬 브랜치와의 병합은 자동으로 진행되지 않는다.
```
git fetch [<옵션>] [<원격저장소 이름>] [<브랜치 이름>]
```
| 옵션 | 설명 |
| --- | --- |
| `--all` | 모든 원격저장소에서 커밋이력을 불러온다. |

- 알아두면 좋은점
 #### fetch를 해서 커밋이력을 불러올 때, 원리는 다음과 같다.
> 변경사항에 대한 Git 객체를 .git/objects 폴더에 저장한다.  
> 원격 Branch의 commit 위치 정보를 .git/refs/remotes에 업데이트한다.  

- 예시
> origin의 커밋이력을 로컬로 가져오기:
```
git fetch origin main
```

<br></br>
## ✅ 예상 가능한 면접 질문?
### 질문1
> Git과 GitHub를 이용해서 협업 프로젝트를 진행한 경험이 있다고 하셨는데요,  
> 팀원들과 협업할 때 Git 저장소를 어떻게 관리하셨나요?  
> 예를 들어 원격 저장소(origin, upstream) 관리, 브랜치 전략, 충돌 해결 등 전반적인 협업 흐름을 설명해주세요.  

### 내가 생각한 답변1
> Git 저장소는 로컬 저장소와 원격 저장소로 구분할 수 있습니다.  
> 원격 저장소는 일반적으로 Upstream과 Origin이라는 이름으로 구분되는데,  
> Upstream은 프로젝트 원본 저장소, Origin은 Upstream을 clone하여 작업하는 개인의 원격저장소를 의미합니다.  
> 보통 협업을 시작할 때는 Upstream에서 fork하여 개인 origin 저장소를 만들고, origin을 clone하여 로컬에 clone하여 개발을 진행합니다.  
> 그 후 main혹은 develop 브랜치에서 분기해서 feature/기능명 hotfix/버그명 등으로 명명하여 브랜치를 관리하였습니다.  
> Merge전에는 충돌을 방지하기 위해, upstream의 최신 main 브랜치에서 내 작업 브랜치로 먼저 merge하여 충돌을 예방한 후 Pull Request를 통해 Upstream에 merge하는 방법을 이용했습니다.

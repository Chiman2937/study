# 📝 Git
Git과 GitHub을 이용한 협업 프로젝트 관리 flow (Fork 방식)
<br></br>
## ✅ Git과 GitHub?
### 1. Git
- Git은 `분산 버전 관리 시스템`(중앙 서버에만 의존하지 않고, 각 **개발자의 컴퓨터(로컬)에도 전체 프로젝트의 이력을 복사해서 관리**할 수 있는 시스템)이다.
- 프로젝트의 변경 이력을 관리하고 다양한 브랜치를 활용해 안정적으로 개발을 진행할 수 있도록 도와준다.
- Git을 사용하면 로컬 저장소와 원격 저장소를 연동해 팀원 간의 코드 공유 및 협업이 가능하다.
### 2. GitHub
- Git 저장소를 `웹에서 호스팅하고 관리`할 수 있도록 해주는 플랫폼이다.
- GitHub에서는 저장소 관리뿐만 아니라 Pull Request, 코드 리뷰, 이슈 트래킹, 협업 워크플로우 등 다양한 협업 기능도 함께 제공된다.
 
<br></br>
## ✅ 로컬저장소와 원격저장소
### 1. 로컬저장소
- 개인 PC에서 관리되는 Git 저장소
- 개발자가 코드를 작성하고 버전 관리를 진행하는 공간

### 2. 원격저장소
- GitHub와 같은 서버에 위치한 저장소
- 여러 개발자들이 공유하고 협업할 수 있도록 만들어준다

<br></br>
## ✅ 원격저장소
### 1. Upstream & Origin
- 일반적으로 원격 저장소는 Upstream과 Origin이라는 이름으로 구분해 사용한다.
- Upstream과 Origin 모두 로컬 저장소와 연결하여 사용할 수 있으며, fetch, pull, push 등의 명령어를 통해 협업을 원할하게 진행할 수 있다.

  |이름|설명|
  |---|---|
  | `Upstream` | 프로젝트의 원본이나 팀의 공식 저장소를 말한다.|
  | `Origin` | 내가 직접 소유하거나, Upstream에서 Fork해서 생성된 저장소를 말한다. |

![스프린트 미션 flow](https://github.com/user-attachments/assets/81c7dccb-a715-4017-959e-6772496a11d5)

<br></br>
### 2. 원격저장소간의 관계
- Upstream -> Origin -> local 순서로 작업하는 흐름 때문에, Upstream이 Origin의 상위개념이라고 오해할 수 있다.
- Upstream은 Origin은 둘 다 원격저장소를 지칭하는 이름일 뿐이며, 계층적 관계는 아니다.
- 다만 Upstream과 Origin은 Fork관계로 연결되어 있을 뿐이다.
![원격저장소들의 관계](https://github.com/user-attachments/assets/710f56a5-db45-4f76-8ada-033495aa3293)

<br></br>
### 3. Fork 여부에 따른 GitHub PR 구조 이해하기

#### 🔍 GitHub에서 `Fork`란?
 - 영어단어 Fork : `갈라지다`
 - **다른 사람의 GitHub 저장소를 내 GitHub 계정의 새로운 원격 저장소로 복제** 하는 것을 말한다.
 - `A(Upstream) 저장소`에서 Fork해서 `B(Origin) 저장소`를 만든 후
 - B(Origin)에서 `커밋이 발생`하면 GitHub는 이것을 인식하고 `Compare & pull request` 버튼을 자동으로 보여준다(A와 B의 브랜치가 연결되어있을 경우)

| 관계                   | 가능 PR 방향        | 조건                                          | 권장 여부           |
|----------------------|---------------------|---------------------------------------------|--------------------|
| A와 B가 Fork관계가 아님          | A → B / B → A       | 상대 저장소 접근 권한 + 변경사항 존재                | ✅ 가능             |
| B가 A에서 Fork됨       | B → A                | 일반적인 오픈소스 협업 방식                       | ✅ 매우 권장        |
| B가 A에서 Fork됨       | A → B                | 기술적으로 가능하나, 개인 저장소에 반영은 비협업적 구조임   | ❌ 비권장 / 보안 우려 |

![git과 github의 사본 (1)](https://github.com/user-attachments/assets/5631068d-5f0a-48db-82fc-deb9ae7dea53)


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
> 
> 팀원들과 협업할 때 Git 저장소를 어떻게 관리하셨나요?
> 
### 질문2
> 예를 들어 원격 저장소(origin, upstream) 관리, 브랜치 전략, 충돌 해결 등 전반적인 협업 흐름을 설명해주세요.

<br></br>
### 내가 생각한 답변1
> Git과 GitHub를 활용한 협업에서는 저장소를 `로컬 저장소`와 `원격 저장소`로 나누어 관리했습니다.
> 
> 원격 저장소는 보통 `Upstream`과 `Origin`이라는 이름으로 구분되는데,  
> 
> Upstream은 팀 프로젝트의 원본 저장소, Origin은 Upstream을 개인이 Fork한 원격 저장소입니다.  
> 
> 협업 초기에는 Upstream에서 Fork하여 개인 origin 저장소를 만들고, origin을 로컬에 clone하여 개발을 시작했습니다.
>
> 작업 후에는 Origin에 Push 후, GitHub에서 Pull Request를 생성하고 팀원들과 코드 리뷰를 거쳐 Upstream 저장소에 병합하는 방식으로 협업했습니다.

### 내가 생각한 답변2
> 그 후 main혹은 develop 브랜치에서 분기해서 `feature/기능명` `hotfix/버그명` 등으로 명명하여 `브랜치를 관리`하였습니다.
> 
> Merge전에는 충돌을 방지하기 위해, upstream의 최신 main 브랜치에서 내 작업 브랜치로 먼저 merge하여 충돌을 예방한 후 Pull Request를 통해 Upstream에 merge하는 방법을 이용했습니다.

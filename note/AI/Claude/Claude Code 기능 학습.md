# TIL: Claude Code 핵심 개념 정리

> Claude Code의 Memory, Rules, Skills, Subagents, Agent Teams 개념을 공부하며 정리한 내용

---

## Memory (CLAUDE.md)

Claude Code가 세션 시작 시 **자동으로 읽는 파일**. 프로젝트의 "설계도" 역할을 한다.

- 항상 로드되며, 전체 프로젝트에 공통으로 적용되는 규칙과 컨텍스트를 담는다
- 저장 위치에 따라 적용 범위가 다르다
  - `~/.claude/CLAUDE.md` → 전역 (모든 프로젝트)
  - `./CLAUDE.md` 또는 `./.claude/CLAUDE.md` → 프로젝트 한정
- 담는 내용: 아키텍처, API 명세, 빌드 명령어, 코딩 컨벤션, 에이전트 위임 규칙 등
- 권장 크기: **200줄 이하**. 넘어가면 Rules로 분리

```markdown
<!-- CLAUDE.md 예시 -->

## 프로젝트 개요

Next.js 15 + TypeScript 기반 그룹 미팅 플랫폼

## 빌드 명령어

- 개발 서버: npm run dev
- 테스트: npm run test

## 에이전트 위임 규칙

- 프론트엔드 작업 → frontend 에이전트에게 위임
- 백엔드 작업 → backend 에이전트에게 위임
```

---

## Rules

**CLAUDE.md가 200줄을 넘어갈 때 주제별로 분리하는 공식 방법**.

- `.claude/rules/` 디렉토리 안의 모든 `.md` 파일이 자동으로 로드됨
- CLAUDE.md와 동일한 우선순위를 가짐
- **핵심 기능: 경로 스코핑** — `paths` 필드로 특정 파일 작업 시에만 로드되도록 설정 가능

```
.claude/
├── CLAUDE.md              # 전체 공통 지침
└── rules/
    ├── code-style.md      # 항상 로드 (paths 없음)
    ├── testing.md         # 항상 로드
    ├── frontend.md        # tsx 파일 작업 시에만 로드
    └── backend.md         # api 파일 작업 시에만 로드
```

```markdown
## <!-- .claude/rules/frontend.md -->

paths:

- src/components/\*_/_.tsx
- src/hooks/\*_/_.ts

---

# 프론트엔드 규칙

- 함수형 컴포넌트만 사용
- Tailwind로만 스타일링
```

> **CLAUDE.md vs Rules 차이**
> CLAUDE.md는 항상 로드, Rules는 `paths` 설정으로 조건부 로드 가능

---

## Skills

**특정 조건일 때만 로드되는 전문 지침**. 작업의 "전문 매뉴얼" 역할을 한다.

- 세션 시작 시 이름과 설명(frontmatter)만 시스템 프롬프트에 등록
- 조건에 맞을 때만 전체 내용 로드 → **Progressive Disclosure(점진적 공개)**
- 메인 에이전트, 서브에이전트 모두 사용 가능 (서브에이전트 전용이 아님)
- 저장 위치
  - `~/.claude/skills/` → 전역
  - `.claude/skills/` → 프로젝트 한정 (Git으로 팀 공유 가능)

```
skill-name/
├── SKILL.md        # 진입점 (name, description 필수)
├── scripts/        # 실행 스크립트
├── references/     # 참고 문서
└── assets/         # 템플릿, 폰트 등
```

### 스킬 트리거 방식 3가지

| 방식                             | 설명                                     |
| -------------------------------- | ---------------------------------------- |
| 자동 트리거                      | `description` 매칭 시 Claude가 자동 로드 |
| 명시적 호출                      | `/skill-name` 명령어로 직접 실행         |
| `disable-model-invocation: true` | 자동 트리거 비활성화, 사용자만 호출 가능 |

### 스킬이 효과를 내는 조건

```
스킬 description이 구체적
        +
CLAUDE.md에서 사용 규칙 명시
        +
프로젝트에 맞는 스킬만 선별 설치
```

> 커뮤니티 스킬 100개 설치 < 프로젝트에 맞는 스킬 3개 + 잘 쓴 CLAUDE.md

---

## Subagents

**별도의 Claude 인스턴스를 생성해 작업을 위임하는 기능**.

인테리어 담당자가 전기 배선을 어떻게 설치하는지 알 필요 없듯, 메인 에이전트는 작업을 시키고 결과만 받는다. 서브에이전트는 역할에 따라 컨텍스트를 분리해 명확한 작업을 수행한다.

### 핵심 특징

- 각 서브에이전트는 **독립된 컨텍스트 창**을 가짐 → 메인 컨텍스트 오염 없음
- 최대 **10개까지 병렬 실행** 가능
- 메인 에이전트는 결과만 수신 (단방향)
- 저장 위치
  - `~/.claude/agents/` → 전역
  - `.claude/agents/` → 프로젝트 한정 (프로젝트가 전역보다 우선순위 높음)

```markdown
## <!-- .claude/agents/frontend.md -->

name: frontend
description: UI 컴포넌트, 페이지, 스타일링 작업이 필요할 때
tools: Read, Write, Edit, Bash
model: sonnet

---

너는 시니어 프론트엔드 개발자야.

- Next.js, TypeScript, Tailwind 전문가
- src/frontend/ 디렉토리만 수정
```

### 서브에이전트 활용 가치

| 가치               | 설명                             |
| ------------------ | -------------------------------- |
| 컨텍스트 품질 유지 | 각 인스턴스가 자기 영역에만 집중 |
| 전문성 확보        | 역할별 전문 스킬/지침 주입 가능  |
| 병렬 처리          | 프론트/백엔드 동시 작업 가능     |
| 실수 방지          | `tools`로 디렉토리 접근 제한     |

### 모노레포와의 궁합

모노레포의 "코드는 분리, 관리는 통합" 철학이 서브에이전트의 "컨텍스트는 분리, 오케스트레이션은 통합"과 완전히 같은 방향이다.

```
my-project/
├── .claude/
│   ├── CLAUDE.md          # API 명세, 전체 아키텍처
│   └── agents/
│       ├── frontend.md
│       └── backend.md
├── apps/
│   ├── frontend/
│   └── backend/
└── packages/
    └── shared/            # 메인 에이전트가 직접 관리
```

---

## Agent Teams

**서브에이전트의 한계(단방향 통신)를 극복한 인스턴스 간 양방향 소통 기능**.

- 현재 **실험적 기능** (`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 로 활성화)
- 팀원들이 공유 태스크 리스트와 인박스 메시지로 직접 소통
- 세션 종료 시 사라지는 **임시 구조** (서브에이전트 `.md` 파일은 영속적)

### 서브에이전트 vs Agent Teams

|                    | 서브에이전트   | Agent Teams            |
| ------------------ | -------------- | ---------------------- |
| 인스턴스           | 별도 O         | 별도 O                 |
| 인스턴스 간 소통   | ❌ (메인 경유) | ✅ (직접 소통)         |
| 공유 태스크 리스트 | ❌             | ✅                     |
| 영속성             | ✅ (.md 파일)  | ❌ (세션 종료 시 소멸) |
| 토큰 비용          | 낮음           | 높음                   |

```
서브에이전트   메인 → 서브 (단방향)
Agent Teams   메인 ↔ 서브 ↔ 서브 (양방향)
```

### 언제 무엇을 쓸까

| 상황                                     | 선택                   |
| ---------------------------------------- | ---------------------- |
| API 명세 확정 후 프론트/백엔드 독립 구현 | 서브에이전트           |
| API 명세 협의하면서 동시에 구현          | Agent Teams            |
| 코드 리뷰 → 수정 → 재검토 반복 루프      | Agent Teams            |
| 단순 분업, 결과만 취합                   | 서브에이전트 (더 저렴) |

---

## ~/.claude/projects/ (세션 히스토리)

**세션 대화 기록 저장소**. CLAUDE.md 같은 설정 파일이 아니라 순수하게 대화 기록만 저장한다.

- 프로젝트 경로를 인코딩해서 폴더명으로 사용
- 세션마다 UUID로 된 `.jsonl` 파일 생성
- 기본적으로 **30일 후 자동 삭제** (설정으로 변경 가능)

```
~/.claude/projects/
└── -Users-cyga2-wego/          # 프로젝트 경로를 인코딩한 폴더명
    ├── abc123.jsonl             # 세션 1 대화 기록
    ├── def456.jsonl             # 세션 2 대화 기록
    └── memory/                 # Auto memory 파일
        └── MEMORY.md
```

### 어디에 쓰이냐

**`claude --resume`**
과거 세션을 이어서 시작할 때 이 폴더를 읽어서 대화 목록을 보여줌

**Auto Memory**
Claude가 세션 중 "Writing memory" / "Recalled memory" 표시할 때 `memory/` 폴더에 실제로 파일을 쓰고 읽는 것

### CLAUDE.md vs projects/ 차이

|           | CLAUDE.md            | projects/                      |
| --------- | -------------------- | ------------------------------ |
| 작성자    | 사람이 직접 작성     | Claude가 자동 생성             |
| 역할      | Claude에게 주는 지침 | Claude가 남기는 기록           |
| 로드 시점 | 세션 시작 시 항상    | `--resume` 또는 Auto Memory 시 |
| Git 공유  | ✅ 권장              | ❌ 로컬 전용                   |

### 자동 삭제 비활성화

```json
// ~/.claude/settings.json
{
  "cleanupPeriodDays": 365
}
```

---

## 전체 구조 요약

```
CLAUDE.md            항상 적용되는 설계도
Rules                CLAUDE.md가 200줄 넘을 때 주제별 분리
Skills               조건부로 로드되는 전문 매뉴얼
Subagents            컨텍스트를 분리한 전문 직원 (단방향)
Agent Teams          직원들끼리 직접 소통 가능한 팀 구조 (양방향)
~/.claude/projects/  세션 대화 기록 저장소 (Claude가 자동 관리)
```

> 스킬을 아무리 잘 설치해도, CLAUDE.md가 이것들을 제대로 오케스트레이션하지 못하면 효과가 절반 이하로 떨어진다.
> **CLAUDE.md가 전체 시스템의 퀄리티를 결정한다.**

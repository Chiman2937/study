# 🧪 TypeScript 과제: Router 경로에서 파라미터 추출 타입 만들기

## 📚 과제 목적

라우터 경로 문자열(예: `/users/:id`)에서 `:id`, `:userId` 같은 파라미터 이름을 추출하여  
타입스크립트 타입으로 `{ id: string }`, `{ userId: string; postId: string }` 형태로 만들어보세요.

이를 통해 `infer`, `template literal types`, `conditional types`, `mapped types`, `재귀 타입`을 실습합니다.

---

## ✅ 최종 목표

| 경로 문자열                        | 기대 타입                             |
| ---------------------------------- | ------------------------------------- |
| `/users/:id`                       | `{ id: string }`                      |
| `/posts/:slug/comments/:commentId` | `{ slug: string; commentId: string }` |
| `/about`                           | `{}`                                  |
| `/users/:id?`                      | `{ id?: string }`                     |

---

## 📘 레벨 1: 단일 파라미터 추출

```ts
// 아래 타입을 완성하세요
type ExtractParams<S extends string> =
  // 여기에 구현

// 테스트 코드
type T1 = ExtractParams<'/users/:id'>;   // { id: string }
type T2 = ExtractParams<'/posts/:slug'>; // { slug: string }
type T3 = ExtractParams<'/about'>;       // {}
```

---

## 📗 레벨 2: 여러 개 파라미터 추출

```ts
type ExtractParams<S extends string> =
  // 여기에 재귀적으로 구현

// 테스트 코드
type T4 = ExtractParams<'/users/:userId/posts/:postId'>;
// { userId: string; postId: string }

type T5 = ExtractParams<'/comments/:commentId/like/:reactionId'>;
// { commentId: string; reactionId: string }
```

---

## 📙 레벨 3 (도전): 옵셔널 파라미터 지원

```ts
type ExtractParams<S extends string> =
  // `:id?` → id?: string
  // 여기에 구현

// 테스트 코드
type T6 = ExtractParams<'/users/:id?'>;
// { id?: string }

type T7 = ExtractParams<'/users/:userId/posts/:postId?'>;
// { userId: string; postId?: string }
```

---

## 💡 힌트

- `infer`를 사용해 문자열에서 파라미터 이름 추출하기
- `extends \`\${string}:\${infer Param}\`\` 구조 익히기
- `Mapped Type`으로 `{ [K in Param]: string }` 형태 만들기
- 여러 번 등장하는 경우 재귀적으로 처리
- `infer Name`, `infer Rest` 활용해 분리하기

---

```

```

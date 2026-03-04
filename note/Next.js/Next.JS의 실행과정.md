# 📜 Next.js에서 SSR 실행과정

## 💡 Next.js의 렌더링 방식
Next.js는 CSR, SSR, SSG, ISR 방식을 모두 지원한다.

Next.js의 기본 렌더링 방식은 SSG(애플리케이션을 빌드할 때 한번만 HTML을 생성하는 렌더링) 방식이다.

SSR 방식은 사용자가 요청할 때 마다 서버에서 렌더링 하는 방식이다.

## 💡 SSR 실행과정 요약
1. 브라우저가 페이지 요청
2. Next.js 서버에서 SSR 대상인지 판단
3. 서버에서 React 컴포넌트를 실행하여 HTML 파일 생성
4. HTML파일을 클라이언트로 전송
5. 클라이언트에서 Hydration 수행

<br></br>
---

### ✔️ 브라우저가 페이지 요청
사용자가 브라우저에 특정 페이지에 대한 요청을 하면 해당 요청은 Next.js 서버로 전송된다.

<br></br>
---

### ✔️ Next.js 서버에서 SSR 대상인지 판단
Next.js 서버는 SSR의 조건을 만족하면 SSR을 적용한다.

- (예시) 컴포넌트에 다음 선언이 있는 경우
```ts
//Next.js 15 기준
export const dynamic = 'force-dynamic';
```

<br></br>
---

### ✔️ 서버에서 React 컴포넌트를 실행하여 HTML 파일 생성
Next.js 서버는 RSC를 이용해 HTML파일을 생성한다.

> ❓ RSC란?
>
> React 18부터 도입된 개념으로 JS 번들없이 서버에서만 실행되는 React 컴포넌트이다.

<br></br>
---

### ✔️ HTML파일을 클라이언트로 전송

<br></br>
---

### ✔️ 클라이언트에서 Hydration 수행
브라우저는 받은 HTML을 그대로 표시하고, JS 번들이 로딩되면 React가 이를 Hydration 한다.
이 과정을 통해 이벤트 핸들러나 상태관리 등 인터렉션 기능이 복원된다.

# 📜 Next.js의 SSR과 Hydration

## 📌 기존 React의 한계
React는 기본적으로 CSR(Client Side Rendering)을 기반으로 한다.

이 방식은 사용자가 페이지에 접근하면 JS 번들이 로드되고 그 JS가 실행되어야 화면이 보이는 구조이다.

### ❗ CSR의 단점
- 초기 로딩 지연: 콘텐츠를 보려면 JS가 모두 로드되어야 하므로 초기 로딩이 느리다.
- SEO에 불리: 초기 HTML이 비어 있어 검색 엔진 최적화(SEO)에 불리하다.
- 사용자 경험 저하: 로딩 전까지 빈 화면이 보일 수 있다.

## 💡 Next.js가 React의 단점을 극복한 방식
Next.js는 React의 단점을 보완하기 위해 SSR(Server Side Rendering)을 도입하였으며 CSR, SSR, SSG, ISR 렌더링 방식을 모두 지원한다.

### 🔍 App Router vs Page Router
|디렉토리|기본 렌더링 방식|설명|
|---|---|---|
|pages/|SSG| 빌드 타임에 정적 HTML을 미리 생성|
|app/|SSR + RSC|요청 시에 Server Component를 실행하여 HTML 생성|

## 💡 SSR의 정의와 한계
### ✔️ SSR(Server Side Rendering)
서버에서 HTML을 생성한 뒤 클라이언트에 전달해주는 렌더링 방식이다.

초기 화면을 빠르게 보여줄 수 있고, SEO에 유리하다는 장점이 있다.

### ❓ SSR의 한계
Next.js가 SSR 방식을 채택하여

SSR 방식인 Server Component는 서버에서만 실행되므로 useState, useEffect, onClick과 같은 브라우저 기반 상호작용 로직은 사용할 수 없다.



Next.js는 이 문제를 해결하기 위해 Hydration 기술을 도입했다.

## 💡 Hydration
SSR은 서버에서 실행 가능한 Server Component들을 HTML로 먼저 렌더링하고 사용자와 상호작용하는 로직을 가진 Client Component들은 브라우저에서 JS번들이 로드된 후 실행된다.

이때 placeholder(자리표시자)를 사용해 해당 위치를 유지한 뒤, React가 그 자리에 JS 컴포넌트를 연결(hydrate)한다.

이 방식을 통해 Next.js는 CSR의 한계를 극복하면서 빠른 렌더링, SEO 친화성, 동적 상호작용을 만족할 수 있다.

## 💡 실제 동작 흐름(App Router 기준)
### ✅ 1. 사용자가 페이지를 요청한다.
`Page.tsx`는 SSR 컴포넌트로 처리된다.

### ✅ 2. Page 내부 검사
 ✔️ Page 내부에 SSR 컴포넌트가 있을 경우
RSC에 의해 해당 컴포넌트는 HTML로 즉시 렌더링된다.

 ✔️ Page 내부에 CSR 컴포넌트가 있을 경우
해당 컴포넌트의 자리에 placeholder를 HTML로 삽입한다.

### ✅ 3. HTML을 클라이언트에 전송
클라이언트는 HTML을 전달받아 즉시 화면에 렌더링한다.

### ✅ 4. hydrate
이후 CSR 컴포넌트의 JS 번들이 브라우저에 로드되면 React가 해당 placeholder에 CSR 컴포넌트를 동적으로 연결한다.

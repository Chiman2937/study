# 📜 Next.js의 장점

## 💡 React의 한계

### ✔️ SEO에 불리함
React는 기본적으로 CSR(Client Side Rendering) 방식만 지원하기 때문에, 초기 HTML이 비어 있어 검색 엔진 최적화(SEO)에 불리하다.

### ✔️ 복잡한 라우팅 설정
페이지 라우팅을 위해 react-router-dom을 직접 설치하고, <Routes> 및 <Route>로 경로와 컴포넌트를 수동으로 설정해야 한다.

<br></br>

## 💡 Next.JS의 기능
### ✔️ 자동 라우팅 지원
app/ 또는 pages/ 디렉토리 구조만으로도 URL이 자동으로 생성되어 라우팅이 매우 간편하다.

<details>
  <summary>🔍 app 디렉토리 구조: Next.js 13부터 도입된 새로운 방식(App Router)</summary>
  
폴더의 이름이 URL 경로가 되며, 해당 폴더 내 `page.tsx`가 해당 경로의 루트 컴포넌트로 렌더링된다.
```bash
app/
├── page.tsx             # 루트 경로 '/'의 페이지
├── login/
│   └── page.tsx         # '/login' 경로의 페이지
├── signup/
    └── page.tsx         # '/signup' 경로의 페이지
```  
</details>

<details>
  <summary>🔍 pages 디렉토리 구조: Next.js 12까지의 기본 방식(Pages Router)</summary>

  파일 이름이 URL 경로가 되며, `index.tsx`가 해당 디렉토리의 루트 페이지로 인식 된다.
```bash
pages/
├── index.tsx         # 루트 경로 '/'의 페이지
├── login.tsx         # '/login' 경로의 페이지
├── signup.tsx        # '/signup' 경로의 페이지
```

</details>


### ✔️ 다양한 렌더링 방식 지원
CSR, SSR(Server Side Rendering), SSG(Static Site Generation), ISR(Incremental Static Regeneration)을 모두 지원하여 상황에 따라 유연한 렌더링 전략을 선택할 수 있다.

|항목|Next.js 12이전|Next.js 15|
|---|---|---|
|CSR|`useEffect`|`use client` + `useEffect`|
|SSR|`getServerSideProps`|`dynamic = 'force-dynamic'`|
|SSG|`getStaticProps`|`dynamic = 'force-static'`|
|ISR|`getStaticProps` + `revalidate`|`fetch(...{ next: {revalidate: N} })` or `revalidate = N`|

<details>
  <summary>🔍 CSR 방식(Next.js 15 기준)</summary>

```tsx

```

</details>

### ✔️ 이미지 최적화
`next/image` 컴포넌트를 통해 자동 리사이징, 포맷 변환(WebP), lazy loading 등 이미지 성능 최적화를 지원한다.

### ✔️ 성능 최적화
코드 스플리팅, 정적 자원 캐싱, 자동 빌드 최적화 등 다양한 성능 향상 기능이 기본으로 제공된다.

### ✔️ 의미 기반 디렉토리 구조
`layout.tsx`, `page.tsx`, `loading.tsx`, `error.tsx` 등 역할에 맞는 파일명을 사용함으로써, 구조적이고 일관성 있는 프로젝트 관리가 가능하다.

## 💡 React vs Next.JS 비교
| 항목          | React                 | Next.js                      |
| ----------- | --------------------- | ---------------------------- |
| **페이지 라우팅** | `react-router-dom` 필요 | `app/` 또는 `pages/` 기반 자동 라우팅 |
| **SEO**     | CSR만 지원 → 불리          | CSR/SSR/SSG/ISR 모두 지원 → 유리   |
| **이미지 최적화** | 직접 최적화 필요             | `next/image`로 자동 최적화         |
| **성능 최적화**  | 수동 설정 필요              | 자동 코드 분할, 캐싱 등 기본 제공         |
| **프로젝트 구조** | 자유도 높지만 일관성 낮을 수 있음   | 의미 기반 디렉토리 구조로 일관성 있음        |

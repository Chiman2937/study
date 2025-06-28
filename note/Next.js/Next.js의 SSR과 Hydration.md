# 📜 Next.js의 SSR과 Hydration

## 📌 기존 React의 한계
React는 기본적으로 CSR(Client Side Rendering)을 기반으로 한다.

이 방식은 사용자가 페이지에 접근하면 JS 번들이 로드되고 그 JS가 실행되어야 화면이 보이는 구조이다.

### ❗ CSR의 단점
- 초기 로딩 지연: 콘텐츠를 보려면 JS가 모두 로드되어야 하므로 초기 로딩이 느리다.
- SEO에 불리: 초기 HTML이 비어 있어 검색 엔진 최적화(SEO)에 불리하다.
- 사용자 경험 저하: 로딩 전까지 빈 화면이 보일 수 있다.

<br></br>

---

### 🤔 React의 SSR 구현

React에서는 CSR의 단점을 극복하기 위해 react-dom/server 모듈을 통해 SSR로 렌더링할 수 있도록 만들었다.
아래의 API를 사용하면 서버에서 JSX → HTML로 변환할 수 있다.

> renderToString()
> 
> renderToNodeStream() (React 17 이하)
> 
> renderToPipeableStream() (React 18 이상 – Streaming 지원)

<br></br>
---

#### ✔️ SSR(Server Side Rendering)
서버에서 HTML을 생성한 뒤 클라이언트에 전달해주는 렌더링 방식이다.

초기 화면을 빠르게 보여줄 수 있고, SEO에 유리하다는 장점이 있다.

<br></br>
---

#### ❗ SSR의 한계
SSR 방식은 서버에서만 실행되므로 useState, useEffect, onClick과 같은 브라우저 기반 상호작용 로직은 사용할 수 없고, CSR 방식을 사용해야한다.

그래서 React는 SSR방식을 사용하면서도 상호작용하는 로직이 있는 컴포넌트만 CSR로 렌더링 할 수 있도록 `Hydration` 기술을 도입했다.

<br></br>

---

### 💧 Hydration

Hydration은 서버에서 렌더링된 HTML에 클라이언트 측 자바스크립트를 연결하여 동적으로 동작하게 만드는 과정을 말한다.

그러니까 정적으로 처리 가능한 Server Component들 먼저 HTML 처리하여 클라이언트로 보내주고, 동적으로 작동하는 Client Component는 나중에 전달해준다는 것이다.

Hydration은 SSR을 사용하는 웹 환경 전반에서 존재하던 개념이며, 이런 기술은 React 이전(Vue.js 등)에도 존재했다.

> Hydration 정의 문구를 보고, 그러면 어떤 JSX 컴포넌트가 있을 때 정적 DOM 요소와 JS로직을 분리한다는 말인가? 라고 오해했었다

<br></br>
---


### 🔍 Hydration의 원리

![image](https://github.com/user-attachments/assets/9b307e5e-2d04-4103-863b-a8f994759651)

1. SSR은 서버에서 실행 가능한 Server Component들을 HTML로 먼저 렌더링한다.
2. Client Component는 placeholder(자리표시자)를 HTML에 렌더링한다.
3. HTML이 먼저 클라이언트에 전달되어 브라우저에 화면이 렌더링 된다.
4. 사용자와 상호작용하는 로직을 가진 Client Component들은 브라우저에서 JS 번들이 로드된 후 실행된다.

이 방식을 통해 CSR의 한계를 극복하면서 빠른 렌더링, SEO 친화성, 동적 상호작용을 만족할 수 있다.

<br></br>
---

## 💡 Next.js에서는?
React에서는 기본적으로 CSR로 렌더링 되지만 SSR로 렌더링 하는 방식도 가능하며, Hydration을 사용할 수도 있다고 했다.

그럼 Next.js를 쓸때와 그냥 React를 쓸때는 어떤 차이가 있을까?

### 🔍 Next.js의 기본 렌더링 구조

Next.js는 기본적으로 페이지 전체를 SSR로 렌더링하면서도 React의 Hydration 기능을 활용해 사용자와 상호작용이 필요한 일부 컴포넌트만 클라이언트에서 CSR + Hydration으로 처리하는 구조를 채택했다.

이를 통해 초기 렌더링 속도와 SEO를 확보하면서도 클라이언트 JS 비용을 최소화하는 하이브리드 렌더링 구조를 구현한다.

|구분|React|Next.js 도입(App Router 기준)|
|---|---|---|
|기본 렌더링|CSR|SSR|
|SSR 가능?|별도 설정으로 가능|기본으로 제공|
|hydration 가능?|별도 설정으로 가능|`'use client'` 설정으로 가능|

<br></br>
---

### 📌 Hydration 기술을 통해 알 수 있는 점
Next.js에서는 기본적으로 SSR 방식으로 렌더링 하며, 사용자와 상호작용하는 컴포넌트만 CSR 방식으로 렌더링하는 Hydration 기술을 사용한다고 말했다.

이를 통해 알 수 있는 점은 각 컴포넌트의 역할 분리가 명확히 일어나야 한다는 것이다.

사진처럼 정적인 요소와 사용자와 상호작용하는 요소가 하나의 컴포넌트에 같이 위치한다면 Hydration의 이점을 활용할 수 없을 것이다.

<br></br>

---

## 💡 실제 동작 요약(App Router 기준)

![image](https://github.com/user-attachments/assets/40b93acc-37b0-4d32-8da8-f0acc9ffed3a)


### ✅ 1. 사용자의 페이지 요청
사용자가 특정 URL에 접근하면, 해당 경로의 Page.tsx가 SSR 방식으로 처리된다.

<br></br>
### ✅ 2. Page 내부 컴포넌트 확인
✔️ Server Component가 포함되어 있다면:

  - React Server Components(RSC)에 의해 서버에서 즉시 HTML로 렌더링된다.

✔️ Client Component가 포함되어 있다면:
  
  - 서버에서는 해당 위치에 placeholder만 렌더링된다 (HTML 뼈대만 포함됨).
  - 이 때 Client Component를 위한 JS번들 생성 작업을 즉시 시작한다.

<br></br>
### ✅ 3. 클라이언트로 HTML 전달
서버가 렌더링한 HTML을 클라이언트에 전송하고, 브라우저는 이를 즉시 표시한다.

<br></br>
### ✅ 4. JS 번들(Client Component용) 전달
클라이언트가 placeholder에 들어갈 Client Component용 JavaScript 번들을 다운로드한다.

<br></br>
### ✅ 5. Hydration 실행
JS 번들이 로드되면, React가 placeholder에 Client Component를 동적으로 연결(hydrate)하여 완전한 인터랙티브 페이지가 완성된다.

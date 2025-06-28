# 📜 Next.js의 SSR과 Hydration

## 📌 기존 React의 한계
React는 기본적으로 CSR(Client Side Rendering)을 기반으로 한다.

이 방식은 사용자가 페이지에 접근하면 JS 번들이 로드되고 그 JS가 실행되어야 화면이 보이는 구조이다.

<br></br>
---

### ❗ CSR의 단점
- 초기 로딩 지연: 콘텐츠를 보려면 JS가 모두 로드되어야 하므로 초기 로딩이 느리다.
- SEO에 불리: 초기 HTML이 비어 있어 검색 엔진 최적화(SEO)에 불리하다.
- 사용자 경험 저하: 로딩 전까지 빈 화면이 보일 수 있다.

<br></br>

---

## 💡 Next.js가 React의 단점을 극복한 방식
Next.js는 React의 단점을 보완하기 위해 SSR(Server Side Rendering)을 도입하였으며 CSR, SSR, SSG, ISR 렌더링 방식을 모두 지원한다.

### 🔍 App Router vs Page Router
|디렉토리|기본 렌더링 방식|설명|
|---|---|---|
|pages/|SSG| 빌드 타임에 정적 HTML을 미리 생성|
|app/|SSR + RSC|요청 시에 Server Component를 실행하여 HTML 생성|

<br></br>

---

## 💡 SSR의 정의와 한계
### ✔️ SSR(Server Side Rendering)
서버에서 HTML을 생성한 뒤 클라이언트에 전달해주는 렌더링 방식이다.

초기 화면을 빠르게 보여줄 수 있고, SEO에 유리하다는 장점이 있다.

<br></br>
---

### ❓ SSR의 한계
하지만 SSR 방식인 Server Component는 서버에서만 실행되므로 useState, useEffect, onClick과 같은 브라우저 기반 상호작용 로직은 사용할 수 없고, CSR 방식인 Client Component를 사용해야한다.

React를 기준으로 아래와 같이 페이지 컴포넌트가 있다고 가정해보자.

```tsx
export default function PageComponent () {
  return (
    <Header /> {/* 정적 컴포넌트 */}
    <Form /> {/* 사용자와 상호작용하는 컴포넌트 */}
    <Footer /> {/* 정적 컴포넌트 */}
  )
}
```
![image](https://github.com/user-attachments/assets/3e5510a7-d426-4eeb-8432-ef4caba28308)

React에서는 컴포넌트가 CSR 방식으로 렌더링 되긴 하지만, 이 컴포넌트를 SSR 방식으로 렌더링 할 수 있을까?

이 예제에서 `Header`와 `Footer` 컴포넌트가 정적 컴포넌트라고 해도 `Form` 컴포넌트가 사용자와 상호작용하는 컴포넌트이므로 `Page`는 CSR 방식으로 렌더링 되어야 정상적으로 동작할 것이다.

그러니까 사용자와 상호작용하는 부분이 하나라도 있으면 그 페이지는 SSR방식으로 렌더링을 하지 못하는 것이다.

<br></br>
---

### ✅ Next.js에서는?

Next.js에서는 이 문제를 해결하기 위해 아래와 같은 해결책을 내놓게 된다.

> **"기본적으로는 SSR을 적용하되, 사용자와 상호작용이 필요한 컴포넌트만 CSR을 적용하자"**

이게 Hydration 기술의 기본 개념이다.

<br></br>

---

## 💡 Hydration

Hydration은 서버에서 렌더링된 HTML에 클라이언트 측 자바스크립트를 연결하여 동적으로 동작하게 만드는 과정이라고 정의하고 있다.

그러니까 정적으로 처리 가능한 Server Component들 먼저 HTML 처리하여 클라이언트로 보내주고, 동적으로 작동하는 Client Component는 나중에 전달해준다는 것이다.

> Hydration 정의 문구를 보고, 그러면 어떤 JSX 컴포넌트가 있을 때 정적 DOM 요소와 JS로직을 분리한다는 말인가? 라고 오해했었다

### 🔍 Hydration 기술의 원리

![image](https://github.com/user-attachments/assets/973982e7-2164-4fd8-995d-af362a00715a)

1. SSR은 서버에서 실행 가능한 Server Component들을 HTML로 먼저 렌더링한다.
2. Client Component는 placeholder(자리표시자)를 HTML에 렌더링한다.
3. HTML이 먼저 클라이언트에 전달되어 브라우저에 화면이 렌더링 된다.
4. 사용자와 상호작용하는 로직을 가진 Client Component들은 브라우저에서 JS 번들이 로드된 후 실행된다.

이 방식을 통해 Next.js는 CSR의 한계를 극복하면서 빠른 렌더링, SEO 친화성, 동적 상호작용을 만족할 수 있다.

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

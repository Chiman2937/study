# 📝 가상 DOM(Virtual DOM)

## 📌 가상 DOM이란?
실제 DOM Tree와 동일한 구조를 갖는 메모리상의 JavaScript 객체로, 현재 UI의 구조를 추상적으로 표현한 것

> <details>
>   <summary><b>실제 DOM</b></summary>
>   
> ```html
>   <div id="root">
>     <h1>제목</h1>
>     <h2>부제목</h2>
>   </div>
> ```
> </details>

> <details>
> <summary><b>가상 DOM 시각화</b></summary>
> 
> ```js
> const virtualDom = {
>   type: 'div',
>   props: { id: root },
>   children: [
>     {
>       type: 'h1',
>       props: {},
>       children: ['제목'],
>     },
>     {
>       type: 'h2',
>       props: {},
>       children: ['부제목'],
>     },
>   ],
> }; 
> ```
> </details>

<br></br>
## 🔍 가상 DOM을 생성하고 실제 DOM에 적용하는 과정

예제는 button을 클릭 했을 때 count가 1씩 증가하는 예제이다.

![image](https://github.com/user-attachments/assets/60f44530-22fe-4843-ad11-0f991f0717ae)

```js
import { useState } from 'react';

function App() {
  const [count, setCount] = useState(0);
  const handleClick = () => setCount(count + 1);

  return (
    <>
      <h1>제목</h1>
      <button onClick={handleClick}>count: {count}</button>
    </>
  );
}
```
button을 클릭했을 때 페이지가 리렌더링 되는 과정을 살펴보면 다음과 같다.

### ✔️ 1. button Click
- handleClick 함수에 의해 count state의 값이 1 증가

<br></br>
### ✔️ 2. React Scheduler 등록

- React에서 리렌더링을 발생시키는 조건이 발생하면 scheduling Queue에 React Scheduler를 등록한다.
  
    > 예제에서는 state 변경에 의해 React Scheduler를 등록한다.

    | 트리거 이벤트          | 설명                             |
    | ---------------- | ------------------------------ |
    | `setState` 호출    | 컴포넌트의 상태가 변경될 때                |
    | 부모 컴포넌트의 리렌더링    | 부모 리렌더링 → 자식 컴포넌트도 리렌더링 스케줄링   |
    | `props` 변경       | 부모가 전달한 `props`가 변경될 때         |
    | `context` 변경     | React Context 구독 중인 값 변경 시     |
    | `forceUpdate` 호출 | 클래스 컴포넌트에서 강제 리렌더링 요청 시        |
    | `key` 변경         | 컴포넌트의 `key` 속성이 변경되어 재마운트 필요 시 |


<br></br>
### ✔️ 3. Event Loop 타이밍에 scheduler 실행

<br></br>
### ✔️ 4. Render Phase (diffing)
여기서 render는 화면은 렌더링 한다는 의미가 아닌, 현재 상태를 기반으로 새로운 UI 구조를 메모리상에 준비한다는 의미이다.

state를 참조하여 Virtual DOM을 새로 만들고 이전에 생성했었던 Virtual DOM과 비교하여 Patch Plan을 생성한다.

<details>
  <summary><b>💡 변경 전 가상 DOM 시각화</b></summary>
  
```js
const virtualDomBefore = {
  type: 'div',
  props: {},
  children: [
    {
      type: 'h1',
      props: {},
      children: ['제목'],
    },
    {
      type: 'button',
      props: { onClick: handleClick },
      children: ['count: 0'],
    },
  ],
};
```
</details>

<details>
  <summary><b>💡 변경 후 가상 DOM 시각화</b></summary>

```js
const virtualDomAfter = {
  type: 'div',
  props: {},
  children: [
    {
      type: 'h1',
      props: {},
      children: ['제목'],
    },
    {
      type: 'button',
      props: { onClick: handleClick },
      children: ['count: 1'],
    },
  ],
};
```
</details>

<details>
  <summary><b>💡 Patch Plan 시각화</b></summary>
  
```js
const patchPlan = [
  {
    type: 'TEXT_UPDATE',
    target: 'button',
    oldText: 'count: 0',
    newText: 'count: 1',
  },
];
```
</details>

<br></br>
### ✔️ 5. Commit Phase
Patch Plan을 기준으로 실제 DOM에 접근하여 변경사항을 반영한다.
이때 DOM Tree는 dirty 상태로 변경된다.

```js
patchPlan.forEach(patch => {
  if (patch.type === 'TEXT_UPDATE') {
    const domElement = document.querySelector(patch.target);
    if (domElement && domElement.textContent === patch.oldText) {
      domElement.textContent = patch.newText;
    }
  }
});
```

<br></br>
### ✔️ 6. JS 실행 종료
브라우저가 IDLE 상태로 전환된다.

> IDLE 상태란?
> 
> JS 코드가 모두 실행 되어 Call Stack, task Queue, Microtask Queue 등이 모두 비워져 브라우저가 자유롭게 렌더링을 수행할 수 있는 상태

<br></br>
### ✔️ 7. 렌더링 사이클 실행
브라우저가 Layout → Paint → Composite → 화면 업데이트

<br></br>
## ✅ 바닐라JS와 React의 렌더링 플로우 비교

### 바닐라JS
1. JS실행 중 코드에서 직접 DOM API 호출
2. JS 실행 종료
3. 렌더링 사이클 실행

### React
1. JS실행 중 상태변경 실행
2. 상태변경에 의해 React Scheduler 실행
3. Render Phase
4. Commit Phase
5. JS 실행 종료
6. 렌더링 사이클

<br></br>
## ❓ 가상DOM이 필요한 이유
바닐라JS로 실제 DOM에 직접 접근하여 수정하는 것과 React에서 가상 DOM을 이용해 실제DOM을 수정하는 방식이 크게 달라보이지 않는다.

가상DOM을 썼을때 장점은 뭐가있을까?

### 1. 역할 분리
React는 주요 철학으로 '역할 분리'를 매우 중요하게 여긴다.

Virtual DOM은 '역할 분리'의 개념을 강화한다.

|주체|역할|
|---|---|
|개발자| state와 UI 관리에만 집중|
|React 시스템|DOM 접근과 업데이트 책임을 전담|

<br></br>
### 2. 개발자의 실수 방지
바닐라JS에서는 직접 DOM을 조작하면서 발생할 수 있는 불필요한 DOM 업데이트, 렌더링 과잉, 상태-UI 불일치, 리플로우 과잉 문제 등이 발생할 수 있다.

하지만 React는 시스템이 DOM접근을 관리하기 때문에 이런 문제들을 구조적으로 방지할 수 있다.

개발자는 복잡한 DOM 변경 로직을 고민할 필요 없이 상태만 관리하고 React가 자동으로 최적의 방법으로 DOM을 갱신한다.
```html
<body>
  <div>
    <h1>제목</h1>
    <h2>부제목</h2>
  </div>

  <script>
    const h2 = document.querySelector('h2');

    // 1. DOM 변경 (쓰기)
    h2.style.fontSize = '50px';

    // 2. 바로 레이아웃 읽기 → 강제 리플로우 발생
    console.log(h2.offsetWidth);

    // 3. 또 다시 DOM 변경 (쓰기)
    h2.style.color = 'red';
  </script>
</body>
```
#### (1) `h2.style.fontSize = '50px';`
  - DOM은 dirty 상태로 변경
  - JS가 아직 실행중이므로 브라우저는 아직 렌더링 사이클을 실행하지 않음
    
#### (2) `h2.offsetWidth` 호출
    
  - h2의 너비를 불러오기 위해 브라우저가 강제로 렌더링 사이클 실행
    
    > offsetWidth: 요소의 margin을 제외하고 border, padding까지 포함한 너비를 불러오는 속성

#### (3) `h2.style.color = 'red';`
  - DOM은 dirty 상태로 변경
    
#### (4) JS실행 종료 후 렌더링 사이클 실행

<br></br>
## 🚫 가상DOM에 대한 오해
### 1. ❌ Virtual DOM이 메모리에서만 동작하므로 더 빠르다?
- diffing 계산 비용이 존재하므로 DOM API보다 무조건 빠른것은 아니다.
   
<br></br>
### 2. ❌ Virtual DOM은 성능 향상을 위한 기술이다?
- Virtual DOM은 DOM 업데이트 관리 최적화, 유지보수성, 안정성 향상을 위한 기술이며 성능 최적화는 간접적인 효과이다.

<br></br>
### 3. ❌ Virtual DOM만 사용하면 렌더링이 최적화된다?
- DOM 업데이트 이후에 `렌더링 사이클`은 브라우저 엔진이 처리하기 때문에 렌더링 성능과 관련이 없다.

## Fiber Tree(React 16 도입)
React 16부터 Virtual DOM의 구현체가 Fiber Tree로 변경되어 diffing, patching, 렌더링 스케줄링이 비동기적으로 처리 가능하며

작업 단위가 쪼개져서 긴 연산도 끊고 사용자 입력, 애니메이션을 우선 처리할 수 있다.

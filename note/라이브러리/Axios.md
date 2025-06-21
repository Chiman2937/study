# 📝 Axios
[[🔗 Axios 공식문서]](https://axios-http.com/kr/docs/intro)

Axios는 node.js와 브라우저를 위한 Promise 기반 HTTP 클라이언트이며, 주로 서버에 데이터를 요청하거나 전송할 때 사용한다.

서버 사이드에서는 네이티브 node.js의 http 모듈을 사용하고, 클라이언트(브라우저)에서는 XMLHttpRequests를 사용한다.

<br></br>

---

## 💡 Axios를 사용하는 이유
Axios는 기본 내장함수는 fetch보다 더 편리한 기능들을 제공하기 때문에 많이 사용한다.

<br></br>

---

## 💡 Axios의 특징
Axios의 공식문서에 따르면, Axios의 특징은 다음과 같다.
- 브라우저를 위해 XMLHttpRequests 생성
- node.js를 위해 http 요청 생성
- Promise API를 지원
- 요청 및 응답 인터셉트
- 요청 및 응답 데이터 변환
- 요청 취소
- JSON 데이터 자동 변환
- XSRF를 막기위한 클라이언트 사이드 지원

<br></br>

---

## 💡 Axios vs fetch
Axios의 특징들에 대해 브라우저 내장 Api인 fetch와 비교 해보면 다음과 같다.
| 항목                | Axios                                 | Fetch                                                  |
| ----------------- | ------------------------------------- | ------------------------------------------------------ |
| 요청 방식             | 브라우저에서 `XMLHttpRequest` 사용            | 브라우저의 `fetch API` 사용                                   |
| Node.js 지원 여부     | ✅ Node.js에서 내부적으로 `http` 모듈 사용          | ⚠️ 브라우저 전용 API이며, Node.js에서는 `node-fetch` 같은 별도 패키지 사용 필요 |
| Promise API 지원 여부 | ✅ 지원                                    | ✅ 지원                                                     |
| 요청 및 응답 인터셉터      | ✅ 지원 (`axios.interceptors`)             | ❌ 직접 구현 필요                                               |
| 요청 취소             | ✅ 지원 (`CancelToken`, `AbortController`) | ❌ `AbortController`를 직접 구현해야 함                           |
| JSON 자동 변환        | ✅ 자동으로 JSON 파싱 (`res.data`)             | ❌ `res.json()`을 수동 호출해야 함                                |
| XSRF 방지 지원        | ✅ 기본 지원 (`withCredentials`, 자동 헤더 설정 등) | ❌ 수동 처리 필요 (쿠키/헤더 직접 설정)                                 |

<br></br>

---

## 💡 Axios의 특징에 대해 파헤쳐보기
### ✅ 브라우저를 위해 XMLHttpRequests 생성
Axios는 ES6 이전부터 사용된 구형 브라우저 호환성을 고려해 XMLHttpRequest 기반으로 만들어졌다.

> **XMLHttpRequest(XHR)란?**
> 
> XHR은 AJAX 프로그래밍에서 많이 활용된다.
> XHR 객체는 서버와 상호작용하기 위해 사용되며, 페이지를 새로고침 하지 않고도 URL에서 데이터를 가져올 수 있다.
> 이를 활용하여 사용자의 작업을 방해하지 않고 페이지의 일부를 업데이트 할 수 있다.

<br></br>

---

### ✅ node.js를 위해 http 요청 생성
결론부터 말하자면 fetch는 환경에 따라 구현이 다르기 때문에 문제가 생길 여지가 있고, Axios는 그런 차이를 내부에서 알아서 처리해주는 안정적인 선택지이다.

이게 무슨 말인지 처음엔 잘 이해가 가지 않았는데, fetch의 특징부터 살펴보고 다시 돌아오니 이해가 잘 되었다.

#### ❗ fetch의 경우
fetch는 브라우저 전용 Api이며, node.js 환경에서 사용하기 위해서는 별도의 패키지를 설치해주어야 한다.
```
npm install node-fetch
```
fetch와 node-fetch는 동일하게 작동하긴 한다.

하지만 사양이 동일하지 않기 때문에 일부 옵션이나 동작 방식이 다를 수 있고, 예상치 못한 버그 등이 발생할 수 있는 가능성을 가지고 있다.

#### ✔️ Axios의 경우
Axios는 브라우저와 Node.js 환경 둘다 동일한 API와 기능을 제공하기 때문에 위의 문제점에서 자유롭다.

Axios는 브라우저에서는 XMLHttpRequest, Node.js 환경에서는 http/https 모듈을 알아서 사용하여 환경에 맞게 처리한다.

<br></br>

---

### ✅ Promise API를 지원
Promise API를 지원한다는 말은 곧 Axios로 보낸 HTTP 요청 결과를 `then`,`catch` / `async`,`await`와 같은 비동기 방식으로 처리할 수 있다는 말이다.

Axios는 내부적으로 Promise를 반환하므로 콜백 함수 없이도 비동기 Http 요청 결과를 체계적으로 관리할 수 있다.

다만 fetch도 Promise를 지원하기 때문에, Promise API를 지원한다는 부분에 대해서는 차이점이 없다.

```js
// then / catch 방식
axios.get('/products/1')
  .then((response) => {
    console.log('상품 정보:', response.data);
  })
  .catch((error) => {
    console.error('요청 실패:', error.message);
  });

// async / await 방식
const getProduct = async (id) => {
  try {
    const response = await axios.get(`/products/${id}`);
    console.log('상품 정보:', response.data);
  } catch (error) {
    console.error('요청 실패:', error.message);
  }
};

```

<br></br>

---

### ✅ 요청 및 응답 인터셉터
Axios는 Api 요청 및 응답 과정에서 요청 정보를 수정하거나 응답을 가공할 수 있는 **인터셉터** 기능을 제공한다.

이 기능을 활용하면 반복되는 설정 코드(예: 토큰 부착, 에러 메시지 처리 등)을 한 곳에서 일괄적으로 관리할 수 있다.

fetch는 인터셉터 기능을 지원하지 않기 때문에 요청 및 응답을 일괄적으로 처리하고 싶을 경우 직접 코드를 작성해 주어야 한다.

#### ❗ fetch의 경우
fetch를 사용할때도 커스텀훅으로 try/catch 처리를 하는 방법 등으로 일괄적으로 관리가 가능하기는 하지만, 한계가 존재한다.

먼저 설명하기 전에 fetch를 이용한 Api 요청 과정을 도식화해서 살펴보면 아래와 같다.

![image](https://github.com/user-attachments/assets/41201e4d-6c13-4a8e-84d7-134be86386da)

이 사진을 보면 커스텀 훅에서도 try/catch를 통해 분기처리를 하고, 각 Api함수에서도 Error/Response 반환 처리를 하면서 불필요하게 여러 군데에서 오류 처리를 하고 있는 것을 알 수 있다.

예시를 통해 살펴보자.

아래와 같이 응답을 일괄적으로 처리하는 useAsync라는 커스텀 훅이 있다고 가정하자.

이 커스텀 훅에서는 전달받은 api함수 정보를 이용해 api요청을 보내고 응답/오류 등을 일괄적으로 처리하고 있다.

- useAsync(Api 응답 일괄처리 커스텀 훅)
```jsx
import { useEffect, useState } from "react";

export const useAsync = (asyncFunction, options) => {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);
  const [result, setResult] = useState(null);

  const getAsyncResult = async (options) => {
    let result = null;
    try {
      setIsLoading(true);
      setError(false);
      result = await asyncFunction(options);
      setResult(result);
    } catch (e) {
      setError(e);
      return;
    } finally {
      setIsLoading(false);
    }
  };

  useEffect(() => {
    getAsyncResult(options);
  }, [options]);

  return { isLoading, error, result };
};

```

하지만 이 기능을 정상적으로 이용하려면, 각 Api함수에서 별도로 오류 처리를 하는 구문을 작성해주어야 한다.

호출한 Api함수에서 오류를 반환해주어야 커스텀 훅에서 catch로 error를 감지할 수 있기 때문이다.

- Api함수 관리 파일
```js
//BASE_URL을 Api함수 관리 파일에서 전역적으로 관리한다.
const BASE_URL = 'https://panda-market-api.vercel.app';

export const getItemDetails = async (id) => {
  const response = await fetch(`${BASE_URL}/products/${id}`);
  // 응답 오류가 발생했을 경우 에러 처리를 하고있다.
  if (!response.ok) {
    throw new Error('품목을 불러오지 못했습니다.');
  }
  const body = await response.json();
  return body;
}

export const getItemComments = async (id) => {
  const response = await fetch(`${BASE_URL}/products/${id}/comments?limit=100`);
  // 응답 오류가 발생했을 경우 에러 처리를 하고있다.
  if (!response.ok) {
    throw new Error('댓글을 불러오지 못했습니다.');
  }
  const body = await response.json();
  return body;
}
```

정리해보자면, useAsync와 같은 커스텀 훅에서 try/catch 분기 처리를 해준다고 하더라도 각 Api함수에서 각각 오류처리를 하는 구문을 작성을 해주어야 하고 있다.

여기서 발생하는 문제를 짚어보자면 아래와 같다.

1. 오류 처리, URL 처리 등에서 역할분리가 명확하지 않음
2. 각 Api함수마다 오류 처리를 하고 있어서 유지보수에 불리함

하지만 Axios를 사용하면, 각 Api함수에서 따로 오류처리 하는 부분까지 전부 인터셉터 기능을 이용해 일괄적으로 관리가능하다

#### ✔️ Axios의 경우

Axios를 이용한 Api요청 과정을 도식화해서 살펴보면 아래와 같다.
![image](https://github.com/user-attachments/assets/425001e0-b6a5-44bb-ab72-f25c8f9f475e)

Axios를 통해 인터셉터와 상호작용하며 요청, 응답을 일괄적으로 관리할 수 있다.

먼저 Axios를 사용하기 위해 인스턴스 객체를 생성한다.

이 때 Axios 인스턴스 객체가 http 요청을 관리하는 주체이기 때문에 변수명을 `httpClient`라고 명명했다.

- httpClient
```js
const httpClient = axios.create({
  // 공통URL도 axios의 속성을 이용해 관리 가능하다.
  // 각 API 함수에서는 상대 경로만 지정하면 이 URL이 자동으로 붙는다.
  baseURL: 'https://panda-market-api.vercel.app',

  // 지정된 시간(ms) 안에 응답이 없으면 요청을 자동으로 중단한다.
  timeout: 10_000,

  // 모든 요청의 기본 headers를 설정할 수 있다.
  // 여기서 Content-Type: application/json은 대부분의 API에 적합하지만, FormData 사용 시는 오히려 자동 설정되도록 주석처리하는 게 안전하다
  // multipart/form-data는 직접 설정하면 boundary가 누락될 수 있음
  headers: { 'Content-Type': 'application/json' },
});
```

이 Axios 인스턴스 객체를 이용해 인터셉터를 생성하여 관리할 수 있다.

- 요청 인터셉터
```js
httpClient.interceptors.request.use(
  // access_token이 있을 경우 headers에 붙여주는 과정을 일괄적으로 처리할 수 있다.
  config => {
    const token = localStorage.getItem('access_token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  error => Promise.reject(error)
);
```

- 응답 인터셉터
```js
httpClient.interceptors.response.use(
  // 성공 응답은 그대로 전달
  // 이때 Axios의 응답값은 .data 속성에 저장되기 때문에 response.data를 반환해주어야 한다.
  ( response ) => response.data,
  // 에러 응답 처리
  ( error ) => {
    const status = error.response?.status;
    const messageMap = {
      400: '잘못된 요청입니다. 입력값을 확인해주세요.',
      401: '로그인이 필요합니다. 로그인 페이지로 이동합니다.',
      403: '접근 권한이 없습니다.',
      404: '요청하신 자원을 찾을 수 없습니다.',
      500: '서버 오류가 발생했습니다. 잠시 후 다시 시도해주세요.',
    };

    const message = messageMap[status] || '알 수 없는 오류가 발생했습니다.';

    // 401인 경우 로그인 페이지로 이동
    if (status === 401) {
      window.location.href = '/login';
    }

    // 에러는 그대로 throw해서 useAsync나 .catch에서 처리할 수 있게 함
    return Promise.reject(error);
  }
);
```

이렇게 되면 각 Api함수에서는 요청만 관리하면 되기 때문에 유지보수 면에서 훨씬 안정적이다.

```js
import httpClient from './httpClient';
// Axios에서 baseURL을 관리하므로 BASE_URL 변수를 관리할 필요가 없다.
// const BASE_URL = 'https://panda-market-api.vercel.app';

export const getItemDetails = async (id) => {
  const data = httpClient.get(`/products/${id}`);
  return data;
}

export const getItemComments = async (id) => {
  const data = httpClient.get(`/products/${id}/comments?limit=100`);
  return data;
}
```


<br></br>

---

### ✅ 요청 취소

#### ❓ 요청 취소 기능이 필요한 이유
왜 굳이 요청을 잘 보내놓고 취소한다는걸까? 그리고 요청을 취소하는 기능이 있으면 어떤 점이 좋을까?

**⚠️ 불필요한 네트워크 요청 방지**
- 사용자가 입력할 때마다 API 요청이 발생하는 자동완성 기능이나 검색창에서 이전 요청을 취소하지 않으면, 마지막 입력 이전의 응답이 뒤는게 도착해서 잘못된 결과가 표시될 수 있음

**⚠️ 리소스 낭비 최소화**
- 스크롤링이나 데이터 페이징 시 사용자가 빠르게 이동하면 이전 요청은 더 이상 유효하지 않음

**⚠️ 컴포넌트 unmount 시 안전하게 요청 중단**
- React 컴포넌트가 unmount 되었는데 비동기 요청이 완료 되면 setState() 등의 호출로 메모리 누수 또는 경고 발생

**⚠️ 사용자 액션에 따른 요청 취소**
- 사용자가 명시적으로 요청을 취소할 수 있도록 기능 제공(ex. 업로드 취소 버튼)
- 긴 시간 소요되는 요청(대용량 파일 업로드 등)에서 유용함

<br></br>
---
#### ❓ 요청 취소 기능을 사용하려면?

Axios에서 요청을 취소하는 방법은 CancelToken과 AbortController API를 활용하는 방법이 있다.

하지만 CancelToken은 v0.22.0에서 deprecated(사용 중단 예정) 되었으며, Axios 공식문서에서도 신규 프로젝트에서는 사용을 권장하지 않는다고 명시되어있다.

> cancelToken은 [취소 가능한 프로미스] 제안을 기반으로 만들어졌지만 해당 제안이 철회되었고, Axios 내부에서도 메모리 누수 및 관리 복잡성 문제가 발견되면서 유지보수 부담이 커졌다.
>
> 이러한 이유로, 현재는 웹 표준인 AbortController를 사용하는 방식이 공식적으로 권장되고 있다.


<details>
  <summary>참고: 취소 가능한 프로미스란?</summary>
  <!-- 내용 -->

취소 가능한 프로미스란, 진행 중인 Promise를 중단할 수 있는 개념이다.

하지만 자바스크립트의 기본 `Promise`는 원래 취소 기능을 지원하지 않는다.

- Promise 예시
```js
const p = new Promise((resolve)=>{
  setTimeout(() => {
    resolve('완료');
  },5000);
});
```

이것을 해결하기위해 나온 아이디어가 내부적으로 Promise를 감싸고, 외부에서 취소할 수 있게 만든 "취소 가능한 프로미스" 라는 개념이다.

- 구조 예시
```js
  const {promise, cancel) = cancelablePromise(fetch(...));

  cancel();
```

자바스크립트 표준화 위원회(TC39)에서 cancelable Promise를 Stage1 까지 제안했지만, 설계가 복잡하고 AbortController로 대체 가능하다는 이유로 제안이 철회되었다.

[[🔗 참고자료- TC39 Cancelable Promises Proposal (Withdraw)]](https://github.com/tc39/proposal-cancelable-promises)

</details>


[[🔗 Axios 공식문서 - 요청취소 가이드]](https://axios-http.com/docs/cancellation)

![image](https://github.com/user-attachments/assets/81c14d61-df44-4626-88dc-08f03e417ea5)

![image](https://github.com/user-attachments/assets/d43cb38d-7c5b-479f-9865-092fb8aaa995)

![image](https://github.com/user-attachments/assets/bacc72f1-11bc-460c-a255-e755e4767786)

<br></br>

---

#### ✔️ AbortController 사용하기
먼저 AbortController는 fetch에서도 사용할 수 있는 브라우저 내장(Web API)이다.

fetch

### ✅ JSON 데이터 자동 변환
fetch를 사용하면 응답(Response)을 수동으로 .json() 메서드를 호출해 JSON으로 파싱해야 한다.
반면 Axios는 응답을 받을 때 내부적으로 자동으로 JSON으로 파싱하여 .data 프로퍼티에 바로 사용할 수 있는 객체 형태로 담아준다.

#### ❗ fetch를 사용한 경우
```js
export const getItemDetails = async (id) => {
  const response = await fetch(`${BASE_URL}/products/${id}`);
  if (!response.ok) {
    throw new Error('품목을 불러오지 못했습니다.');
  }
  // response 값을 json 형태로 파싱 후 return 해줘야 함
  const body = await response.json();
  return body;
}
```

#### ✔️ Axios를 사용한 경우
```js
/* ---------- response interceptor ---------- */
// Axios의 응답값은 JSON으로 파싱된 값이며 .data 프로퍼티에 저장된다.
httpClient.interceptors.response.use(
  (res) => res.data,
  (error) => { /* ... */ }
```

<br></br>

---

### ✅ XSRF를 막기위한 클라이언트 사이드 지원

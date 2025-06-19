# 📝 Axios
[[🔗 Axios 공식문서]](https://axios-http.com/kr/docs/intro)

Axios는 node.js와 브라우저를 위한 Promise 기반 HTTP 클라이언트이며, 주로 서버에 데이터를 요청하거나 전송할 때 사용한다.

서버 사이드에서는 네이티브 node.js의 http 모듈을 사용하고, 클라이언트(브라우저)에서는 XMLHttpRequests를 사용한다.

## 💡 Axios를 사용하는 이유
Axios는 기본 내장함수는 fetch보다 더 편리한 기능들을 제공하기 때문에 많이 사용한다.

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

## 💡 Axios의 특징에 대해 파헤쳐보기
### ✅ 브라우저를 위해 XMLHttpRequests 생성
Axios는 ES6 이전부터 사용된 구형 브라우저 호환성을 고려해 XMLHttpRequest 기반으로 만들어졌다.

> **XMLHttpRequest(XHR)란?**
> 
> XHR은 AJAX 프로그래밍에서 많이 활용된다.
> XHR 객체는 서버와 상호작용하기 위해 사용되며, 페이지를 새로고침 하지 않고도 URL에서 데이터를 가져올 수 있다.
> 이를 활용하여 사용자의 작업을 방해하지 않고 페이지의 일부를 업데이트 할 수 있다.

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

### ✅ 요청 및 응답 인터셉터
// 추후 추가 예정

### ✅ 요청 취소
// 추후 추가 예정

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

### ✅ XSRF를 막기위한 클라이언트 사이드 지원

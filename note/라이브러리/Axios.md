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
| 요청 및 응답 인터셉터      | ✅ 지원 (`axios.interceptors`)             | ❌ 직접 구현 필요                                 정

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

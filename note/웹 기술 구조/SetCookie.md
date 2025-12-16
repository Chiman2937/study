WeGo 프로젝트에서 refreshToken 사용 관련해 발생한 문제 기록

## 문제 상황

로그인 했을 때 백엔드에서 refreshToken을 httpOnly 로 set cookie 해주는 방식을 적용을 했는데, 아래와 같이 domain이 백엔드 url 그대로 설정되는 문제가 있었다.

이 때문에 우리 웹 사이트 도메인인 wego.monster와 일치하지 않아 새로고침하면 사라지는 문제가 발생했다.

<img width="420" height="88" alt="image" src="https://github.com/user-attachments/assets/b9145dce-29e2-44b1-90c0-3b7409e82ccb" />

### 원인

백엔드에서 Set Cookie를 해줄 때 domain 속성을 지정해주지 않으면 domain은 백엔드의 url에 그대로 적용된다.

이는 RFC 6265 명세를 따른다.(공식 규칙)

#### 📊 RFC 6265 명세 (공식 규칙)

If the Domain attribute specifies a domain with a leading dot:
The user agent will include the cookie in requests to that domain and all subdomains.

```
브라우저 (local.wego.monster)
  ↓ fetch('https://api.wego.monster/api/v1/auth/login')
  ↓
API 서버 (api.wego.monster)
  ↓ Set-Cookie: refreshToken=...; (Domain 없음)
  ↓
브라우저
  ↓ "누가 이 쿠키를 보냈지?"
  ↓ "api.wego.monster가 보냈네"
  ↓ "그럼 Domain=api.wego.monster로 설정해야지!"
  ↓
쿠키 저장: Domain=api.wego.monster ✅
```

### 해결 방법1

next.config.ts에서 rewrite 속성을 이용해 요청 url 자체를 현재 웹페이지 url로 덮어써버리는 방식을 사용할 수 있다.

```ts
  async rewrites() {
    return [
      {
        source: '/api/v1/:path*',
        destination: 'https://api.wego.monster/api/v1/:path*',
      },
    ];
  },
```

단, 이 방법은 클라이언트에서만 동작하기 때문에 서버 요청에는 원래 백엔드 URL을 지정해주어야 한다.

NEXT_PUBLIC_API_BASE_URL: 원래 백엔드 URL (ex. https://api.wego.monster/api/v1)
API_BASE_URL: 덮어쓴 URL(ex. /api/v1)

```ts
// axios instance
const getBaseURL = () => {
  if (typeof window === 'undefined') {
    return process.env.API_BASE_URL;
  }
  return process.env.NEXT_PUBLIC_API_BASE_URL;
};

export const baseAPI = axios.create({
  baseURL: getBaseURL(),
  timeout: 20000,
  withCredentials: true,
});
```
이 때 네트워크 탭에서 요청 url을 확인해보면 웹사이트의 url로 덮어써 진것을 확인할 수 있다.

또한 cookie domain 속성을 보면 현재 웹페이지의 domain이 적용되어있어 새로고침을 해도 쿠키가 사라지지 않는다.

<img width="417" height="102" alt="image" src="https://github.com/user-attachments/assets/a13aca08-a89a-4516-8ddf-ba2a7bb61e35" />
<img width="274" height="25" alt="image" src="https://github.com/user-attachments/assets/8b5c7e67-490e-4a54-9430-2d941fffbf2d" />

정말 백엔드 URL까지 숨기고 싶다면 사용해볼만한 방식인 것 같다.

다만 사이트 규모가 커져서 서브도메인이 많아지고, 서브도메인 간 쿠키 공유가 필요하다면 이 방법은 사용할 수 없다. 결과적으로 cookie 도메인 속성이 고정되어있기 때문이다.

refresh cookie domain: local.wego.monster

예를들어 sub.wego.monster에서 refresh 쿠키를 사용하고 싶다면? 사용 불가❌

결론: Rewrites는 Domain 문제를 "직접" 해결하는 게 아니라, Domain 생략 시 브라우저가 올바른 도메인을 설정하도록 돕는 것이다

### 해결 방법2
백엔드에서 set cookie 시 domain을 지정해주되, 앞에 메인 도메인 앞에 .을 찍어서 내려주면 된다.

ex) Domain=.wego.monster

RFC 규칙에 따라 .으로 시작하면 해당 도메인 + 모든 서브도메인을 포함할 수 있게 된다.(와일드카드 개념)

#### RFC 6265 Domain 규칙
```
1. Domain 생략 시:
   → 응답 보낸 서버의 도메인 사용

2. Domain=.wego.monster (점 있음):
   → wego.monster + 모든 서브도메인

3. Domain=wego.monster (점 없음):
   → wego.monster만 (서브도메인 제외)
```

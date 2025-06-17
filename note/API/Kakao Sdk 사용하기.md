# 📝 Kakao Sdk 사용하기
기초 프로젝트를 진행하면서 kakao API를 이용해 카카오톡 공유하기 기능을 구현해야 했다.
카카오톡 공유하기 기능을 구현하기 전 Kakao Sdk를 사용하는 방법부터 알아야하기 때문에 해당 내용부터 살펴봤다.

## 💡 Kakao Developers
먼저 Kakao Developers 사이트에서 계정을 생성하고, 내 애플리케이션을 등록해야 한다.

### ✅ Kakao Developers에 내 애플리케이션 등록하기

[[🔗 Kakao Developers]](https://developers.kakao.com/)
1. Kakao Developers에 로그인 하고, 내 애플리케이션으로 이동한다.

![image](https://github.com/user-attachments/assets/c6dc2d1c-faaf-4172-a9a8-4d9d4dc3f685)

2. 새로운 애플리케이션을 추가한다.
회사명은 크게 신경쓰지 않고 팀명만 입력해도 된다.

![image](https://github.com/user-attachments/assets/959df00b-c573-4082-932e-007cf78d61a2)

![image](https://github.com/user-attachments/assets/566e9a37-8b48-484c-8742-beb855f646e0)

3. 내 애플리케이션 키 확인
앱 설정 => 앱 키로 들어가서 JavaScript 키를 확인해둔다.

![image](https://github.com/user-attachments/assets/ed9786cc-b5ad-4c60-a693-9697b61c06da)

### ✅ Kakao SDK를 내 프로젝트에 적용하기
Kakao Developers의 문서들을 참고하면 어떤 방식으로 적용해야하는지 알 수 있다.

Kakao Developers => 문서 => 카카오 API 시작하기(JavaScript)로 들어가보자.

![image](https://github.com/user-attachments/assets/87cd36c4-9a9c-46c3-9ede-f9c88b49a035)

1. JavaScript SDK 파일을 웹 페이지에 include 하기

![image](https://github.com/user-attachments/assets/adf7a5e5-7097-457f-afe7-85ff3d2e6a1d)

여기서 카카오의 버전 `${VERSION}`과 `${INTEGRITY_VALUE}` 값을 지정해줘야 한다는 것을 알 수 있다.
다운로드 페이지로 이동하면, version과 integrity 값이 포함된 script를 한번에 복사해 올 수 있다.

![image](https://github.com/user-attachments/assets/1a43fb92-53a7-4b7a-9742-eb173fa87f65)
```html
<script src="https://t1.kakaocdn.net/kakao_js_sdk/${VERSION}/kakao.min.js"
  integrity="${INTEGRITY_VALUE}" crossorigin="anonymous"></script>
```


이 script가 실행 된 이후 아래와 같이 초기화 구문을 작성해 주면 된다.

![image](https://github.com/user-attachments/assets/25217337-3d67-4d96-b01f-cd74b5751a07)
```js
Kakao.init('JAVASCRIPT_KEY');
Kakao.isInitialized();
```

### 실제 코드에 적용 해보기
1. 바닐라js
바닐라js 에서는 html 파일에 바로 넣어서 사용해주면 된다.
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>Kakao JavaScript SDK</title>
  <script src="https://t1.kakaocdn.net/kakao_js_sdk/${VERSION}/kakao.min.js"
    integrity="${INTEGRITY_VALUE}" crossorigin="anonymous"></script>
  <script>
    // SDK를 초기화 합니다. 사용할 앱의 JavaScript 키를 설정해야 합니다.
    Kakao.init('JAVASCRIPT_KEY');

    // SDK 초기화 여부를 판단합니다.
    console.log(Kakao.isInitialized());
  </script>
</head>
<body></body>
</html>
```

2. React

그렇다면 React에서는 어떻게 사용해야 할까?

위의 바닐라js환경에서 작성된 코드를 보면, html이 파싱될 때 카카오 SDK를 불러오고 초기화시키는 구문을 작성한 것을 볼 수 있다.

물론 React의 index.html에서도 위와 같은 방식으로 작성할 수 있지만, 카카오 sdk를 사용하는 프로젝트마다 별도로 작성해줘야하는 불편함이 있다.

이 문제를 해결하려면, React답게 파일 하나로 재사용이 가능하도록 만들어야 할 것 같다.

(1) script 분석

먼저 카카오 sdk를 불러오는 구문을 분석해보면, 아래와 같은 정보를 알 수 있다.
- 카카오 sdk는 **`<script/>` 요소**를 이용해 불러온다
- script 요소의 `src` 속성에 kakaoSdk경로를 설정해줘야 한다.
- script 요소의 `integrity` 속성에 integrity 값을 설정해주어야 한다. 
```html
<script src="https://t1.kakaocdn.net/kakao_js_sdk/${VERSION}/kakao.min.js"
  integrity="${INTEGRITY_VALUE}" crossorigin="anonymous"></script>
```

그 다음 sdk를 초기화 하는 구문을 분석해보면, 아래와 같은 정보를 알 수 있다.
- 내 애플리케이션의 `JAVASCRIPT_KEY`를 지정해 초기화 해주어야 한다.
```js
Kakao.init('JAVASCRIPT_KEY');
Kakao.isInitialized();
```

그럼 js로 구현할 때는 이런 순서로 진행해보면 될것 같다.
- script 요소 생성
- script의 src 속성에 kakaoSdk경로 지정
- script의 integrity 속성에 integrity 값 지정
- script 실행
- script가 실행되면 내 애플리케이션의 JAVASCRIPT_KEY를 지정해 초기화 진행

(2) 구현

카카오 sdk는 화면에 렌더링 된 순간부터 사용할 수 있어야 하므로

useEffect를 통해 컴포넌트가 mount 될 때 sdk를 불러오고, 초기화 시키는 구문을 작성하면 될 것 같다.

```js
export const useKakaoShare = () => {

  useEffect(() => {
    const JS_APP_KEY = import.meta.env.VITE_KAKAO_JS_KEY; //App Key는 중요한 값이므로 환경변수 처리하였다.
    const kakaoSdkUrl = 'https://t1.kakaocdn.net/kakao_js_sdk/2.7.5/kakao.min.js';
    const integrityValue =
      'sha384-dok87au0gKqJdxs7msEdBPNnKSRT+/mhTVzq+qOhcL464zXwvcrpjeWvyj1kCdq6';

    // 이미 로드되었는지 확인
    if (window.Kakao) {
      if (!window.Kakao.isInitialized()) {
        window.Kakao.init(JS_APP_KEY);
      }
      return;
    }

    const script = document.createElement('script');
    script.src = kakaoSdkUrl;
    script.integrity = integrityValue;
    script.crossOrigin = 'anonymous';
    script.onload = () => {
      if (window.Kakao && !window.Kakao.isInitialized()) {
        window.Kakao.init(JS_APP_KEY);
      }
    };
    document.head.appendChild(script);
  }, []);
}
```
결국 이런식으로 작성해서 마무리하긴 했는데..
사실 이 구문은 결과적으로 html에 직접 간섭하여 <script/> 요소를 직접 추가하는 구문이라서 이 구문이 딱히 React 다운 구문이라는 생각이 들진 않았다.

(3) 추후 다시 도전해 볼 내용

React의 기본 철학 중 가장 중요한 것은 역할 분리, 즉 개발자는 개발에만 집중하고, 요소의 제어는 React 시스템이 하도록 만들어 역할을 완전히 분리하게 만드는 것이다.
이것을 고려해 봤을 때 가장 React다운 해결 방법은 React-Helmet을 이용하는게 아닐까 싶다.
개발자는 Helmet에 kakao Sdk를 추가하는 script를 작성만 해주면, React 시스템이 html에 알아서 script를 추가하게 만드는 것이다.

아직 직접 해보지는 않았지만 추후에 다시 작업해 볼 생각이다.
```js
import { Helmet } from 'react-helmet'

export const KakaoScript = () => {
  return (
    <Helmet>
      <script src="https://t1.kakaocdn.net/kakao_js_sdk/${VERSION}/kakao.min.js"
      integrity="${INTEGRITY_VALUE}" crossorigin="anonymous"></script>
    </Helmet>
  )
}
```

```js

```

### ✅ JavaScript_key 환경변수 처리하기
기초 프로젝트를 진행하기 이전에 환경변수를 한번도 사용해 본 적이 없어서 어떤 값을 환경변수로 처리해야될 지 고민을 좀 했었던 것 같다.

#### ❓ 환경변수 적용 검토
먼저 Kakao Sdk를 사용하기 위해 필요한 값들은 아래와 같다.

- SDK Version을 포함한 참조 경로
- integrity 값
- 내 애플리케이션의 JAVASCRIPT_KEY 값

이 중 첫번째와 두번째는 kakao developers 사이트에서 최신값을 항상 제공해주기 때문에 환경변수로 관리할 필요가 없다고 느꼈다.

하지만 내 애플리케이션의 JAVASCRIPT_KEY 값은 내 애플리케이션의 고유 ID이기 때문에 환경변수로 관리헤야한다고 느꼈다.

#### ❓ 환경변수 지정 방법
나는 JAVASCRIPT_KEY 값을 환경변수로 사용하기로 결정했고, 환경변수가 필요한 곳이 어디인지 생각을 해보아야 한다.

우선 내가 작업하는 환경은 Node.js 기반의 Local 환경, 그리고 배포를 Vercel로 진행했기 때문에 Vercel에도 환경 변수를 등록해주어야 했다.

1. Local 환경에서 환경변수 지정하기
프로젝트의 최상위 폴더에 `.env.local` 파일을 생성하고, 환경변수로 사용할 변수 이름과 값을 지정해준다.

참고로, React + vite 빌드환경에서는 Vercel 배포할때 환경 변수 이름 앞에 `VITE`를 붙여주어야 오류가 나지 않는다고 한다.

```
VITE_KAKAO_JS_KEY=...
```

2. Vercel 환경에서 환경변수 지정하기
Vercel => 프로젝트 선택 => Settings => Environment Variables

환경변수 Key 이름과 Value를 작성해준 수 Save 버튼을 눌러 저장해주면 된다.

여기서 Environments는 `All Environments` 가 맞는지 확인해는 게 좋다.

`Products`로 설정하게 되면 Vercel의 Preview Link에서 작동하지 않는다.

![image](https://github.com/user-attachments/assets/5ab98b5b-5275-4a0c-b596-2c68db5e7be2)



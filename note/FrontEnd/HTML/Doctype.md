# 📝 DOCTYPE

<br></br>
## ✅ DOCTYPE란?
- Document Type Declaration의 약자로, 문서 형식 선언을 말한다. 즉, **문서의 유형을 정의하기 위해 사용하는 선언문**이다.

### 📖 DOCTYPE의 종류
HTML, XHTML, HTML5

## ✅ DOCTYPE의 역할
1. 웹 문서의 시작을 알림
2. 웹 브라우저에서 처리할 문서가 HTML이라는 것을 알림
3. 어떤 버전으로 작성했는지 알림
4. 위의 내용을 바탕으로 해석하라고 알림

- 즉, 웹 브라우저에게 어떤 버전의 HTML로 작성했는지 알려주는 중요한 역할을 한다.
- DOCTYPE이 없다면 웹 브라우저가 HTML을 잘못해석하거나 렌더링에 오류가 발생할 수 있다.
  
<br></br>
## ✅ 렌더링 모드
### 📖 렌더링 모드란?
- 웹페이지를 번역, 혹은 읽는 방식을 말한다.
- 다양한 웹 브라우저에서 웹표준에 따라 웹페이지를 올바르게 표시할 수 있도록 만들어주는 것

<br></br>
### 📖 렌더링 모드의 종류
|렌더링 모드|설명
|---|---|
|표준 모드(Standards Mode)| W3C의 최신 웹 표준을 정확하게 따름|
|부분 표준 모드(Almost Standards Mode)| 대부분 표준모드처럼 동작하지만, 일부 예외가 존재함(`line-height` 처리, `img` 태그의 줄 간격 등)|
|비 표준 모드 (Quirks Mode)| 과거 구형 브라우저 호환용 동작 방식으로, 표준과 다르게 렌더링 됨|

<br></br>
### 📖 HTML4의 렌더링 모드 설정
HTML4에서는 렌더링 모드에 대해 엄격하기 정의해야했기 때문에, 아래와 같이 DOCTYPE 선언문에 추가로 속성 기입이 필요했다.

```HTML
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Strict//EN"
  "http://www.w3.org/TR/html4/strict.dtd">
```
#### 🔹 DTD 참조 방식(PUBLIC & SYSTEM)

|종류|사용 방식| 설명
|---|---|---|
|PUBLIC| 공개 식별자 방식 | 공식적인 웹 표준 DTD를 참조할 때 사용 |
|SYSTEM| 시스템 식별자 방식 | 로컬 파일 경로나 내부 시스템에서 사용할 DTD를 참조할 때 사용 |

#### 🔹 식별자

  > 예시 : "-//W3C//DTD HTML 4.01 Strict//EN"
  
- W3C가 정의한 HTML 4.01버전의 Strict DTD를 따르겠다 라는 뜻이다.

|DTD의 종류| 방식 |
|---|---|
|Strict DTD|HTML4에서 더 이상 사용되지 않는 태그나 속성을 제외하고 표준을 준수하는 방식|
|Transitional DTD|HTML4에서 오래된 태그(ex: `<font>`, `<center>`와 속성을 사용하도록 허용하는 방식|
|Frameset DTD|프레임을 사용하는 html 페이지에 해당하는 DTD, 프레임셋을 정의하는 방식|

#### 🔹 Url

- W3C가 정의한 공식 DTD를 참조하기 위한 Url이다.
- URL을 정의하지 않으면 공식 DTD를 참조할 수 없기 때문에 부분 표준 모드로 동작한다.

<br></br>
### 📖 HTML5의 렌더링 모드 설정

DOCTYPE을 선언할 때는 `<!DOCTYPE>` 으로만 선언한다.
```HTML
<!DOCTYPE html>
<html>
  <!-- HTML5 문서내용 -->
</html>
```

#### 🔹 왜 간단해졌을까?
- HTML5에서는 모든 문서가 일관성 있게 표준모드에서 동작하도록 설계되어있다.
- 따라서 DOCTYPE을 복잡하게 설정할 필요가 없어진 것이다.

<!DOCTYPE html>을 기입하는 것 만으로도 **이 부분부터 문서의 시작이며 HTML5 문서이고, 표준모드로 렌더링 해라!** 라고 말하는 것과 같다.

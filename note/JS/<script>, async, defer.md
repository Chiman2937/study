# 📝 <script>, <script async>, <script defer>의 차이

<br></br>
## 차이점
| 방식               | 다운로드 시점                              | 실행 시점                       | DOM 파싱 영향                     | 실행 순서 (스크립트가 여러 개일 경우)       |
|--------------------|---------------------------------------------|----------------------------------|------------------------------------|----------------------------------------|
| `<script>`         | DOM 파싱을 중단하고 다운로드                 | 다운로드 후 즉시 실행            | ❌ DOM 파싱 중단됨                  | ✅ 작성 순서대로 실행                   |
| `<script async>`   | ✅ DOM 파싱과 병렬로 비동기 다운로드          | 다운로드 완료 후 즉시 실행       | ✅ DOM 파싱은 계속되나, 실행 타이밍 예측 불가 | ❌ 다운로드 완료 순서대로 실행 (순서 보장 없음) |
| `<script defer>`   | ✅ DOM 파싱과 병렬로 비동기 다운로드          | ✅ DOM 파싱 완료 후 (DOMContentLoaded 직전) 실행 | ✅ DOM 파싱 중단 없음                | ✅ 작성 순서대로 실행                   |

#### ※ async와 defer는 외부파일을 참조하는 방식만 사용가능하다.(src 속성 기입 필요)

> 참고: HTML 명세
>
> The `async` and `defer` content attributes **must be ignored if the src attribute is not present**.
>
> 출처: [WHATWG HTML spec – The script element](https://html.spec.whatwg.org/multipage/scripting.html#attr-script-async)

<br></br>
## ✔️ 예제

`<script>`는 보통 <body> 태그 내에 작성한다.

HTML파일을 파싱하면서 `<script>`를 만나면 HTML 파싱을 중단하고 바로 Javascript 파일을 실행한다.

이때 **<script> 하단에 작성된 DOM 요소들은 참조할 수 없다**는 문제가 발생한다.

```html
<body>
  <span id="hello">안녕</span>
  <script>
    console.log(document.querySelector('#hello').value); // <span id="hello">안녕</span>
    console.log(document.querySelector('#bye').value); // null
  </script>
  <span id="bye">잘가</span>
</body>
```

### 💡 해결방법1
#### ✅ `<script>`를 `<body>` 하단에 작성한다.

```html
  ...
  </body>
  <script>
    console.log(document.querySelector('#hello'));
    console.log(document.querySelector('#bye'));
  </script>
```
#### ❗ 해결방법1의 한계 및 단점
1. HTML 구조가 길어질수록 <script>의 위치를 찾기 어렵고 유지보수가 불편하다.

2. `<script>`는 동기적으로 실행되며, 동일 문서 내에 `<script async>`가 함께 존재할 경우 실행 순서가 예측되지 않을 수 있다.

3. `<script>`는 HTML 파싱을 중단시키며, 파싱 중간에 실행되면 DOM이 완성되기 전에 스크립트가 실행될 수 있다.

    ┗  스크립트가 오래 걸리는 작업을 포함할 경우, 사용자는 빈 화면을 오래 보게 되어 초기 로딩 성능이 저하된다.




### 💡 해결방법2
DOMContentLoad 를 이용해 DOM Tree가 완성된 후 함수가 실행되게 한다.

```html
  <body>
    <span id="hello">안녕</span>
    <script>
      document.addEventListener('DOMContentLoaded', function () {
        console.log(document.querySelector('#hello'));
        console.log(document.querySelector('#bye'));
      });
    </script>
    <span id="bye">잘가</span>
  </body>
```

#### ❓ 해결방법2의 문제점
스크립트마다 매번 구문을 추가로 작성해줘야한다.
`document.addEventListener('DOMContentLoaded', function () {});`
`<script>`만 작성할 때의 단점에서 `<script>`의 하단에 작성된 DOM요소를 참조 할 수 있다는 점 외에는 개선점이 없다

### 💡 해결방법3
`<script async>`를 이용한다.

index.html
```html
    ...
    <script async src="./console.js"></script>
  </head>
  <body>
    <span id="hello">안녕</span>
    <span id="bye">잘가</span>
  </body>
```

console.js
```js
console.log(document.querySelector('#hello'));
console.log(document.querySelector('#bye'));
```

#### ❓ 해결방법3의 문제점
script async는 비동기로 파일이 다운로드 되고, 스크립트가 실행될 때 DOM 파싱을 멈추지 않는다.
스크립트가 너무 빨리 다운로드되면 DOM요소가 생성되기 전에 스크립트가 실행될 수 있어 오류가 발생할 수 있다.
script async가 여러개 있을 경우, 먼저 다운로드가 끝난 파일부터 실행되기 때문에 실행순서를 보장할 수 없다.
따라서 script async는 주로 순서와 상관없는 구문을 실행 할때 사용한다.(웹 분석 도구, 광고 스크립트 등)

### 💡 해결방법4
`<script defer>`를 사용한다.
```html
    ...
    <script defer src="./console.js"></script>
  </head>
  <body>
    <span id="hello">안녕</span>
    <span id="bye">잘가</span>
  </body>
```

console.js
```js
console.log(document.querySelector('#hello'));
console.log(document.querySelector('#bye'));
```
1. `<script defer>`는 비동기로 파일이 다운로드 되며 DOM 파싱을 멈추지 않는다.(성능 최적화 유리)
2. 하지만 DOM 파싱이 끝난 뒤 스크립트 파일을 실행된다.(DOM
3. 또한 `<script defer>`가 여러개 있을 경우, 무조건 작성순서를 따른다(실행 순서 보장)

성능 최적화에 유리하며, 스크립트의 실행순서를 보장하는 defer를 사용하는 방법이 보편적이다.

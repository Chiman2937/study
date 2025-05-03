
# 📝 this

## 📌 this란?

`this`는 실행 시점에 **자신에게 일을 시킨 객체**를 가리키는 **참조 변수**이다.  
함수가 **어떻게 호출되었느냐**에 따라 **동적으로 바인딩**되며,  
**호출 주체**에 따라 `this`가 가리키는 대상이 달라진다.

---

## 📊 상황별 this가 가리키는 대상

| 상황                 | this가 가리키는 대상                     |
|----------------------|-------------------------------------------|
| 일반 함수 단독 호출   | 전역 객체 (`window` / `global`) <br> `strict mode`에서는 `undefined` |
| 객체의 메서드 호출   | 해당 객체                                 |
| 생성자 함수 호출 (new) | 새로 생성된 인스턴스 객체                 |
| call, apply, bind 사용 | 명시적으로 지정한 객체                    |
| 화살표 함수          | 선언 당시 **외부 렉시컬 스코프의 `this`** |

---

## 🔍 전역 컨텍스트에서의 this

```js
console.log(this);
```
### 출력 결과
```console
`window` // 브라우저 환경  
`global` //Node.js 환경
`undefined` //strict mode
```
---

## 🔍 일반 함수에서의 this

```js
function newFunc() {
  console.log(this);
}
newFunc();
```

- 전역에서 호출되므로 `this === 전역 객체`
- 브라우저: `window`  
- Node.js: `global`  
- strict mode: `undefined`

---

## 🔍 객체 메서드로 호출한 경우

```js
const obj = {
  method: newFunc,
};

function newFunc() {
  console.log(this);
}
obj.method(); // obj가 호출 주체
```

- 호출한 주체가 `obj`이므로 `this === obj`

---

## 🔍 생성자 함수에서의 this

```js
function NewFunc(name) {
  this.name = name;
  console.log(this);
}
const obj = new NewFunc('Kim');
```

### 출력 결과:
```console
NewFunc { name: 'Kim' }
```

**설명**:

1. `obj`는 `NewFunc` 생성자 함수로 만들어진 인스턴스 객체이다.
2. 생성자 함수 내부에서 `this`는 생성된 인스턴스인 `obj`를 가리킨다.
3. 따라서 `console.log(this)`는 `obj`를 출력한다.
4. `name` 프로퍼티가 추가되었으므로 `NewFunc { name: 'Kim' }` 형태로 출력된다.

---

## 🔍 call, apply, bind

```js
function newFunc() {
  console.log(this.name);
}
newFunc.call({ name: 'Kim' });
```

- `call`, `apply`, `bind`는 **this를 명시적으로 바인딩**한다.
- 비유하자면:
  
  > 전역 객체가 “야, 너는 지금부터 나라고 생각하고 이 함수 좀 대신 실행해줘”라고 전달된 객체에 지시한 것과 같다.
- 따라서 `this.name`은 `'Kim'`이 되고 `'Kim'`이 출력된다.

---

## 🔍 화살표 함수에서의 this

```js
const newFunc = () => {
  console.log(this);
};
newFunc();
```

### 출력 결과:
```console
{}      // Node.js
window  // 브라우저
```

- 화살표 함수는 자신만의 `this`를 갖지 않는다.
- 대신 **선언된 위치의 상위 스코프의 `this`를 상속**한다.

**환경에 따라 상속된 this는 다르다**:

- **Node.js**: 모듈 스코프의 `this`는 `{}` → 결과도 `{}`  
- **브라우저**: 최상위 스코프가 곧 전역 객체 `window` → 결과도 `window`

---

git cherry-pick

체리픽이란?
다른 브랜치에 있는 커밋 중 특정 커밋만 선택하여 현재 브랜치에 적용하는 명령어이다.
선택된 커밋들은 현재 브랜치의 새로운 커밋으로 복사되며 이 과정에서 새로운 커밋 해시가 생성된다.

cherry-pick 명령어 사용 방법
cherry-pick 명령어 사용할 때는 커밋을 복사해올 브랜치에 먼저 checkout 하고,
복사할 커밋해시를 입력해주면 된다.

```
git checkout [브랜치]
git cherry-pick [복사할 커밋해시]
```

## 예제

- `main` 브랜치에서 calculator.js파일을 생성, `add 함수 추가` 후 commit(`8147ed3`)
- `main` 브랜치에서 calculator.js파일에 `remain 함수 추가` 후 commit(`d01ddb1`)
- `8147ed3` 커밋에서 `feat 브랜치로 분기`, calculator.js파일에 `substract 함수 추가` 후 commit(`5102aa8`)
- `feat` 브랜치에서 calculator.js파일에 `multiply 함수 추가 후` commit(`435b233`)
- `feat` 브랜치에서 calculator.js파일에 `divide 함수 추가 후` commit(`0748dfb`)
![git cherry-pick](https://github.com/user-attachments/assets/034afb80-bc09-45f8-aa0b-53fb481b50bf)

### 예제1
main 브랜치에 feat 브랜치의 divide함수 복사해오기
1. main 브랜치에 커밋을 추가할 것이기 때문에 `main 브랜치에 checkout`
2. divide 함수를 추가한 커밋(`0748dfb`)만 cherry-pick 해서 가져오면 된다.

```
git checkout main
git cherry-pick 0748dfb
```
0748dfb 커밋의 내용을 그대로 복사하여 main 브랜치에 새로운 커밋으로 적용한다.
![git cherry-pick (1)](https://github.com/user-attachments/assets/57f65f45-3f3a-4223-ba46-5534ff276e9c)

### 예제2
main 브랜치에 feat 브랜치의 subtract, multiply 함수 복사해오기
1. main 브랜치에 커밋을 추가할 것이기 때문에 `main 브랜치에 checkout`
2. subtract, multiply 함수를 추가한 커밋(`5102aa8`,`435b233`)을 cherry-pick 해서 가져오면 된다.

이때 여러개의 커밋을 복사할 때는 아래와 같이 두가지의 명령어를 이용할 수 있다.
연속된 커밋 복사
```
git cherry-pick [시작커밋해시]^..[마지막커밋해시]
```
개별 커밋 복사
```
git cherry-pick [커밋해시1] [커밋해시2] [커밋해시3]
```
예제에서 복사할 커밋은 연속된 커밋이므로 아래와 같이 명령어를 입력할 수 있다.
```
git checkout main
git cherry-pick 5102aa8^..435b233
```
그런데 이렇게 cherry-pick 명령어를 입력하면 conflict가 발생하게 된다.
conflict가 발생한 파일을 열어보면 아래와 같이 보이는걸 확인할 수 있다.
```js
const add = (a, b) => {
  return a + b;
};
<<<<<<< HEAD

const remain = (a, b) => {
  return a % b;
};
const divide = (a, b) => {
  return a / b;
=======
const subtract = (a, b) => {
  return a - b;
>>>>>>> 5102aa8 (update calculator.js(add subtract function))
};

```
여기서 이런 의문이 생겼다.
1. subtract, multiply 함수는 기존에 main 브랜치에는 없던 함수인데 왜 conflict가 발생한걸까?
2. conflict된 내용에 왜 

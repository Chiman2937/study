# 📝 undefined와 null
> 원문 : https://codingeverybody.kr/%ec%9e%90%eb%b0%94%ec%8a%a4%ed%81%ac%eb%a6%bd%ed%8a%b8-undefined%ec%99%80-null-%ea%b0%92%ec%9d%98-%ec%b0%a8%ec%9d%b4%ec%a0%90/

<br></br>
## ✅ undefined와 null

|구분|개념|
|---|---|
|✔️ undefined|변수에 값이 할당되지 않았다는 의미로, 자바스크립트 엔진에서 자동으로 할당하는 초깃값|
|✔️ null|변수에 값이 명시적으로나 의도적으로 없음을 나타내는 값|

<br></br>
## ✅ undefined와 null의 차이점

|구분|undefined|null|
|---|---|---|
|데이터 타입|undefined 타입(undefined의 유일한 원시값)|null 타입(null 타입의 유일한 원시값)|
|정의|변수가 값에 할당되지 않았다는 의미|변수에 값이 없음을 **의도적으로** 나타내는 값|
|주요 차이점|undefined 값은 자바스크립트에서 값이 할당되지 않은 변수를 나타내기 위해 도입됨|객체를 반환할 것으로 예상되지만 실제로 관련된 객체가 존재하지 않을 때의 값으로 null을 사용|
|변수에 값으로 할당| 권장하지 않음 | 의도적으로 값이 없음을 명시할 때 적절함 |
|typeof로 데이터 타입을 확인하면|undefined|object|

<br></br>
## ✅ 예제
### ✔️ 예제1

```javascript
let x; // 변수에 값을 할당하지 않음
console.log(x); // 변수에 값이 할당되지 않았다는 의미로 undefined 반환

let y = null; // 의도적으로 값이 없다는 것을 나타내는 의미로 null 할당
console.log(y); // null
```

### ✔️ 예제2
```javascript
function test(){ };
console.log(test());
```

<details>
<summary></summary>
  
- 결과: undefined

  > test 함수를 호출하였지만 return 값이 없기 때문에 test()의 결과에 값이 할당되지 않음
</details>


  
<br></br>
### ✔️ 예제3
```javascript
function test(a){
  return a;
};
console.log(test());
```

<details>
<summary></summary>
  
- 결과: undefined

  > test 함수가 실행될 때 매개변수가 a가 선언되었지만, a에 값이 할당되지 않음
</details>
  
<br></br>
### ✔️ 예제4
```javascript
//text 클래스를 가진 요소가 없다고 가정
console.log(document.querySelector('.text'));
```

<details>
<summary></summary>
  
- 결과: null

  > 객체를 반환할 것으로 예상되지만 실제로 관련된 객체가 존재하지 않을 때의 값으로 null을 사용
</details>

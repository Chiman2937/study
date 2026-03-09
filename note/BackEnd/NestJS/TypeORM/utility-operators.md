# TypeORM 유틸리티 연산자

`where` 조건절에서 사용하는 TypeORM 내장 유틸리티 함수들.

```ts
import { Not, Like, ILike, IsNull, LessThan, MoreThan, Between, In } from 'typeorm';

await this.userRepository.find({
  where: {
    id: Not(1),
    email: Like('%0%'),
    additionalId: IsNull(),
  },
});
```

---

## 비교 연산자

### 1. `Not`
해당 값이 아닌 경우.

```ts
where: { id: Not(1) }
// id가 1이 아닌 모든 데이터
```

### 2. `LessThan`
해당 값보다 작은 경우.

```ts
where: { count: LessThan(10) }
// count < 10
```

### 3. `LessThanOrEqual`
해당 값보다 작거나 같은 경우.

```ts
where: { count: LessThanOrEqual(10) }
// count <= 10
```

### 4. `MoreThan`
해당 값보다 큰 경우.

```ts
where: { count: MoreThan(10) }
// count > 10
```

### 5. `MoreThanOrEqual`
해당 값보다 크거나 같은 경우.

```ts
where: { count: MoreThanOrEqual(10) }
// count >= 10
```

### 6. `Equal`
해당 값과 같은 경우. 값 직접 할당과 동일하게 동작한다.

```ts
where: { id: Equal(1) }
// where: { id: 1 } 과 동일
```

---

## 문자열 검색

### 7. `Like`
유사한 값을 검색. **대소문자를 구분한다.**
`%`는 와일드카드로, 임의의 문자열을 의미한다.

```ts
where: { email: Like('%gmail.com') }
// 'gmail.com'으로 끝나는 email (대소문자 구분)

where: { name: Like('%kim%') }
// 'kim'을 포함하는 name
```

### 8. `ILike`
유사한 값을 검색. **대소문자를 구분하지 않는다.**

```ts
where: { email: ILike('%GMAIL.com') }
// 'gmail.com', 'GMAIL.COM' 등 모두 매칭
```

---

## 범위 / 포함

### 9. `Between`
두 값 사이에 있는 경우 (경계값 포함).

```ts
where: { count: Between(1, 10) }
// 1 <= count <= 10
```

### 10. `In`
배열에 포함되는 값인 경우.

```ts
where: { id: In([1, 2, 3]) }
// id가 1, 2, 3 중 하나인 데이터
```

---

## null 체크

### 11. `IsNull`
값이 null인 경우.

```ts
where: { additionalId: IsNull() }
// additionalId가 null인 데이터
```

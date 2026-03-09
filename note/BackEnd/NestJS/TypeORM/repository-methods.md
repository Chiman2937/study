# TypeORM Repository 메서드

## 데이터 생성 / 수정

### 1. `create`
객체를 생성만 하고 데이터베이스에는 저장하지 않음.

```ts
const user = this.userRepository.create({
  email: 'test@gmail.com',
});
// DB 저장 X, 엔티티 인스턴스만 반환
```

### 2. `save`
객체를 생성하고 데이터베이스에 저장함. 이미 존재하면 업데이트.

```ts
const user = await this.userRepository.save({
  email: 'test@gmail.com',
});
// DB 저장 O
```

### 3. `preload`
입력된 값을 기반으로 DB에서 데이터를 불러온 뒤, 추가로 입력된 값으로 덮어씌움.
**DB에 저장하지 않음** (저장하려면 `save`를 추가로 호출해야 함).

```ts
const user = await this.userRepository.preload({
  id: 1,
  email: 'new@gmail.com',
});
// id=1인 데이터를 DB에서 가져온 뒤, email을 'new@gmail.com'으로 대체
// DB 저장 X
```

---

## 데이터 삭제

### 4. `delete`
조건에 맞는 데이터를 데이터베이스에서 삭제.

```ts
await this.userRepository.delete({
  id: 101,
});
```

---

## 값 증감

### 5. `increment`
조건에 맞는 데이터의 특정 프로퍼티 값을 증가시킴.

```ts
await this.userRepository.increment(
  { id: 1 },  // 조건
  'count',    // 대상 프로퍼티
  1,          // 증가량
);
// id=1인 데이터의 count를 1 증가
```

### 6. `decrement`
조건에 맞는 데이터의 특정 프로퍼티 값을 감소시킴.

```ts
await this.userRepository.decrement(
  { id: 1 },
  'count',
  1,
);
// id=1인 데이터의 count를 1 감소
```

---

## 집계

### 7. `count`
조건을 만족하는 데이터의 개수를 반환.

```ts
const total = await this.userRepository.count({
  where: {
    email: Like('%0%'),
  },
});
```

### 8. `sum`
조건을 만족하는 데이터의 특정 프로퍼티 값의 합계를 반환.

```ts
const total = await this.userRepository.sum('count', {
  email: ILike('%0%'),
});
```

### 9. `average`
조건을 만족하는 데이터의 특정 프로퍼티의 평균값을 반환.

```ts
const avg = await this.userRepository.average('count', {
  id: LessThan(4),
});
```

### 10. `minimum`
조건을 만족하는 데이터의 특정 프로퍼티의 최솟값을 반환.

```ts
const min = await this.userRepository.minimum('count', {
  id: LessThan(4),
});
```

### 11. `maximum`
조건을 만족하는 데이터의 특정 프로퍼티의 최댓값을 반환.

```ts
const max = await this.userRepository.maximum('count', {
  id: LessThan(4),
});
```

---

## 데이터 조회

### 12. `find`
조건을 만족하는 데이터 목록을 반환.

```ts
const users = await this.userRepository.find({
  where: { email: Like('%test%') },
});
```

### 13. `findOne`
조건을 만족하는 단 하나의 데이터만 반환.

```ts
const user = await this.userRepository.findOne({
  where: { id: 1 },
});
```

### 14. `findAndCount`
조건을 만족하는 데이터 목록과 전체 개수를 함께 반환.
페이지네이션 구현에 주로 사용.

```ts
const [users, total] = await this.userRepository.findAndCount({
  take: 20,   // 가져올 개수
  skip: 0,    // 건너뛸 개수
});
// users: 데이터 배열, total: 전체 조건 만족 데이터 수
```

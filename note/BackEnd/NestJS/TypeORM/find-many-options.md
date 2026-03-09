# TypeORM FindManyOptions

`find()`에 전달하는 옵션 객체. 조회 조건, 정렬, 페이지네이션 등을 설정한다.

```ts
this.userRepository.find({
  select: { id: true },
  where: { id: 1 },
  relations: { profile: true },
  order: { id: 'ASC' },
  skip: 0,
  take: 20,
});
```

---

## 조회 설정

### 1. `select`
가져올 프로퍼티를 지정한다. SQL의 `SELECT`에 해당.

- 정의하지 않으면 모든 프로퍼티를 가져온다.
- 정의하면 지정된 프로퍼티만 가져온다.

```ts
select: {
  id: true,
  email: true,
}
// id, email 컬럼만 반환
```

### 2. `relations`
연관 엔티티를 함께 가져온다. SQL의 `JOIN`에 해당.

```ts
relations: {
  profile: true,
}
// user와 연관된 profile 데이터를 함께 조회
```

---

## 필터링

### 3. `where`
조회 조건을 설정한다. SQL의 `WHERE`에 해당.

**AND 조건**: 하나의 객체 안에 여러 조건을 작성하면 AND로 묶인다.

```ts
where: {
  id: 1,
  email: 'test@gmail.com',
}
// id = 1 AND email = 'test@gmail.com'
```

**OR 조건**: 배열로 여러 객체를 작성하면 OR로 묶인다.

```ts
where: [
  { id: 1 },
  { id: 2 },
]
// id = 1 OR id = 2
```

---

## 정렬

### 4. `order`
정렬 기준과 방향을 설정한다. SQL의 `ORDER BY`에 해당.

- `ASC`: 오름차순
- `DESC`: 내림차순

```ts
order: {
  id: 'ASC',
  createdAt: 'DESC',
}
```

---

## 페이지네이션

### 5. `skip`
처음 몇 개를 건너뛸지 설정한다. (기본값: `0`)

### 6. `take`
몇 개를 가져올지 설정한다. (기본값: `0` = 전체 데이터)

```ts
skip: 20,  // 앞의 20개를 건너뜀
take: 10,  // 그 이후 10개를 가져옴
// → 3페이지 (21~30번째 데이터)
```

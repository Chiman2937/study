# TypeORM Relations

## OneToOne

### 1. 기본 설정

두 엔티티가 1:1로 연결될 때 사용. **`@JoinColumn()`이 붙은 쪽의 테이블에 FK 컬럼이 생성된다.**

```ts
// user.entity.ts
@Entity()
export class UsersModel {
  @OneToOne(() => ProfileModel, (profile) => profile.user)
  @JoinColumn() // 이 엔티티 테이블에 FK 컬럼(profileId) 생성
  profile: ProfileModel;
}
```

```ts
// profile.entity.ts
@Entity()
export class ProfileModel {
  @OneToOne(() => UsersModel, (user) => user.profile)
  user: UsersModel; // @JoinColumn 없음 → FK 컬럼 없음
}
```

- 첫 번째 인자: 연결할 Model
- 두 번째 인자: 해당 Model에서 연결되는 프로퍼티 (양방향 참조 설정)
- `@JoinColumn()`은 **반드시 한 쪽에만** 붙여야 하며, FK를 관리하는 주체가 된다.

```ts
// FK 컬럼명 커스텀 가능
@JoinColumn({ name: 'profile_id' }) // 기본값: profileId
```

---

## OneToMany / ManyToOne

### 1. 기본 설정

1:N 관계. **`ManyToOne` 쪽에 자동으로 FK 컬럼이 생성된다** (`@JoinColumn` 불필요).

```ts
// users.entity.ts
@Entity()
export class UsersModel {
  @OneToMany(() => PostsModel, (post) => post.author)
  posts: PostsModel[]; // 한 유저가 여러 게시글 작성 가능
}
```

```ts
// posts.entity.ts
@Entity()
export class PostsModel {
  @ManyToOne(() => UsersModel, (user) => user.posts)
  author: UsersModel; // 이 테이블에 authorId(FK) 컬럼 자동 생성
}
```

**결정 기준**: 현재 엔티티와 대상 엔티티의 관계가 N:1이면 `ManyToOne`, 1:N이면 `OneToMany`.

예시) 유저(1) : 게시글(N)
- `UsersModel` 기준 → `OneToMany`
- `PostsModel` 기준 → `ManyToOne`

> `OneToMany`만 단독으로는 사용 불가. 반드시 `ManyToOne`과 쌍으로 사용해야 한다.

---

## ManyToMany

### 1. 기본 설정

N:M 관계. **`@JoinTable()`이 붙은 쪽에서 중간(Junction) 테이블을 자동으로 생성한다.**

```ts
// post.entity.ts
@Entity()
export class PostsModel {
  @ManyToMany(() => TagModel, (tag) => tag.posts)
  @JoinTable() // 중간 테이블 생성 담당 (posts_tags_tags 형태)
  tags: TagModel[];
}
```

```ts
// tag.entity.ts
@Entity()
export class TagModel {
  @ManyToMany(() => PostsModel, (post) => post.tags)
  posts: PostsModel[];
}
```

---

## 공통 옵션

| 옵션 | 설명 |
|---|---|
| `cascade` | 부모 저장/삭제 시 자식에도 자동 반영 |
| `eager` | find() 시 관계 엔티티 자동 로딩. 기본값 `false` |
| `nullable` | FK null 허용 여부. 기본값 `true` |
| `onDelete` | 부모 삭제 시 동작: `CASCADE` / `SET NULL` / `RESTRICT` |

### 1. `cascade`

부모 엔티티를 저장/삭제할 때 자식 엔티티에도 자동으로 반영.

```ts
@OneToMany(() => PostsModel, (post) => post.author, {
  cascade: true, // save, remove 등 자동 전파
})
posts: PostsModel[];
```

### 2. `eager`

`find()` 호출 시 관계 엔티티를 자동으로 함께 로딩. 기본값은 `false`.

```ts
@ManyToOne(() => UsersModel, (user) => user.posts, {
  eager: true, // relations 옵션 없이도 자동 로딩
})
author: UsersModel;
```

> `eager: true`는 항상 로딩되므로 불필요한 경우 성능에 영향을 줄 수 있다. 필요할 때만 사용하는 것이 권장된다.

### 3. `nullable`

FK 컬럼의 NULL 허용 여부. 기본값은 `true`.

```ts
@ManyToOne(() => UsersModel, (user) => user.posts, {
  nullable: false, // author 없는 게시글 불가
})
author: UsersModel;
```

### 4. `onDelete`

부모 엔티티 삭제 시 자식 엔티티의 처리 방식.

```ts
@ManyToOne(() => UsersModel, (user) => user.posts, {
  onDelete: 'CASCADE',   // 부모 삭제 시 자식도 함께 삭제
  // onDelete: 'SET NULL',   // 자식의 FK를 NULL로 설정
  // onDelete: 'RESTRICT',   // 자식이 있으면 부모 삭제 불가 (기본값)
})
author: UsersModel;
```

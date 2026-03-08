# TypeORM Column

## 1. Column Annotation

```ts
import {
  Column,
  CreateDateColumn,
  Entity,
  Generated,
  PrimaryGeneratedColumn,
  UpdateDateColumn,
  VersionColumn,
} from 'typeorm';

@Entity()
export class UserModel {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;

  @VersionColumn()
  version: number;

  @Column()
  @Generated('uuid')
  additionalId: number;
}
```

| Annotation | 설명 |
|---|---|
| `@PrimaryGeneratedColumn()` | ID 자동 생성 (1, 2, 3... 순차 증가) |
| `@PrimaryGeneratedColumn('uuid')` | UUID 형태로 ID 자동 생성 (e.g. `asd123-...`) |
| `@PrimaryColumn()` | 모든 테이블에 반드시 존재해야 하는 기본키 컬럼 (Row 구분) |
| `@CreateDateColumn()` | 데이터 생성 날짜/시간 자동 기록 |
| `@UpdateDateColumn()` | 데이터 수정 날짜/시간 자동 기록 |
| `@VersionColumn()` | 업데이트 시마다 1씩 증가. 최초 생성 시 값 = 1 |
| `@Column() + @Generated('increment')` | PrimaryGeneratedColumn에서 Primary 기능만 뺀 것 |

---

## 2. Column Property

```ts
@Column({
  type: 'varchar',
  name: '_title',
  length: 300,
  nullable: true,
  update: true,
  select: true,
  default: 'default value',
  unique: true,
})
title: string;
```

| 옵션 | 설명 |
|---|---|
| `type` | DB에서 인지하는 컬럼 타입 직접 지정 |
| `name` | DB 컬럼 이름 매핑. e.g. `name: '_title'` → DB 컬럼명은 `_title` |
| `length` | 입력 가능한 최대 글자 수 (number) |
| `nullable` | null 허용 여부 (boolean) |
| `update` | false 시 최초 생성 때만 입력 가능, 이후 수정 불가. **v0.3.20 이후 수정 시도해도 에러 없이 조용히 실패하는 버그 있음** |
| `select` | 기본값 true. find/findOne 조회 시 해당 컬럼 포함 여부 |
| `default` | 값 미입력 시 기본값 |
| `unique` | 기본값 false. 컬럼 값의 유일성 강제 (e.g. email 필드) |

---

## 3. Enum Column

```ts
export enum Role {
  USER = 'user',
  ADMIN = 'admin',
}

@Entity()
export class UserModel {
  @Column({
    type: 'enum',
    enum: Role,
    default: Role.USER,
  })
  role: Role;
}
```

- 필드 값을 enum으로 강제할 수 있음

---

## 4. Entity Embedding

```ts
export class Name {
  @Column()
  first: string;

  @Column()
  last: string;
}

@Entity()
export class StudentModel {
  @PrimaryGeneratedColumn()
  id: number;

  @Column(() => Name)
  name: Name;

  @Column()
  class: string;
}

@Entity()
export class TeacherModel {
  @PrimaryGeneratedColumn()
  id: number;

  @Column(() => Name)
  name: Name;

  @Column()
  salary: number;
}
```

- 공통 컬럼을 별도 클래스로 분리해 중복 제거
- 실제 테이블 생성 시 `nameFirst`, `nameLast` 형태로 컬럼명 생성됨

---

## 5. Table Inheritance (상속)

### (1) 일반 Inheritance

```ts
export class BaseModel {
  @PrimaryGeneratedColumn()
  id: number;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}

@Entity()
export class BookModel extends BaseModel {
  @Column()
  name: string;
}

@Entity()
export class CarModel extends BaseModel {
  @Column()
  brand: string;
}
```

- `BookModel`, `CarModel` 두 개의 테이블이 각각 생성됨
- 두 테이블 모두 `BaseModel`의 컬럼을 그대로 포함

### (2) Single Table Inheritance

```ts
@Entity()
@TableInheritance({
  column: { name: 'type', type: 'varchar' },
})
export class SingleBaseModel {
  @PrimaryGeneratedColumn()
  id: number;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}

@ChildEntity()
export class ComputerModel extends SingleBaseModel {
  @Column()
  brand: string;
}

@ChildEntity()
export class AirplaneModel extends SingleBaseModel {
  @Column()
  country: string;
}
```

- 테이블 **하나만** 생성됨 (`SingleBaseModel`)
- `brand`, `country` 컬럼이 같은 테이블에 공존
- `type` 컬럼으로 `ComputerModel` / `AirplaneModel` 구분
- 서로 다른 유형의 데이터를 **하나의 테이블**에서 관리할 때 사용

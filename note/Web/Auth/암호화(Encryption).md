# 📃 암호화(Encryption)

## 1. 암호화 알고리즘 종류

| 알고리즘 | 특징 | 권장 여부 |
|---|---|---|
| `md5` | 빠름, 취약점 다수 발견 | ❌ 비권장 |
| `sha1` | 빠름, 충돌 공격에 취약 | ❌ 비권장 |
| `bcrypt` | 의도적으로 느리게 설계, salt 내장 | ✅ 권장 |

### 1-1. bcrypt를 사용하는 이유

의도적으로 느리게 실행되도록 설계된 알고리즘. **느릴수록 해킹에 걸리는 시간이 기하급수적으로 늘어난다.**

```ts
import * as bcrypt from 'bcrypt';

const password = 'chiman';
const saltRounds = 10; // 높을수록 느려짐 (밸런스 중요)

// 해시 생성
const hash = await bcrypt.hash(password, saltRounds);
// 'chiman' => '$2b$10$asdf1231...' (hash 값)

// 비밀번호 검증
const isMatch = await bcrypt.compare('chiman', hash);
// true
```

---

## 2. 비밀번호 암호화가 필요한 이유

### 2-1. 데이터베이스 탈취 시나리오

해커가 DB를 탈취했을 때, **평문 비밀번호가 저장되어 있으면 다른 사이트에서도 동일하게 사용 가능**하다.

```
[평문 저장 시 - 위험]
email              | password
chiman@gmail.com   | asdf1234!   ← 그대로 다른 사이트에서 사용 가능
```

```
[암호화 저장 시 - 안전]
email              | password
chiman@gmail.com   | ajsdgnlkqweu...  ← 다른 사이트에서 사용 불가
```

### 2-2. 비밀번호 검증 방법

원본 비밀번호는 절대 저장하지 않는다. **항상 해시된 값끼리 비교**한다.

```ts
// 로그인 시
const inputPassword = 'asdf1234!';
const storedHash = user.password; // DB에 저장된 해시

const isValid = await bcrypt.compare(inputPassword, storedHash);
// bcrypt가 내부적으로 같은 조건으로 해시 후 비교
```

---

## 3. Dictionary Attack & Salt

### 3-1. Dictionary Attack (사전 공격)

같은 조건이면 **항상 같은 해시가 생성**되는 특성을 이용한 공격.

1. 자주 사용되는 비밀번호들의 해시 테이블을 미리 생성 (Rainbow Table)
2. 탈취한 DB의 해시값과 비교하여 원본 비밀번호 추론

```
[미리 만들어둔 해시 테이블]
password123  =>  482c811da...
qwerty       =>  d8578edf8...
123456       =>  e10adc394...
```

### 3-2. Salt로 방어

Salt는 해시 생성 시 **비밀(secret)로 설정한 문자열을 비밀번호에 추가**하여 Rainbow Table 공격을 무력화한다.

```
비밀번호:  999aaazzz
Salt:      chiman (secret으로 설정)

999aaazzz + chiman => bcrypt => akjlsdfnqwipou...
```

**Salt가 섞이면 같은 비밀번호도 다른 해시가 생성되므로, 미리 만들어둔 해시 테이블이 무의미해진다.**

### 3-3. Salt까지 탈취되면?

Salt가 털려도 bcrypt의 **의도적인 느린 속도** 덕분에 새 Rainbow Table을 만드는 데 현실적으로 불가능한 시간이 소요된다.

```
[bcrypt의 방어 원리]
Salt 탈취 → 새 Rainbow Table 생성 시도
→ bcrypt가 매우 느려서 수억 개 해시 생성에 수백 년 소요
→ 사실상 원본 비밀번호 역추적 불가
```

---

## 4. bcrypt 속도 밸런스

### 4-1. saltRounds 선택 기준

`saltRounds`(cost factor)가 높을수록 느려진다. **느릴수록 안전하지만, 사용자 경험에도 영향**을 미친다.

| saltRounds | 해시 생성 시간 (대략) | 비고 |
|---|---|---|
| `8` | ~40ms | 빠름, 보안 다소 낮음 |
| `10` | ~100ms | **일반적인 권장값** |
| `12` | ~400ms | 보안 높음, 로그인 느려짐 |
| `14` | ~1.5s | 매우 느림 |

```ts
// 권장 설정
const SALT_ROUNDS = 10;

async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(input: string, hash: string): Promise<boolean> {
  return bcrypt.compare(input, hash);
}
```

> **핵심**: bcrypt는 해시 + salt + 느린 속도의 조합으로, DB가 전부 털려도 원본 비밀번호가 노출될 확률을 현저히 낮춘다.

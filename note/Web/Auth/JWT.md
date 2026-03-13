# 📃 JWT (JSON Web Token)

## 1. JWT 구조

JWT는 점(`.`)으로 구분되는 **세 부분**으로 구성된다.

```
Header.Payload.Signature
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6ImNvZGVmYWN0b3J5QGdtYWlsLmNvbSIsInN1YiI6MiwidHlwZSI6ImFjY2VzcyIsImlhdCI6MTc3MzQwNjc1MSwiZXhwIjoxNzczNDA3MDUxfQ.서명값
```

| 부분 | 인코딩 방식 | 역할 |
|---|---|---|
| Header | Base64 | 알고리즘 및 토큰 타입 정보 |
| Payload | Base64 | 실제 담을 데이터 |
| Signature | HMAC 등 | 변조 여부 검증 |

---

## 2. Header

### 2-1. Header 구성

사용할 알고리즘(`alg`)과 토큰 타입(`typ`)을 기록한다. **보통 따로 설정할 일은 없다.**

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

---

## 3. Payload

### 3-1. Payload 구성

등록하고 싶은 데이터를 자유롭게 기입하는 부분이다.

```json
{
  "email": "codefactory@gmail.com",
  "sub": 2,       // sub = 사용자 id
  "type": "access",
  "iat": 1773406751,  // 발급 시각 (issued at)
  "exp": 1773407051   // 만료 시각 (expiration)
}
```

> **주의**: Header와 Payload는 Base64 인코딩이므로, **JWT 값을 알면 누구든 디코딩해서 내용을 볼 수 있다.** 민감한 정보는 담지 않는다.

---

## 4. Signature

### 4-1. Signature 생성 방식

Header, Payload, **서버의 비밀키**를 합쳐 알고리즘으로 인코딩한 값이다.

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  비밀키
)
```

### 4-2. 인코딩 vs 해싱

| 구분 | 방향 | 예시 | 비고 |
|---|---|---|---|
| 인코딩/디코딩 | 양방향 | Base64 | 누구나 디코딩 가능 |
| 해싱 | 단방향 | HMACSHA256, bcrypt | 역산 불가 |

Header와 Payload는 **인코딩**이므로 JWT를 가진 누구든 디코딩할 수 있다.
Signature는 **해싱**이므로 서명값을 알아도 비밀키를 역산할 수 없다.

### 4-3. 서버의 검증 방식

서버는 Signature를 역산하는 게 아니라, **비밀키로 서명을 직접 다시 만들어서 비교**한다.

```
[서버 검증 과정]
1. 클라이언트로부터 JWT 수신
2. Header + Payload를 꺼내서 서버의 비밀키로 서명 재생성
3. 재생성한 서명 vs JWT의 Signature 비교
4. 일치 → 검증 성공 / 불일치 → 변조된 토큰으로 판단, 요청 거부
```

### 4-4. Signature의 역할 — 변조 방지

Payload를 변조해서 다른 유저의 정보를 불러오는 공격이 불가능한 이유가 바로 Signature다.

```
[공격 시도]
1. JWT 디코딩 → Payload의 sub: 2 를 sub: 1 로 변조
2. 변조된 Payload로 새 JWT 생성 시도
3. 비밀키를 모르면 올바른 Signature를 만들 수 없음
4. 서버에서 Signature 검증 실패 → 요청 거부
```

**비밀키를 모르면 Signature를 만들 수 없으므로, Payload를 변조해도 서버에서 반드시 걸러진다.**

# 📃 NestJS Guard

## 1. Guard 개요

### 1-1. Guard란?
API 요청이 Controller / Service / Module까지 도달할 수 있는지 결정하는 NestJS의 보안 레이어.
Middleware와 달리 **ExecutionContext에 접근**할 수 있어 라우트 핸들러 정보를 알 수 있다.

### 1-2. 실행 순서
```
Request → Middleware → Guard → Interceptor → Pipe → Controller
```
Guard는 **Controller 실행 직전**에 동작한다.

---

## 2. Guard 구현

### 2-1. 기본 구조
`CanActivate` 인터페이스를 implement하고 `canActivate` 메서드를 오버라이딩한다.
`@Injectable` 데코레이터를 붙이면 constructor를 통해 의존성 주입이 가능하다.

```ts
@Injectable()
export class BearerTokenGuard implements CanActivate {
  constructor(
    private readonly authService: AuthService,
    private readonly usersService: UsersService,
  ) {}

  async canActivate(context: ExecutionContext): Promise<boolean> {
    // true: 요청 통과 / false: 요청 차단 (403 Forbidden)
    return true;
  }
}
```

### 2-2. canActivate 반환값

| 반환값 | 동작 |
|---|---|
| `true` | 요청을 계속 진행 |
| `false` | 요청 차단 (403 Forbidden) |
| `Promise<boolean>` | 비동기 처리 가능 |

---

## 3. ExecutionContext

### 3-1. context 스위처 종류
`canActivate`의 인자로 받는 `ExecutionContext`에서 요청 객체를 가져온다.

| 메서드 | 용도 |
|---|---|
| `context.switchToHttp()` | HTTP 요청 |
| `context.switchToRpc()` | RPC 요청 |
| `context.switchToWs()` | WebSocket 요청 |

### 3-2. Request 객체 가져오기
`getRequest()`의 반환 타입은 `any`이므로 **제네릭으로 타입을 지정**하는 것을 권장한다.
NestJS는 Express 기반이므로 Express의 `Request` 타입을 사용할 수 있다.

```ts
import { Request } from 'express';

const req = context.switchToHttp().getRequest<Request>();
```

### 3-3. 커스텀 Request 타입 확장
Guard에서 req 객체에 커스텀 속성을 추가할 경우 인터페이스를 확장한다.

```ts
interface BearerRequest extends Request {
  user: UsersModel | null;
  token: string;
  tokenType: 'access' | 'refresh';
}

const req = context.switchToHttp().getRequest<BearerRequest>();
```

---

## 4. Bearer Token Guard 구현 예시

### 4-1. 전체 흐름
Authorization 헤더에서 토큰을 추출 → 유효성 검사 → 유저 정보 추출 → req 객체에 추가 → `true` 반환.

```ts
async canActivate(context: ExecutionContext): Promise<boolean> {
  const req = context.switchToHttp().getRequest<BearerRequest>();

  // 1. Authorization 헤더에서 토큰 추출
  const rawToken = req.headers['authorization'];
  const token = this.authService.extractTokenFromHeader(rawToken, true);

  // 2. 토큰 유효성 검사
  const result = this.authService.verifyToken(token);

  // 3. 유저 정보 추출 후 req 객체에 추가
  const user = await this.usersService.getUserByEmail(result.email);
  req.user = user;
  req.token = token;
  req.tokenType = result.type;

  return true; // 요청 통과
}
```

### 4-2. req 객체 보존
`req` 객체를 직접 반환하지 않아도 **요청이 종료될 때까지 req 객체는 보존**된다.
이후 Controller, Service 등에서도 동일한 req 객체에 접근할 수 있다.

---

## 5. Guard 적용 방법

### 5-1. @UseGuards 데코레이터
Controller 클래스 또는 특정 Route 메서드에 `@UseGuards()` 데코레이터를 적용한다.

```ts
import { UseGuards } from '@nestjs/common';

// 특정 Route에만 적용
@Post('login/email')
@UseGuards(BasicTokenGuard)
loginEmail(@Headers('authorization') rawToken: string, @Request() req) {
  const token = this.authService.extractTokenFromHeader(rawToken, false);
  const credentials = this.authService.decodeBasicToken(token);
  return this.authService.loginWithEmail(credentials);
}

// Controller 전체에 적용
@Controller('auth')
@UseGuards(BearerTokenGuard)
export class AuthController { ... }
```

### 5-2. 전역 적용
`main.ts`에서 `useGlobalGuards`로 앱 전체에 적용할 수 있다.

```ts
// main.ts
app.useGlobalGuards(new BearerTokenGuard());
```

# 📃 NestJS Pipe

> 공식문서: https://docs.nestjs.com/pipes

## 1. 개요

### 1-1. 정의

`@Injectable()` 데코레이터로 선언된 클래스이며, `PipeTransform` 인터페이스를 구현한다.

```ts
import { PipeTransform, Injectable, ArgumentMetadata } from '@nestjs/common';

@Injectable()
export class CustomPipe implements PipeTransform {
  transform(value: any, metadata: ArgumentMetadata) {
    return value;
  }
}
```

### 1-2. 두 가지 핵심 역할

| 역할 | 설명 |
|---|---|
| **Transformation** | 입력값을 원하는 타입으로 변환 (ex. `string` → `number`) |
| **Validation** | 입력값이 유효하지 않으면 예외를 던져 요청을 중단 |

---

## 2. 기본 내장 Pipe

### 2-1. Parse***Pipe

URL 파라미터, 쿼리스트링 등 **string으로 들어오는 값을 원하는 타입으로 파싱**한다.

| Pipe | 변환 타입 |
|---|---|
| `ParseIntPipe` | `number` (정수) |
| `ParseFloatPipe` | `number` (실수) |
| `ParseBoolPipe` | `boolean` |
| `ParseArrayPipe` | `Array` |
| `ParseUUIDPipe` | UUID 문자열 (형식 검증) |
| `ParseEnumPipe` | Enum |
| `ParseFilePipe` | File |

### 2-2. ValidationPipe

DTO 클래스에 선언된 **class-validator 데코레이터 기반으로 요청 body를 검증**한다.

```ts
// main.ts - 전역 적용
app.useGlobalPipes(new ValidationPipe());
```

### 2-3. DefaultValuePipe

값이 `undefined`일 경우 **기본값을 설정**한다.

```ts
@Get()
getList(
  @Query('page', new DefaultValuePipe(1), ParseIntPipe) page: number,
) {
  return this.service.getList(page);
}
```

---

## 3. ParseIntPipe 예시

### 3-1. Pipe 미적용 (기존 방식)

URL 파라미터는 항상 `string`으로 추출되므로, 수동으로 `Number()`로 변환해야 한다.

```ts
@Get(':id')
getPost(@Param('id') id: string) {
  return this.postsService.getPostById(Number(id)); // 수동 변환
}
```

- **문제**: 유효성 검증이 없어 `id`가 숫자가 아닐 경우 `NaN`이 전달됨
- **에러 응답**: 별도 처리 없으면 500 Internal Server Error

```json
{
    "statusCode": 500,
    "message": "Internal server error"
}
```

### 3-2. Pipe 적용 (ParseIntPipe)

`@Param()` 두 번째 인자에 Pipe를 넘기면 **자동 변환 + 자동 검증**이 적용된다.

```ts
@Get(':id')
getPost(@Param('id', ParseIntPipe) id: number) { // string → number 자동 변환
  return this.postsService.getPostById(id);
}
```

- **Transformation**: `id`를 `number` 타입으로 자동 변환
- **Validation**: `id`가 숫자 문자열이 아닐 경우 400 Bad Request 자동 응답

```json
{
    "message": "Validation failed (numeric string is expected)",
    "error": "Bad Request",
    "statusCode": 400
}
```

> Pipe 하나로 타입 변환과 유효성 검증을 동시에 처리할 수 있다.

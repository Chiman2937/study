# Storybook vite-plugin-storybook-nextjs 버그 수정 과정

이 글은 next-font-local을 storybook에 적용할 때 발생한 버그와 해결 과정을 정리합니다.

이 전에 storybook/nextjs 프레임워크 환경에서 next-font-local 을 사용할 때 정상적으로 적용되지 않는 버그가 있어 해당 내용을 수정하고 storybook에 기여할 수 있었다.

그 후 nextjs-vite 플러그인에서도 동일한 코드를 사용하고 있어서 수정 제안을 받았고, 수정 작업에 들어갔다.

먼저 storybook에서 사용 가능한 nextjs 프레임워크는 nextjs(webpack)과 nextjs-vite 2가지가 있다.

둘 다 nextjs 프레임워크를 흉내낸 느낌으로 생각하면 된다.

storybook/nextjs-vite는 storybook/nextjs 와 다르게 font 관리 시스템이 vite-plugin-storybook-nextjs 플러그인으로 분리되어있었다.

https://github.com/storybookjs/vite-plugin-storybook-nextjs

# 작업 과정

### Storybook 저장소 Fork 후 clone

```
https://github.com/Chiman2937/vite-plugin-storybook-nextjs
```

### 의존성 설치 및 빌드

storybook은 yarn을 사용하지만 vite-plugin-storybook-nextjs 플러그인은 pnpm을 사용한다.

```
pnpm install
```

### watch 모드로 빌드
```
pnpm dev(pnpm build --watch)
```

### 패키지 전역 링크 생성
```
pnpm link --global
```

### 메인 프로젝트로 돌아가서 생성된 링크에 연결
```
pnpm link C:\git\contribute-vite-plugin-storybook-nextjs\vite-plugin-storybook-nextjs
```

## 버그1. font 네트워크 요청 버그
next local font 사용시 font 파일을 불러오지 못하는 문제가 있었다.

<img width="1454" height="359" alt="image" src="https://github.com/user-attachments/assets/64abf197-2a65-4718-b906-f52932ce6f22" />

이 중 가장 눈에 띄는 것은 `.../@fsC:/..` 로 되어있는 경로였는데, 뭔지 몰라서 찾아봤다.

### vite 의 @fs

Vite에서 URL에 나타나는 @fs는 **파일 시스템(File System)**의 약자로, Vite 개발 서버가 정적 파일을 제공하는 방식 중 하나를 나타내는 특수한 접두사이다.

@fs/는 파일 시스템의 루트 디렉터리에서 시작하는 절대 경로에 매핑됩니다. 이는 Windows 환경 등에서 발생할 수 있는 드라이브 문자(예: C:/) 관련 문제를 해결하고 일관된 URL 형식을 유지하기 위한 Vite 내부 메커니즘입니다.

## 문제 내용

그렇다면 @fsC는 @fs(파일 시스템 디렉토리 루트 경로) + C드라이브 라는 뜻이되고, / 로 구분이 안된 상태라는 뜻이다.

소스코드에서 관련 코드를 찾아봤다.

```ts
return dedent`@font-face {
  font-family: ${id};
  src: url(${font.path.fontReferenceId ? getPlaceholderFontUrl(font.path.fontReferenceId) : `/@fs${font.path.fontPath}`});
  ${font.weight ? `font-weight: ${font.weight};` : ""}
  ${font.style ? `font-style: ${font.style};` : ""}
  ${fontDeclarations}
}`;
```

이 코드 중 `/@fs${localFontSrc.fontPath}` 에서 @fs 뒤에 /가 붙어 있지 않다는 것을 알았다.

그래서 /를 추가하고 메인프로젝트에서 storybook을 다시 빌드하면

<img width="1454" height="359" alt="image" src="https://github.com/user-attachments/assets/a37072da-f398-49c7-9ae7-b10bd83a7315" />

이와 같이 정상적으로 font가 로딩되는 것을 볼 수 있다.

## 버그2. font 로딩 버그

next local font를 사용할 때 크게 2가지 방식이 지원된다.

```ts
export const pretendard = localFont({
  src: [{ path: '../assets/fonts/PretendardVariable.woff2', weight: '45 920' }],
  variable: '--font-pretendard',
  display: 'swap',
});
```

```ts
export const pretendard = localFont({
  src: '../assets/fonts/PretendardVariable.woff2', //variable font
  variable: '--font-primary',
  display: 'swap',
  weight: '45 920', //variable weight
});
```

첫번째는 src에 배열 형태로 path와 weight등의 속성을 모두 지정해주는 것이고, 두번째는 localFont 객체에 직접 weight등의 속성을 지정하는 것이다.

두번째 방식으로 했을 때 기존에 storybook/nextjs 프레임워크에서 정상적으로 폰트 클래스가 생성되지 않는 버그가 있었던 내용을 그대로 반영해봤는데, 여기서는 아예 font 파일 자체가 네트워크 요청이 일어나고 있지 않았다.

<img width="1574" height="244" alt="image" src="https://github.com/user-attachments/assets/c2352b22-ef95-4065-bbaa-ce12675f447d" />

> 기본 font만 요청되고 있음

style이 어떻게 생성되나 확인해보니 아래와 같이 css의 src 속성에 ; 가 빠져있는 것을 볼 수 있었다.

```css
  @font-face {
    font-family: font-5c0ab9;
    // ❌ missing ; after src value
    src: url(/@fs/C:/git/WeGo/WeGo/src/assets/fonts/PretendardVariable.woff2)
    font-weight: 45 920;
  }
```

이것이 아예 잘못된 css 파일로 인식돼서 font 파일 자체가 요청되고 있지 않는 것이었다.

소스코드에서 바로 이 문제의 원인을 찾을 수 있었다.

```ts
return dedent`@font-face {
  font-family: ${id};
  src: url(${localFontSrc.fontReferenceId ? getPlaceholderFontUrl(localFontSrc.fontReferenceId) : `/@fs/${localFontSrc.fontPath}`})
  ${weight ? `font-weight: ${weight};` : ""}
  ${style ? `font-style: ${style};` : ""}
  ${fontDeclarations}
}`;
```


1. **Vite의 @fs 동작 원리**
   - `/@fs/` = 절대 경로 파일 서빙을 위한 특수 prefix
   - `server.fs.allow` 설정으로 접근 제어

2. **패키지 간 차이점**
   - `@storybook/nextjs`: Webpack 기반, 메인 저장소
   - `vite-plugin-storybook-nextjs`: Vite 기반, 독립 저장소
   - 각각 별도로 watch/link 필요

3. **디버깅 과정**
   - Network 탭 확인 → 404 에러 발견
   - 생성된 CSS 확인 → 문법 오류 발견
   - 소스 코드 추적 → 근본 원인 파악

## 참고 링크
- PR: [링크 추가 예정]
- Related: https://github.com/storybookjs/storybook/pull/32998
src 속성 맨 뒤에 ; 이 실제로 빠져있었다.
이것을 수정하고 다시 스토리북을 빌드한 후 font파일이 정상적으로 불러와지는걸 확인할 수 있었다.

<img width="1574" height="394" alt="image" src="https://github.com/user-attachments/assets/12e34809-8f30-4ec2-9c01-a29a89ef088f" />

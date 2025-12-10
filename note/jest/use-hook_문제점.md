WeGo 프로젝트 프로필 페이지의 테스트 코드 작성 중 발생한 문제 기록

현재 프로필 페이지는 React 19의 use hook을 사용하여 동적 파라미터를 받아오고 있다.

```tsx
interface Props {
  params: Promise<{ userId: string }>;
}

const ProfilePage = ({ params }: Props) => {
  const { userId: id } = use(params);
  const userId = Number(id);

  const { data: user } = useGetUser({ userId });

  if (!user) return null;

  return <ProfileInfo user={user} />;
};

export default ProfilePage;
```

해당 페이지의 테스트를 진행하기 위해 아래와 같이 코드를 작성하였다.
```tsx
import { use } from 'react';

import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { render, screen, waitFor } from '@testing-library/react';

import { server } from '@/mock/server';

import ProfilePage from './page';

const createTestQueryClient = () =>
  new QueryClient({
    defaultOptions: {
      queries: {
        retry: false, // 테스트에서 재시도 비활성화
        gcTime: Infinity, // Jest 환경에서 카비지 컬렉션을 위한 타이머 생성 방지
      },
    },
  });

const renderWithQueryClient = (component: React.ReactElement) => {
  // 각 테스트마다 새로운 QueryClient 생성하여 독립적인 상태 유지 (useState 없이)
  const testQueryClient = createTestQueryClient();
  return render(<QueryClientProvider client={testQueryClient}>{component}</QueryClientProvider>);
};

describe('메인 페이지 테스트', () => {
  beforeAll(() => server.listen());
  afterEach(() => {
    server.resetHandlers();
  });
  afterAll(() => server.close());

  test('profile 정보가 올바르게 표시되는지 테스트', async () => {
    renderWithQueryClient(<ProfilePage params={Promise.resolve({ userId: '2' })} />);

    // 비동기 데이터 로딩 대기
    await waitFor(() => {
      expect(screen.getByRole('heading', { level: 1 })).toHaveTextContent('크리스티아누 호날두');
      expect(screen.getByTestId('profileMessage')).toHaveTextContent('안녕하세요');
    });
  });
});

```

await waitFor를 통해 use hook과 useGetUser까지 await하면 데이터를 받아올 수 있을 것이라고 생각했고, test를 실행해보았다.
하지만 예기치 못한 에러가 발생했다.
```
  console.error
    A component suspended inside an `act` scope, but the `act` call was not awaited. When testing React components that depend on asynchronous data, you must await the result:

    await act(() => ...)
```
result를 await를 해야하니 act를 사용해라... 라길래 아래처럼 구문을 변경해봤다.

```tsx
const renderWithQueryClient = async (component: React.ReactElement) => {
  const testQueryClient = createTestQueryClient();
  let renderResult;
  await act(async () => {
    renderResult = render(
      <QueryClientProvider client={testQueryClient}>{component}</QueryClientProvider>,
    );
  });

  return renderResult;
};
```

테스트가 성공했지만, console error가 발생했다.
```
 PASS  src/app/(user)/profile/[userId]/page.test.tsx
  메인 페이지 테스트
    √ profile 정보가 올바르게 표시되는지 테스트 (196 ms)
```
```
console.error
    The current testing environment is not configured to support act(...)

      at isConcurrentActEnvironment (node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:16329:17)
      at pingSuspendedRoot (node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:18598:7)

  console.log
    MSW intercepted: GET http://localhost/users/2

      at _Emitter.log (src/mock/server.ts:8:11)
          at Array.forEach (<anonymous>)

  console.error                                                                                                                                                                                        
    An update to ProfilePage inside a test was not wrapped in act(...).

    When testing, code that causes React state updates should be wrapped into act(...):

    act(() => {
      /* fire events that update state */
    });
    /* assert on the output */

    This ensures that you're testing the behavior the user would see in the browser. Learn more at https://react.dev/link/wrap-tests-with-act

      at node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:18758:19
      at runWithFiberInDEV (node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:874:13)
      at warnIfUpdatesNotWrappedWithActDEV (node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:18757:9)
      at scheduleUpdateOnFiber (node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:16409:11)
      at forceStoreRerender (node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:8262:24)
      at node_modules/.pnpm/react-dom@19.2.1_react@19.2.1/node_modules/react-dom/cjs/react-dom-client.development.js:8247:11
      at node_modules/.pnpm/@tanstack+query-core@5.90.3/node_modules/@tanstack/query-core/src/notifyManager.ts:73:11
      at notifyFn (node_modules/.pnpm/@tanstack+query-core@5.90.3/node_modules/@tanstack/query-core/src/notifyManager.ts:21:5)
      at node_modules/.pnpm/@tanstack+query-core@5.90.3/node_modules/@tanstack/query-core/src/notifyManager.ts:44:13
          at Array.forEach (<anonymous>)
      at node_modules/.pnpm/@tanstack+query-core@5.90.3/node_modules/@tanstack/query-core/src/notifyManager.ts:43:25
      at batchNotifyFn (node_modules/.pnpm/@tanstack+query-core@5.90.3/node_modules/@tanstack/query-core/src/notifyManager.ts:24:5)
      at node_modules/.pnpm/@tanstack+query-core@5.90.3/node_modules/@tanstack/query-core/src/notifyManager.ts:42:9
      at Timeout.task [as _onTimeout] (node_modules/.pnpm/jsdom@26.1.0/node_modules/jsdom/lib/jsdom/browser/Window.js:579:19)
```

정확한 원인은 모르겠지만 testing library가 react19를 온전히 지원하지 않아보이고(use hook), tanstack query와 충돌하는 문제도 있어보였다.

하지만 테스트에는 이상이 없기 때문에 코드 자체에는 문제가 없다.

그래서 2가지 방법으로 수정해보았다.

나는 mocking을 최소화 하고 싶었기 때문에 1번 방법을 선택했는데, 사실 뭐가 더 나은 방법인지는 잘 모르겠다.

1. console error를 띄우지 않도록 수정
```ts
  const originalError = console.error;
  beforeAll(() => {
    console.error = (...args) => {
      if (
        typeof args[0] === 'string' &&
        (args[0].includes('not wrapped in act') ||
          args[0].includes('not configured to support act'))
      ) {
        return;
      }
      originalError.call(console, ...args);
    };
    server.listen();
  });
```

2. use hook 모킹

```tsx
import { use } from 'react';

import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { render, screen, waitFor } from '@testing-library/react';

import { server } from '@/mock/server';

import ProfilePage from './page';

const createTestQueryClient = () =>
  new QueryClient({
    defaultOptions: {
      queries: {
        retry: false, // 테스트에서 재시도 비활성화
        gcTime: Infinity, // Jest 환경에서 카비지 컬렉션을 위한 타이머 생성 방지
      },
    },
  });

const renderWithQueryClient = (component: React.ReactElement) => {
  // 각 테스트마다 새로운 QueryClient 생성하여 독립적인 상태 유지 (useState 없이)
  const testQueryClient = createTestQueryClient();
  return render(<QueryClientProvider client={testQueryClient}>{component}</QueryClientProvider>);
};

jest.mock('react', () => ({
  ...jest.requireActual('react'),
  use: jest.fn(),
}));

const mockUse = use as jest.Mock;

describe('메인 페이지 테스트', () => {
  beforeAll(() => server.listen());
  afterEach(() => {
    server.resetHandlers();
    mockUse.mockClear();
  });
  afterAll(() => server.close());

  test('profile 정보가 올바르게 표시되는지 테스트', async () => {
    mockUse.mockReturnValue({ userId: '2' });
    renderWithQueryClient(<ProfilePage params={Promise.resolve({ userId: '2' })} />);

    await waitFor(() => {
      expect(screen.getByRole('heading', { level: 1 })).toHaveTextContent('크리스티아누 호날두');
    });
    expect(screen.getByTestId('profileMessage')).toHaveTextContent('안녕하세요');
  });
});

```

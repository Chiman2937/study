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
result를 await를 해야하니 act를 사용해라... 라길래 아래처럼 구문을 변경해서 해결할 수 있었다.

```tsx
const renderWithQueryClient = async (component: React.ReactElement) => {
  // 각 테스트마다 새로운 QueryClient 생성하여 독립적인 상태 유지 (useState 없이)
  const testQueryClient = createTestQueryClient();
  let renderResult;
  await act(async () => {
    renderResult = render(
      <QueryClientProvider client={testQueryClient}>{component}</QueryClientProvider>,
    );
  });

  return renderResult;
};

describe('프로필 페이지 테스트', () => {
  beforeAll(() => {
    server.listen();
  });
  afterEach(() => {
    server.resetHandlers();
  });
  afterAll(() => server.close());

  test('프로필 정보가 올바르게 표시되는지 테스트', async () => {
    await renderWithQueryClient(<ProfilePage params={Promise.resolve({ userId: '2' })} />);

    // 비동기 데이터 로딩 대기
    await waitFor(() => {
      expect(screen.getByRole('heading', { level: 1 })).toHaveTextContent('크리스티아누 호날두');
    });
    expect(screen.getByTestId('profileMessage')).toHaveTextContent('안녕하세요');
  });
});

```

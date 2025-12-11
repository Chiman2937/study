playwright 를 이용한 E2E 테스트 진행 중 발생한 문제점에 대한 기록

WeGo 프로젝트를 진행하면서 프로필 페이지에 대한 E2E 테스트를 진행했는데, 원래 정상적으로 실행이 됐었다.

<img width="882" height="216" alt="image" src="https://github.com/user-attachments/assets/31063663-54ab-4ef2-b290-c8ed9a38ae56" />

하지만 이후 다시 실행했을 때 코드는 바뀐것이 없는데 갑자기 에러가 발생했다.

<img width="921" height="181" alt="image" src="https://github.com/user-attachments/assets/2590a921-d43e-4afd-b0fa-8e30d43655fd" />
<img width="916" height="135" alt="image" src="https://github.com/user-attachments/assets/71aeddd7-6fcc-4b1c-a2f3-6f36112b26ea" />

.next 캐시 삭제, node_modules 재설치, MSW 설정 환경변수 이름 교체 등등 할수있는 건 다 해봤는데 해결을 못하다가, 혹시 기존 3000포트가 이미 열려있어서 그런건 아닌가 싶어서 현재 활성화되어있는 포트를 확인해보았다.

```
PS C:\git\WeGo\WeGo> netstat -ano | findstr :3000
  TCP    127.0.0.1:3000         0.0.0.0:0              LISTENING       26096
  TCP    127.0.0.1:3000         127.0.0.1:49707        ESTABLISHED     26096
  TCP    127.0.0.1:3000         127.0.0.1:52302        FIN_WAIT_2      26096
  TCP    127.0.0.1:49707        127.0.0.1:3000         ESTABLISHED     26172
  TCP    127.0.0.1:52302        127.0.0.1:3000         CLOSE_WAIT      26172
```

열려있는 3000포트를 전부 삭제 후 다시 테스트 해봤더니 정상동작했다..

```
PS C:\git\WeGo\WeGo> taskkill /F /PID 26096 /PID 26172
성공: 프로세스(PID 26172)가 종료되었습니다.
성공: 프로세스(PID 26096)가 종료되었습니다.
```

<img width="890" height="217" alt="image" src="https://github.com/user-attachments/assets/0db2c13d-ecc5-4bba-aa56-6fbf070aba3f" />


이 문제가 발생한 이유는 현재 playwright.config.ts 에 reuseExistingServer가 !process.env.CI로 설정되어있는데,
이것은 프로세스가 실행중이면 다시 시작하지 않고 열려있는 프로세스를 그대로 사용한다고 한다.

이미 열려있었던 3000포트에 메인 프로젝트가 아니라 프로젝트 / 폴더 가 띄워져있었기 때문에 환경변수가 없고 주소도 일치하지 않아 에러가 발생한 것으로 보인다.
```TS
  /* Run your local dev server before starting the tests */
  webServer: {
    command: 'pnpm dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
```

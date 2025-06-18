# 📝 Cloudinary API 사용하기
Cloudinary는 이미지와 동영상을 업로드, 저장, 최적화, 전송하는 작업을 자동화해주는 강력한 미디어 관리 플랫폼이다.

이번 기초 프로젝트에서는 사용자가 직접 배경화면을 선택할 수 있는 기능을 구현하기 위해 Cloudinary를 도입했다.

Cloudinary는 이미지 업로드를 포함한 다양한 API를 제공해주기 때문에, 복잡한 서버 설정 없이도 이미지 리소스를 손쉽게 업로드하고 관리할 수 있다.

덕분에 프론트엔드에서도 빠르게 미디어 기능을 구현할 수 있었고, 사용자 경험을 향상시키는 데 도움이 되었다.

## 💡 Cloudinary Cloud name 확인하기
나중에 이미지 업로드 Api를 사용할때 Cloud name을 지정해야하기 때문에, 이름을 확인해놓아야 한다.

나 같은 경우는 dxho7f5dm이 cloud name이다.

![image](https://github.com/user-attachments/assets/53d4c8ef-d575-46ef-9919-6d0a92721ddf)


## 💡 Cloudinary Upload Preset 만들기
먼저 Cloudinary에서 이미지 업로드 Api를 사용하려면 Upload Preset을 만들어줘야한다.

Setting(톱니바퀴 아이콘) => Upload => Add Upload Preset 선택

![image](https://github.com/user-attachments/assets/86736da2-e916-46c2-87e2-45a86be44418)

아래 사진과 같이 Preset 정보를 입력하고 저장하면 된다.

나는 이 기능을 백엔드를 거치지 않고 프론트에서 직접 구현했고, 보안보다 기능 구현이 우선이었기 때문에 Unsigned를 선택했다.

![image](https://github.com/user-attachments/assets/d781264e-88d9-43d9-a6fc-ceb8e25a7d2a)

## 💡 upload_preset 환경 변수로 관리하기
여기서 cloud에 직접 접근할 수 있는 값이 upload_preset이므로 환경변수로 관리하는 것이 보안상 유리하다.

로컬환경에서도 접근할 수 있도록 .env.local에 변수값을 관리하고,
사이트 배포는 vercel로 진행했기 때문에 vercel에도 환경변수를 생성해주면 된다.

### ✅ 로컬 환경에서 환경변수 설정하기
Local 환경에서 환경변수 지정하기 프로젝트의 최상위 폴더에 .env.local 파일을 생성하고, 환경변수로 사용할 변수 이름과 값을 지정해준다.

참고로, React + vite 빌드환경에서는 Vercel 배포할때 환경 변수 이름 앞에 VITE를 붙여주어야 오류가 나지 않는다고 한다.

.env.local
```
VITE_CLOUDINARY_UPLOAD_PRESET=...
```

### ✅ Vercel 환경에서 환경변수 설정하기
Vercel => 프로젝트 선택 => Settings => Environment Variables

환경변수 Key 이름과 Value를 작성해준 수 Save 버튼을 눌러 저장해주면 된다.

여기서 Environments는 `All Environments` 가 맞는지 확인해는 게 좋다.

`Products`로 설정하게 되면 Vercel의 Preview Link에서 작동하지 않는다.

![image](https://github.com/user-attachments/assets/a5d394f0-cebf-4629-8804-adc4199537e2)

## 💡 Cloudinary 이미지 업로드 API 사용하기

[[🔗 Cloudinary Api 공식 문서]](https://cloudinary.com/documentation/image_upload_api_reference)

### ✅ 기본 사용 방법

기본적으로 이미지를 업로드할 때는 아래와 같이 POST 요청을 보내면 된다고 설명되어있다.

![image](https://github.com/user-attachments/assets/d4ccb607-1bcf-49fe-97e9-366c7edb3828)

그러니까 이런식이겠다.
```js
const res = await fetch(`https://api.cloudinary.com/v1_1/${cloudname}/image/upload`);
```

### ✅ 파라미터 설정
공식문서에는 아래와 같이 필수 입력값에 대해 설명하고 있다.

![image](https://github.com/user-attachments/assets/7a9fec7a-4a8d-45a3-abe8-ed3b54c42e3e)

Unsigned는 upload_preset이 필수이고, Signed는 signature가 필수이다.

> signature: Cloudinary에서 민감한 설정값(public_id, folder, tags 등) 을 포함한 업로드 요청이 조작되지 않았음을 증명하기 위한 HMAC-SHA1 해시 서명

그러면 Body를 아래와 같이 넘겨주면 될 것 같다.

```js
const formData = {
  file: fileObject,
  upload_preset: upload_preset
}
```

### ✅ 코드 작성

```js
export const uploadImageToCloudinary = async (imageFileObject) => {
  const presetName = import.meta.env.VITE_CLOUDINARY_UPLOAD_PRESET;
  const timestamp = Date.now();
  const baseName = imageFileObject.name.replace(/\.[^/.]+$/, ''); // 확장자 제거
  const formData = new FormData();
  formData.append('file', imageFileObject);
  formData.append('upload_preset', presetName);
  formData.append('public_id', `images/${baseName}_${timestamp}`);

  try {
    const res = await fetch(`https://api.cloudinary.com/v1_1/dxho7f5dm/image/upload`, {
      method: 'POST',
      body: formData,
    });
    if (!res.ok) {
      const errorData = await res.json();
      throw new Error(errorData.error?.message || 'Cloudinary upload failed');
    }
    const data = await res.json();
    return data.url;
  } catch (e) {
    console.error(e);
  }
};

```

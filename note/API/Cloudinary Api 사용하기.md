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

![image](https://github.com/user-attachments/assets/d781264e-88d9-43d9-a6fc-ceb8e25a7d2a)


## 💡 Cloudinary 이미지 업로드 API 사용하기



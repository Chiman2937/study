이 글은 AWS 구축 과정을 정리합니다.

## 1. AWS EC2 생성

Next.js 프로젝트 배포를 위해 EC2에서 인스턴스를 생성합니다.

이름은 WeGo_Web으로 할 예정

<img width="1092" height="217" alt="image" src="https://github.com/user-attachments/assets/7a1f2a9a-4719-4c98-9208-f7d65e691c3a" />

애플리케이션은 통상적으로 사용하는 Ubuntu 사용
<img width="1090" height="703" alt="image" src="https://github.com/user-attachments/assets/241302cb-8018-4584-8f93-1e11306f3603" />

인스턴스는 t2.micro랑 t3.micro 가격 차이가 거의 없어서 성능이 더 좋은 t3 사용하기로 선택
<img width="1088" height="216" alt="image" src="https://github.com/user-attachments/assets/fc14fcfc-99a1-46b0-88bb-e6fa64d73e62" />

아래는 t2.micro 요금
<img width="747" height="85" alt="image" src="https://github.com/user-attachments/assets/d1729dbd-9373-42bd-9abc-e6855e25ffa4" />

요금 비교

| 항목 | t2.micro | t3.micro | 비고 |
|------|----------|----------|------|
| **vCPU** | 1 | 2 | t3가 2배 |
| **메모리** | 1 GiB | 1 GiB | 동일 |
| **네트워크 성능** | Low to Moderate | Up to 5 Gigabit | t3가 훨씬 빠름 |
| **CPU 크레딧/시간** | 6 | 12 | t3가 2배 |
| **베이스라인 성능** | 10% | 10% | 동일 |
| **가상화 기술** | Xen | Nitro System | t3가 최신 |
| **프로세서** | Intel Xeon (다양) | Intel Xeon Platinum 8000 | t3가 최신 |
| **버스트 모드** | Standard/Unlimited | Unlimited (기본) | t3가 편리 |
| **EBS 최적화** | 추가 비용 | 기본 제공 | t3가 유리 |
| **가격 (Ubuntu Pro)** | $0.0162/시간 | $0.0165/시간 | 거의 동일 |
| **월 예상 비용** | ~$11.9 | ~$12.1 | $0.2 차이 |
| **출시 시기** | 2014년 | 2018년 | t3가 4년 최신 |

키페어는 ssh 접속을 위해 반드시 필요하므로 발급 후 선택
<img width="1084" height="193" alt="image" src="https://github.com/user-attachments/assets/d0c7045f-14a6-4617-b8ec-5f4f6b0d8dde" />

보안그룹 설정
1. ssh 설정
2. http 설정
3. https 설정
4. next.js 기본포트 3000번 설정

<img width="1057" height="464" alt="image" src="https://github.com/user-attachments/assets/015e612a-1072-44c8-a2d1-bc9d060c13da" />
<img width="1049" height="475" alt="image" src="https://github.com/user-attachments/assets/76f8ab23-aa8e-4252-9bc4-b529ffbf5017" />

스토리지는 더 필요하면 추후 증가(기본값 사용)

<img width="1087" height="356" alt="image" src="https://github.com/user-attachments/assets/3b4a5a89-46b4-4464-8c02-45ffd28639ee" />

이후 탄력적 IP(Elastic IP) 연결

- 인스턴스가 종료되거나 중지되면 IP 바뀌어서 고정해야함

이후 Secret 추가
- AWS_EC2_HOST: 퍼블릭 IP
- AWS_EC2_USERNAME: ubuntu (EC2 / 인스턴스 / 각 EC2 ID / 인스턴스에 연결 / EC2 인스턴스 연결 - 사용자 이름 (ubuntu)
- AWS_EC2_PEM_KEY: 발급받은 PEM 파일 내용


=====
https 연결

구매한 route 53 도메인에 접속해서 A 유형으로 레코드를 추가한다.

ex)
new-project-final.link / EC2 퍼블릭 IPv4


ec2 인스턴스에 접속


1. Certbot 설치
```
sudo apt update
sudo apt install certbot python3-certbot-dns-route53 -y
```

2. Nginx 정상 동작 확인
```
sudo systemctl status nginx --no-pager -l
# 문법 체크
sudo nginx -t
# 문제 있으면 수정 후 리로드
sudo systemctl reload nginx
```

3. Let's Encrypt 인증서 발급

이 방법은 AWS 콘솔에서 수동으로 ROUTE 53 레코드를 추가하는 방식이다.

자동으로 할수도 있긴 한데 cerbot 설정이 귀찮아서 이렇게 했다.

```
sudo certbot certonly --manual --preferred-challenges dns \
  -d new-project-final.link \
  -d '*.preview.new-project-final.link'
```

AWS Route 53에서 직접 레코드 추가
```
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name:

_acme-challenge.preview.new-project-final.link.

with the following value:

hMuRNgubrDo0k14BpawfFciwTdbnL6h_UxeYExYsd7I

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```
```
Please deploy a DNS TXT record under the name:

_acme-challenge.new-project-final.link.

with the following value:

uSKxO87vYNA_dsfeQnV7SoTCKXr_g2ALHee3te4QEKI

(This must be set up in addition to the previous challenges; do not remove,
replace, or undo the previous challenge tasks yet. Note that you might be
asked to create multiple distinct TXT records with the same name. This is
permitted by DNS standards.)

Before continuing, verify the TXT record has been deployed. Depending on the DNS
provider, this may take some time, from a few seconds to multiple minutes. You can
check if it has finished deploying with aid of online tools, such as the Google
Admin Toolbox: https://toolbox.googleapps.com/apps/dig/#TXT/_acme-challenge.new-project-final.link.
Look for one or more bolded line(s) below the line ';ANSWER'. It should show the
value(s) you've just added.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```

발급 성공

어디 저장됐는지 알려준다.
```
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/new-project-final.link/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/new-project-final.link/privkey.pem
This certificate expires on 2026-02-21.
These files will be updated when the certificate renews.
```

EC2 관리 폴더 구조
```

사용자 파일
/home/ubuntu/
├── wego-production/              # main 프로젝트
│   ├── current/                  # 현재 실행 중
│   │   ├── .next/
│   │   ├── node_modules/
│   │   ├── package.json
│   │   └── .env
│   ├── build/                    # 새 빌드 임시 저장
│   │   └── nextjs-app.tar.gz
│   └── backup/                   # 이전 버전 백업
│
├── wego-preview-feature-1/       # preview 브랜치들
│   ├── current/
│   └── build/
├── wego-preview-feature-2/
│   ├── current/
│   └── build/
│
├── scripts/                      # 실행 스크립트
│   ├── setup-npmrc.sh
│   ├── deploy.sh
│   └── cleanup.sh
│
├── templates/                      # 템플릿 파일
│   └── preview.nginx.tpl
│
├── .pnpm-store/                  # 공유 pnpm store
│   └── v3/
│
├── .npmrc                        # pnpm 글로벌 설정

시스템
/etc/nginx/
├── nginx.conf
├── sites-available/
├── sites-enabled/
└── conf.d/
	├── main.conf                    # wego.link
	├── preview-feature-login.conf   # feature-login.preview.wego.link
	└── preview-feature-signup.conf  # feature-signup.preview.wego.link
```

명령어 정리
```
# ============================================
# 디스크 용량 확인
# ============================================

# EC2 인스턴스 전체 용량 확인
df -h

# 특정 디렉토리 용량 확인
du -sh /home/ubuntu

# 현재 디렉토리 기준 하위 폴더별 용량
du -h --max-depth=1

# 용량 큰 순서대로 정렬
du -h --max-depth=1 | sort -hr

# 특정 폴더 용량 확인
du -sh node_modules

# 현재 디렉토리 전체 용량
du -sh


# ============================================
# 메모리 확인
# ============================================

# 메모리 사용량 확인 (사람이 읽기 쉽게)
free -h

# 실시간 메모리/CPU 사용량 (종료: q)
htop
# 또는
top


# ============================================
# PM2 관련
# ============================================

# PM2 프로세스 목록
pm2 list

# 특정 앱 로그 확인
pm2 logs wego-production

# 최근 N줄만 보기
pm2 logs wego-production --lines 50

# 로그 초기화
pm2 flush

# PM2 프로세스 상태 (CPU, 메모리)
pm2 monit

# 특정 앱 재시작
pm2 reload wego-production

# 특정 앱 중지
pm2 stop wego-production

# 특정 앱 삭제
pm2 delete wego-production


# ============================================
# Nginx 관련
# ============================================

# Nginx 설정 테스트
sudo nginx -t

# Nginx 재시작
sudo systemctl reload nginx

# Nginx 상태 확인
sudo systemctl status nginx

# Nginx 설정 파일 목록
ls -la /etc/nginx/conf.d/

# 특정 설정 파일 내용 확인
cat /etc/nginx/conf.d/main.conf

# Nginx 에러 로그 확인
sudo tail -f /var/log/nginx/error.log

# Nginx 액세스 로그 확인
sudo tail -f /var/log/nginx/access.log


# ============================================
# 파일/디렉토리 관리
# ============================================

# 현재 디렉토리의 파일/폴더 목록 (상세)
ls -lah

# 파일 내용 보기 (전체)
cat file.txt

# 파일 내용 보기 (처음 20줄)
head -20 file.txt

# 파일 내용 보기 (마지막 20줄)
tail -20 file.txt

# 실시간 로그 확인 (파일이 업데이트되면 자동 표시)
tail -f /var/log/app.log

# 파일/폴더 삭제 (강제)
rm -rf folder_name

# 파일/폴더 복사
cp -r source/ destination/

# 파일/폴더 이동
mv source/ destination/


# ============================================
# 네트워크/포트 확인
# ============================================

# 특정 포트 사용 확인
sudo lsof -i :3000

# 모든 리스닝 포트 확인
sudo netstat -tlnp

# 또는
sudo ss -tlnp

# 외부 접속 테스트 (로컬에서)
curl http://localhost:3000

# HTTP 헤더 확인
curl -I http://localhost:3000


# ============================================
# 프로세스 관리
# ============================================

# 특정 프로세스 찾기
ps aux | grep node

# 프로세스 강제 종료
kill -9 <PID>

# 포트로 프로세스 찾아서 종료
sudo lsof -ti:3000 | xargs kill -9


# ============================================
# 시스템 정보
# ============================================

# Ubuntu 버전 확인
lsb_release -a

# Node.js 버전
node -v

# pnpm 버전
pnpm -v

# 현재 사용자
whoami

# 시스템 가동 시간
uptime


# ============================================
# Git (배포 디버깅용)
# ============================================

# 현재 브랜치 확인
git branch

# 마지막 커밋 확인
git log -1

# 변경된 파일 확인
git status


# ============================================
# 유용한 단축키
# ============================================

# Ctrl + C: 실행 중인 명령어 중단
# Ctrl + Z: 실행 중인 명령어 일시 중지
# Ctrl + L: 터미널 화면 클리어 (또는 clear 명령어)
# Ctrl + R: 명령어 히스토리 검색
# ↑/↓ 화살표: 이전 명령어 탐색
```

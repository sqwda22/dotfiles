1. 관리 문서 목록 및 내용

- 시스템 이미지
- 비밀번호 관리자 데이터 (Bitwarden/1Password 등)
- 클라우드 동기화 폴더 (Google Drive/iCloud)
- .devcontainer 공용 템플릿

2. chezmoi

- 설치 스크립트 (.chezmoi.ps1.tmpl): winget과 scoop을 이용한 앱 설치 목록.
- 쉘 프로필 (Microsoft.PowerShell_profile.ps1): 터미널 에일리어스 및 환경 변수.
- 인프라 구성 (docker-compose.yml): Portainer, Caddy 서버 설정.
- 시스템 설정 (.wslconfig, .gitconfig): WSL 자원 할당 및 Git 사용자 정보.

3. infra (Portainer & Caddy) 구성 : 

### docker-compose.yml

services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    ports:
      - "9443:9443"
      - "9000:9000" # Caddy 연결용 HTTP 포트 개방

  caddy:
    image: caddy:latest
    container_name: caddy
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile # 로컬 Caddyfile 마운트
      - caddy_data:/data
      - caddy_config:/config

volumes:
  portainer_data:
  caddy_data:
  caddy_config:

### Caddyfile (도메인/SSL 없이 로컬 접속용)

:80 {
    # Portainer의 HTTP 포트(9000)로 리버스 프록시 설정
    reverse_proxy portainer:9000
}

## 포맷 후 순서

1. Execution Policy 변경: Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser ( 작업 완료 후 재 설정)
2. powershall 일반모드 실행
3. Scoop 설치: iwr -useb get.scoop.sh | iex (관리자 모드에서 실행 안됨)
4. 7zip, git, chezmoi 설치 : scoop install git chezmoi
5. 관리자 모드로 실행
6. git config --global user.email "sqwda22@gmail.com"
   git config --global user.name "sqwda22"
7. chezmoi init --apply sqwda22
8. 인프라 서비스(Docker) 설정 및 구동
- C:\dev\Infra 폴더 생성
- 디렉토리 이동
- docker-compose.yml, Caddyfile 설정 파일 생성
- 서비스 구동: docker-compose up -d
- 최초 실행 시 portainer:9000 포트가 열려 있는지 확인 필수
- Caddyfile 수정 후 docker exec -w /etc/caddy caddy caddy reload 실행
- 만약 접속 에러(ERR_CONNECTION_CLOSED) 발생 시 docker-compose up -d --force-recreate로 컨테이너 재생성
- 접속 확인: 브라우저 주소창에 http://localhost 입력. 자동 HTTPS 전환 방지를 위해 가급적 시크릿 모드 활용 권장.

8. docker, caddy, bitwarden 개별설정

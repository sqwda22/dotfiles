# Windows + WSL + Ubuntu 개발환경 온보딩

## 목적

- Windows 호스트 준비(앱/도구)
- WSL 설치 및 Ubuntu 배포판 준비
- Ubuntu 내부 개발환경(기본 패키지, Node.js, npm, Codex) 자동 부트스트랩
- Infra 템플릿(`C:\dev\Infra`) 동기화

## chezmoi 구성 파일

- `run_once_before_setup-wsl.ps1.tmpl`
  - WSL 활성화/점검, WSL2 기본값 설정, Ubuntu 설치
- `run_once_before_install-packages.ps1.tmpl`
  - Winget/Scoop 기반 Windows 앱/도구 설치
  - `Microsoft.WSL` 항목은 중복 방지를 위해 제외
- `run_once_after_bootstrap-ubuntu.ps1.tmpl`
  - Ubuntu 내부 `apt update/upgrade`, 필수 패키지, nvm + Node.js LTS + npm + Codex 설치
- `run_onchange_after_setup-infra.ps1.tmpl`
  - `data/Infra/*`를 `C:\dev\Infra`로 복사

## 최초 셋업 순서(포맷 후)

1. PowerShell 실행 정책 변경
   - `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`
2. Scoop 설치(일반 사용자 PowerShell)
   - `iwr -useb get.scoop.sh | iex`
3. 기본 도구 설치
   - `scoop install git chezmoi`
4. chezmoi 적용
   - `chezmoi init --apply sqwda22`
5. WSL 설치로 인해 재부팅 메시지가 나오면 재부팅
6. 재부팅 후 Ubuntu를 1회 실행해서 리눅스 사용자 생성 완료
7. 다시 `chezmoi apply -v` 실행
   - Ubuntu 부트스트랩(apt/Node.js/npm/Codex) 단계가 이어서 실행됨

## 운영 메모

- Ubuntu 부트스트랩은 idempotent하게 작성되어 재실행 가능
- `nvm` 기반으로 Node.js LTS를 설치/기본값으로 지정
- Codex는 `npm install -g @openai/codex@latest`로 설치
- Docker 인프라 접속 확인
  - Portainer: `https://localhost:9443`
  - Caddy: `http://localhost`

## 문서/설정 변경 시

1. `chezmoi cd`
2. 파일 수정 (예: `code data\\Infra\\setup.md`)
3. `chezmoi apply -v`
4. `git add .`
5. `git commit -m "Update onboarding workflow"`
6. `git push origin main`

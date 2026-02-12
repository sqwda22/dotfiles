# 포맷 후 체크리스트

각 항목은 완료 후 체크한다.

## 1) 초기 준비

- [ ] PowerShell 실행 정책 설정
  - `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`
- [ ] Scoop 설치
  - `iwr -useb get.scoop.sh | iex`
- [ ] 기본 도구 설치
  - `scoop install git chezmoi`

## 2) dotfiles 적용

- [ ] `chezmoi init --apply sqwda22` 실행
- [ ] WSL/Ubuntu 설치 안내가 나오면 재부팅
- [ ] Ubuntu 1회 실행 후 사용자 생성 완료
- [ ] `chezmoi apply -v` 재실행

## 3) secrets 설정

- [ ] Windows `dev.env` 작성
  - `$HOME\.secrets\dev.env`
- [ ] WSL `dev.env` 작성
  - `~/.secrets/dev.env`
- [ ] PowerShell 검증
  - `Secrets-Check`
- [ ] WSL 검증
  - `secrets_check`

## 4) 개발/인프라 검증

- [ ] Ubuntu에서 `node -v`, `npm -v` 확인
- [ ] `codex --version` 확인
- [ ] `C:\dev\Infra`에 파일 동기화 확인
- [ ] 필요 시 `docker compose up -d` 실행
- [ ] Portainer 접속 확인: `https://localhost:9443`
- [ ] Caddy 접속 확인: `http://localhost`

## 5) 기록

- [ ] 이번 작업 내용을 `logs/YYYY-MM.md`에 기록
- [ ] 변경된 운영 방법이 있으면 `setup.md` 또는 `secrets-setup.md` 갱신


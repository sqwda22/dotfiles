# dotfiles

Windows + WSL + Ubuntu 개발환경을 포맷 후 빠르게 복구하고, 운영 지식을 기록하기 위한 `chezmoi` 기반 저장소다.

## 전반적인 설명

이 레포의 목적은 아래 3가지다.

1. 포맷 후 환경세팅을 일관성 있고 빠르게 복구
2. 비개발자 관점에서 자주 잊는 설정/운영 지식을 문서로 축적
3. 비밀값(토큰/API 키)을 레포 밖에서 안전하게 관리

핵심 파일 구조:

- `run_once_before_setup-wsl.ps1.tmpl`: WSL/Ubuntu 사전 준비
- `run_once_before_install-packages.ps1.tmpl`: Windows 앱/도구 설치
- `run_once_after_bootstrap-ubuntu.ps1.tmpl`: Ubuntu 개발환경 부트스트랩
- `run_once_after_setup-secrets.ps1.tmpl`: Windows/WSL secrets 구조 및 자동 로더
- `run_onchange_after_setup-infra.ps1.tmpl`: `data/Infra` -> `C:\dev\Infra` 동기화
- `data/Infra/setup.md`: 온보딩 메인 가이드
- `data/Infra/secrets-setup.md`: secrets 운영 가이드
- `data/Infra/00-INDEX.md`: 문서 인덱스 (첫 진입점)

## 4) 포맷 후 실제 사용법

1. Windows PowerShell에서 최소 준비
   - `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`
   - `scoop install git chezmoi`
2. dotfiles 적용
   - `chezmoi init --apply sqwda22`
3. 재부팅 요구 시 재부팅 후 Ubuntu 1회 실행(사용자 생성)
4. 다시 적용
   - `chezmoi apply -v`
5. 비밀키 입력
   - Windows: `$HOME\.secrets\dev.env`
   - WSL: `~/.secrets/dev.env`
6. 검증
   - PowerShell: `Secrets-Check`
   - WSL: `secrets_check`
7. 인프라 확인
   - `C:\dev\Infra`에서 `docker compose up -d`
   - Portainer: `https://localhost:9443`
   - Caddy: `http://localhost`

## 5) 앞으로 어떻게 써야 안 까먹는지

1. 시작 문서는 항상 `data/Infra/00-INDEX.md`로 고정
2. 보안 관련은 `data/Infra/secrets-setup.md`만 참고
3. 설정 변경 시 `data/Infra/logs/YYYY-MM.md`에 짧게 기록
4. 새 툴/아키텍처 추가 시 런북 작성
   - 위치: `data/Infra/runbooks/`
   - 최소 항목: 목적 / 실행명령 / 검증명령 / 실패시 복구
5. 비밀값은 레포에 절대 저장하지 않고 `~/.secrets/*.env`만 사용


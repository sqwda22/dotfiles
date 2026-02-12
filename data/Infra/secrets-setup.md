# Secrets 운영 가이드 (Windows + WSL)

## 목적

- API 키/토큰을 dotfiles 레포에서 분리해 로컬에만 보관
- Windows PowerShell과 WSL Bash에서 동일한 구조로 자동 로드
- "설정해두고 잊어도" 셸 시작 시 자동 적용

## 생성되는 구조

- Windows: `$HOME\.secrets`
- WSL Ubuntu: `~/.secrets`

샘플 파일(값 없음):

- `dev.env.example`
- `README.md` (운영 규칙 요약)

실제 사용하는 파일(직접 생성):

- `dev.env`

`dev.env` 포맷:

```dotenv
GITHUB_PAT=...
OPENAI_API_KEY=...
ANTHROPIC_API_KEY=...
GOOGLE_API_KEY=...
```

## 자동 로딩 동작

- PowerShell 프로필(`$PROFILE.CurrentUserAllHosts`)에 로더가 추가됨
- WSL `~/.bashrc`에 로더가 추가됨
- 각 환경에서 `~/.secrets/*.env`를 읽어서 환경변수로 등록

## 점검 명령

PowerShell:

```powershell
Secrets-Check
# 또는
secrets-check
```

WSL Bash:

```bash
secrets_check
```

결과가 `OK`면 로딩 성공, `MISSING`이면 키 미설정

## 권장 권한/운영 규칙

WSL:

```bash
chmod 700 ~/.secrets
chmod 600 ~/.secrets/*.env
```

운영 규칙:

- 실제 값은 dotfiles/git에 절대 커밋하지 않기
- 문서에는 키 이름/경로/절차만 기록하기
- 키 갱신 시 `~/.secrets/dev.env`만 수정하면 됨

## 최초 설정 순서

1. `chezmoi apply -v`
2. `~/.secrets/dev.env` 생성 및 값 입력
3. 터미널 재시작 (또는 `source ~/.bashrc` / PowerShell 재실행)
4. `secrets_check` 또는 `Secrets-Check` 실행

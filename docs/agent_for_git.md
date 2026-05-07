# Agent 기반으로 작업하기 위한 Git, GWS 활용 강의 기획

작성일: 2026-05-07

## 강의 목표

개발자가 AI agent와 함께 일할 때 필요한 협업 기본기를 Git과 Google Workspace 자동화 관점에서 익힌다. 단순히 명령어를 외우는 강의가 아니라, "agent에게 맡겨도 되는 일"과 "사람이 반드시 검토해야 하는 일"을 구분하고, PR 중심의 협업 흐름과 Workspace 자동화를 하나의 실습 흐름으로 연결한다.

## 대상

- Git은 사용해 봤지만 branch, PR, conflict 해결이 아직 불안한 팀원
- AI agent에게 코드 수정, 문서 정리, 일정 생성, 자료 업로드를 맡기고 싶은 개발자
- 팀 프로젝트에서 GitHub, Google Calendar, Google Drive를 협업 도구로 쓰는 사람

## 핵심 메시지

AI agent는 작업 속도를 높여 주지만, 협업 규칙이 없으면 실수를 더 빠르게 확산시킨다. Git flow, PR 규칙, 권한 관리, 자동화 로그를 먼저 정리해야 agent 기반 작업이 안전해진다.

## 전체 구성

| 파트 | 주제 | 목표 | 예상 시간 |
| --- | --- | --- | --- |
| 1 | 실습 환경 확인 | 저장소, Git 상태, gws 인증 여부 확인 | 5분 |
| 2 | Git branch/worktree/sparse 실습 | issue branch, 독립 작업 디렉터리, 부분 checkout 이해 | 12분 |
| 3 | PR 준비 실습 | commit 규칙, diff 확인, PR 본문 초안 작성 | 8분 |
| 4 | 팀 규칙 Skill 작성 실습 | PR/브랜치 규칙을 `SKILL.md`로 직접 작성 | 10분 |
| 5 | gws Calendar 실습 | PR 리뷰 일정을 만들 payload 작성 또는 실행 | 8분 |
| 6 | gws Drive 실습 | 변경 요약 파일 업로드 payload 작성 또는 실행 | 8분 |
| 7 | 통합 agent 프롬프트 작성 | GitHub + Skill + gws 작업을 하나의 요청으로 정리 | 6분 |
| 8 | 회고와 체크리스트 | 사람이 검토해야 할 지점 정리 | 3분 |

총 시간: 60분

## 1시간 실습 운영 원칙

- 설명은 각 실습 시작 전 1~2분으로 제한한다.
- 참가자는 각 단계에서 최소 하나의 산출물을 만든다.
- Git 실습은 실제 commit 직전까지 진행하고, push/PR 생성은 팀 상황에 따라 선택한다.
- gws 실습은 인증된 사람만 실제 실행하고, 나머지는 JSON payload와 명령어를 작성하는 방식으로 진행한다.
- Calendar 생성, Drive 공유 권한 변경은 실습 중에도 실행 전 사람이 확인한다.
- 아래 상세 섹션은 실습 중 참고하는 명령어 모음으로 사용한다.

## 60분 실습 산출물

- issue 번호 기반 branch 이름 1개
- PR 설명 초안 1개
- 팀 Git 규칙용 `SKILL.md` 초안 1개
- Calendar 일정 생성 명령 또는 payload 1개
- Drive 업로드 명령 또는 payload 1개
- 통합 agent 프롬프트 1개

## 주제별 강의자료

- [00_overview.md](./00_overview.md): 1시간 실습 강의 개요
- [01_git_pr_workflow.md](./01_git_pr_workflow.md): Git branch와 PR 실습
- [02_team_git_skill.md](./02_team_git_skill.md): 팀 PR/브랜치 규칙을 Skill로 만들기
- [03_gws_calendar_drive.md](./03_gws_calendar_drive.md): gws CLI로 Calendar와 Drive 자동화하기
- [04_integrated_practice.md](./04_integrated_practice.md): GitHub + Skill + gws 통합 실습

## 참가자 사전 준비

- GitHub 계정과 테스트 저장소 접근 권한
- 로컬 Git 설치
- 실습용 issue 번호 1개
- Codex 또는 사용 중인 agent 실행 환경
- 선택: `gws` 설치와 Google 인증

gws를 직접 실행할 참가자는 강의 전에 다음을 완료한다.

```bash
npm install -g @googleworkspace/cli
gws auth setup
gws auth login --scopes drive,calendar
```

gws 인증이 어려운 참가자는 명령어와 payload 작성까지만 실습한다.

## 1. Agent 기반 개발 협업 개요

### 전달할 내용

- Agent는 "명령 실행자"가 아니라 "작업 단위 수행자"로 봐야 한다.
- 좋은 agent 작업 요청은 다음 정보를 포함한다.
  - 목표: 무엇을 바꿀 것인가
  - 범위: 어떤 파일, 어떤 저장소, 어떤 브랜치인가
  - 제한: 건드리면 안 되는 파일, 하면 안 되는 명령
  - 검증: 어떤 테스트나 확인을 통과해야 하는가
  - 결과물: PR, 문서, 일정, Drive 업로드 등

### 예시 프롬프트

```text
wel-bridge 저장소에서 issue #8 기준으로 브랜치를 만들고,
README의 Git 협업 규칙 섹션을 보강해줘.
main에는 push하지 말고, 변경 후 diff와 테스트 여부를 요약해줘.
```

### 강조 포인트

- Agent에게 Git 권한을 줄수록 branch, commit, PR 규칙이 중요해진다.
- Calendar와 Drive 자동화는 편하지만, 개인정보와 파일 공유 권한을 실수하기 쉽다.
- 자동화 전에는 항상 `--dry-run`, `git diff`, 권한 확인을 습관화한다.

## 2. Git flow의 이해

### 기본 흐름

```text
issue 생성
-> branch 생성
-> local 작업
-> commit
-> push
-> pull request 생성
-> review
-> merge
-> branch 정리
```

### 강의에서 사용할 branch 전략

팀 프로젝트에서는 복잡한 Git Flow보다 GitHub Flow에 가까운 단순 흐름을 추천한다.

- `main`: 항상 배포 가능하거나 팀 기준으로 안정적인 상태
- `feat/{issue-number}`: 기능 추가
- `fix/{issue-number}`: 버그 수정
- `docs/{issue-number}`: 문서 작업
- `refactor/{issue-number}`: 동작 변경 없는 구조 개선

예시:

```bash
git switch main
git pull origin main
git switch -c docs/23
```

### Git worktree 개념과 사용법

`git worktree`는 하나의 Git 저장소에서 여러 작업 디렉터리를 동시에 쓰게 해준다. 각 worktree는 같은 repository history를 공유하지만, 서로 다른 branch와 working tree를 가진다.

Agent 기반 작업에서 유용한 경우:

- 현재 작업 중인 변경을 보존한 채 agent에게 별도 branch 작업을 맡길 때
- 문서 작업, 기능 작업, 리뷰 확인을 동시에 진행할 때
- 실습 중 작업 디렉터리를 분리해 실수를 줄이고 싶을 때

기본 명령:

```bash
git worktree list
git worktree add ../wel-bridge-docs-23 -b docs/23 main
cd ../wel-bridge-docs-23
git status --short
git branch --show-current
```

정리:

```bash
cd ../wel-bridge
git worktree remove ../wel-bridge-docs-23
git worktree prune
```

주의점:

- 같은 branch를 여러 worktree에서 동시에 checkout할 수 없다.
- agent에게 정확한 worktree 경로를 알려줘야 한다.
- worktree 디렉터리를 수동 삭제하기보다 `git worktree remove`를 사용한다.

### Git sparse checkout 개념과 사용법

`git sparse-checkout`은 저장소 전체가 아니라 필요한 경로만 working tree에 펼치는 기능이다. monorepo나 큰 저장소에서 특정 폴더만 작업할 때 유용하다.

기본 명령:

```bash
git sparse-checkout init --cone
git sparse-checkout set ideas meetings
git sparse-checkout list
```

처음 clone할 때부터 sparse로 받을 수도 있다.

```bash
git clone --filter=blob:none --sparse https://github.com/zzangss/wel-bridge.git
cd wel-bridge
git sparse-checkout set ideas meetings
```

되돌리기:

```bash
git sparse-checkout disable
```

주의점:

- sparse checkout은 보안 기능이 아니다.
- 빌드나 테스트가 펼쳐지지 않은 경로에 의존하면 실패할 수 있다.
- agent에게 현재 sparse checkout 범위를 알려줘야 파일 누락을 오해하지 않는다.

### Commit 규칙

Conventional Commit 형식을 사용한다.

```text
docs: add PR collaboration guide
feat: add calendar automation script
fix: handle missing drive folder id
chore: update gws setup notes
```

좋은 commit의 기준:

- 하나의 commit은 하나의 의도를 가진다.
- 메시지는 "무엇을 했는지"보다 "왜 필요한지"가 드러나면 더 좋다.
- 생성 파일, 포맷팅 변경, 기능 변경을 가능하면 분리한다.

## 3. PR 방법과 협업시 규칙

### PR 생성 전 체크리스트

- 작업 branch가 `main`에서 최신 상태인지 확인
- `git status`로 원치 않는 파일이 포함되지 않았는지 확인
- `git diff`로 agent가 변경한 내용을 직접 읽기
- 필요한 테스트, 빌드, 문서 미리보기 실행
- PR 설명에 issue, 변경 요약, 검증 결과 작성

```bash
git status --short
git diff
git log --oneline --decorate -5
```

### PR 설명 템플릿

```md
## 변경 사항

- 

## 검증

- [ ] 테스트 실행:
- [ ] 문서/화면 확인:

## 리뷰 포인트

- 

## 관련 이슈

Closes #
```

### 협업 규칙

- `main`에 직접 push하지 않는다.
- PR은 작게 만든다. 리뷰어가 10~15분 안에 핵심을 볼 수 있는 크기가 좋다.
- 자동 생성 코드나 agent 변경분도 사람이 읽고 승인한다.
- 리뷰 의견은 코드가 아니라 변경의 위험, 요구사항, 유지보수성에 초점을 둔다.
- conflict가 나면 무리하게 덮어쓰지 말고, 어떤 변경이 충돌했는지 먼저 설명한다.

### Agent에게 PR 작업을 맡길 때의 규칙

Agent에게 맡겨도 되는 일:

- branch 생성
- 반복적인 문서 수정
- PR 설명 초안 작성
- 변경 파일 목록과 diff 요약
- 테스트 실행과 실패 로그 요약

사람이 검토해야 하는 일:

- 요구사항 해석
- 보안/개인정보 관련 변경
- 공유 권한 변경
- merge 최종 승인
- 실패한 테스트를 무시할지 여부

## 4. gws CLI 설치와 인증

gws는 Google Workspace를 터미널에서 다루기 위한 CLI다. Drive, Gmail, Calendar, Sheets, Docs, Chat 등 Workspace API를 명령어로 호출할 수 있고, JSON 출력이 기본이라 agent와 함께 쓰기 좋다. 단, GitHub README 기준으로 "공식 지원 Google 제품은 아니다"라는 점을 강의에서 명확히 안내한다.

### 공식 자료

- GitHub: https://github.com/googleworkspace/cli
- 문서: https://googleworkspace-cli.mintlify.app/
- Calendar 명령: https://googleworkspace-cli.mintlify.app/commands/calendar
- Drive 명령: https://googleworkspace-cli.mintlify.app/commands/drive

### 설치 옵션

권장 흐름:

```bash
npm install -g @googleworkspace/cli
gws --help
```

macOS/Linux Homebrew:

```bash
brew install googleworkspace-cli
```

소스 빌드:

```bash
cargo install --git https://github.com/googleworkspace/cli --locked
```

### 인증

`gcloud`가 설치되어 있으면 가장 단순한 흐름:

```bash
gws auth setup
gws auth login
```

필요한 scope만 선택하는 예시:

```bash
gws auth login --scopes drive,calendar
```

CI나 headless 환경:

```bash
gws auth export --unmasked > credentials.json
export GOOGLE_WORKSPACE_CLI_CREDENTIALS_FILE=/path/to/credentials.json
```

### 보안 주의점

- `credentials.json`은 Git에 commit하지 않는다.
- `.env`, 인증 파일, access token은 `.gitignore`에 포함한다.
- 실습 계정 또는 테스트용 Google Cloud project를 사용한다.
- 처음에는 `drive,calendar`처럼 필요한 scope만 승인한다.
- 공유 권한을 바꾸는 명령은 반드시 대상 파일 ID와 role을 확인한다.

## 5. gws CLI로 Calendar 일정 자동화

### 실습 목표

GitHub issue나 PR 작업이 생겼을 때, 팀 캘린더에 리뷰 일정 또는 작업 미팅을 자동 생성한다.

### 캘린더 목록 확인

```bash
gws calendar calendarList list
```

### 오늘 일정 확인

```bash
gws calendar +agenda
```

### 일정 생성: helper command

```bash
gws calendar +insert \
  --summary "wel-bridge PR review" \
  --start "2026-05-07T19:00:00+09:00" \
  --end "2026-05-07T19:30:00+09:00" \
  --attendee teammate@example.com \
  --location "Google Meet" \
  --description "PR 리뷰 및 merge 기준 확인"
```

### 일정 생성: API command

```bash
gws calendar events insert \
  --params '{"calendarId": "primary"}' \
  --json '{
    "summary": "wel-bridge PR review",
    "description": "PR 리뷰 및 merge 기준 확인",
    "start": {"dateTime": "2026-05-07T19:00:00+09:00", "timeZone": "Asia/Seoul"},
    "end": {"dateTime": "2026-05-07T19:30:00+09:00", "timeZone": "Asia/Seoul"},
    "attendees": [
      {"email": "teammate@example.com"}
    ]
  }'
```

### 빈 시간 확인

```bash
gws calendar freebusy query --json '{
  "timeMin": "2026-05-07T09:00:00+09:00",
  "timeMax": "2026-05-07T22:00:00+09:00",
  "items": [
    {"id": "me@example.com"},
    {"id": "teammate@example.com"}
  ]
}'
```

### Agent에게 맡기는 예시

```text
내 캘린더에서 오늘 19:00~22:00 사이 빈 시간을 확인하고,
wel-bridge PR #23 리뷰 일정을 30분으로 잡아줘.
참석자는 teammate@example.com이고, 일정 설명에는 PR 링크와 리뷰 체크리스트를 넣어줘.
일정을 만들기 전에 생성될 payload를 먼저 보여줘.
```

## 6. gws CLI로 코드 관련 데이터 파일을 Google Drive에 자동화

### 실습 목표

코드 리뷰나 회의에 필요한 산출물을 Drive에 업로드하고, 팀원에게 읽기 권한을 부여한다.

예시 산출물:

- 테스트 결과 로그
- 회의록 Markdown
- PR 변경 요약
- API 응답 샘플 JSON
- 분석용 CSV

### Drive 파일 목록 확인

```bash
gws drive files list --params '{"pageSize": 10, "orderBy": "modifiedTime desc"}'
```

### 폴더 생성

```bash
gws drive files create --json '{
  "name": "wel-bridge-review-assets",
  "mimeType": "application/vnd.google-apps.folder"
}'
```

### 파일 업로드

```bash
gws drive +upload ./test-results/report.json \
  --parent FOLDER_ID \
  --name "20260507-pr23-test-report.json"
```

또는 API command:

```bash
gws drive files create \
  --json '{"name": "20260507-pr23-test-report.json", "parents": ["FOLDER_ID"]}' \
  --upload ./test-results/report.json
```

### 공유 권한 설정

```bash
gws drive permissions create \
  --params '{"fileId": "FILE_ID"}' \
  --json '{"type": "user", "role": "reader", "emailAddress": "teammate@example.com"}'
```

### Agent에게 맡기는 예시

```text
test-results/report.json을 Google Drive의 wel-bridge-review-assets 폴더에 업로드하고,
파일명을 20260507-pr23-test-report.json으로 바꿔줘.
업로드 후 teammate@example.com에게 reader 권한을 부여해줘.
실행 전에 대상 폴더 ID, 파일명, 권한 payload를 먼저 확인해줘.
```

## 7. 팀 PR, 브랜치 규칙을 스킬로 만드는 방법

### 실습 목표

팀의 PR, branch, commit 규칙을 매번 프롬프트에 길게 쓰지 않고, agent가 자동으로 참고할 수 있는 `SKILL.md`로 만든다. 목표는 "규칙을 문서화"하는 데서 끝내지 않고, 실제 작업 전에 branch 이름, commit 형식, PR 체크리스트를 agent가 일관되게 확인하도록 만드는 것이다.

### 왜 스킬로 만드는가

- 팀마다 branch naming, PR 템플릿, 리뷰 기준이 다르다.
- 일반적인 Git 지식만으로는 팀 규칙을 정확히 따르기 어렵다.
- 반복 프롬프트는 빠뜨리기 쉽지만, 스킬은 특정 작업에서 자동으로 불러올 수 있다.
- 신규 팀원이 agent를 쓰더라도 같은 협업 기준을 적용하게 만들 수 있다.

### 스킬에 넣을 내용

필수:

- 스킬 이름과 트리거 설명
- branch naming 규칙
- commit message 규칙
- PR 생성 전 체크리스트
- PR 본문 템플릿
- 금지 작업
- 검증 방법

선택:

- issue 유형별 branch prefix 매핑
- 리뷰어 지정 규칙
- release branch 또는 hotfix 규칙
- GitHub CLI 사용 예시
- 팀 저장소별 예외 규칙

### 권장 파일 구조

```text
team-git-workflow/
└── SKILL.md
```

규칙이 커지면 reference 파일을 분리한다.

```text
team-git-workflow/
├── SKILL.md
└── references/
    ├── pr-template.md
    └── branch-rules.md
```

### SKILL.md 예시

```md
---
name: team-git-workflow
description: Use when working in the team's Git repository, creating branches, making commits, opening PRs, or asking an agent to follow team PR and branch rules.
---

# Team Git Workflow

Use this skill before changing code or documentation in the team repository.

## Branch Rules

- Do not work directly on `main`.
- Start from the latest `main`.
- Branch names must use an issue number:
  - `feat/{issue-number}` for features
  - `fix/{issue-number}` for bug fixes
  - `docs/{issue-number}` for documentation
  - `refactor/{issue-number}` for refactoring

## Commit Rules

Use Conventional Commit prefixes:

- `feat`
- `fix`
- `docs`
- `refactor`
- `test`
- `chore`

Keep each commit focused on one intent.

## PR Rules

Before opening a PR:

- Run `git status --short`.
- Review `git diff`.
- Run relevant tests or explain why tests were not run.
- Fill out the PR template.

## Safety Rules

- Do not push directly to `main`.
- Do not rewrite shared branch history unless explicitly requested.
- Do not include credentials, `.env`, tokens, or generated private files.
- Ask before changing Drive sharing permissions or creating Calendar events.
```

### 좋은 description 작성법

`description`은 agent가 스킬을 언제 쓸지 판단하는 핵심 메타데이터다. 너무 짧으면 필요한 순간에 불리지 않고, 너무 일반적이면 불필요한 상황에도 불린다.

좋은 예:

```yaml
description: Use when working in the team's Git repository, creating branches, making commits, opening PRs, or asking an agent to follow team PR and branch rules.
```

부족한 예:

```yaml
description: Git rules.
```

### Agent에게 스킬 제작을 맡기는 프롬프트

```text
우리 팀의 Git 협업 규칙을 Codex Skill로 만들어줘.
스킬 이름은 team-git-workflow로 하고, 다음 규칙을 포함해줘.

- main 직접 작업 금지
- issue 번호 기반 branch naming: feat/8, fix/15, docs/23
- commit prefix: feat, fix, docs, refactor, test, chore
- PR 생성 전 git status, git diff, 테스트 확인
- PR 템플릿 포함
- credential, .env, token commit 금지

SKILL.md는 간결하게 작성하고, 반복적으로 참고할 템플릿만 포함해줘.
```

### 실습 흐름

1. 팀 규칙을 자연어로 정리한다.
2. 규칙을 `SKILL.md`의 frontmatter와 body로 나눈다.
3. `description`이 어떤 요청에서 trigger될지 점검한다.
4. branch 생성 요청, PR 생성 요청 예시로 스킬이 필요한 상황을 테스트한다.
5. 빠진 규칙을 보강한다.

### 검증 질문

- 이 스킬은 `main에 바로 작업해줘` 같은 요청을 받았을 때 막을 수 있는가?
- issue 번호가 없는 branch 요청을 받았을 때 확인 질문을 하도록 되어 있는가?
- PR 생성 전에 diff와 테스트 확인을 요구하는가?
- 팀원이 읽어도 불필요하게 긴 설명 없이 바로 규칙을 이해할 수 있는가?

## 8. 통합 실습 시나리오

### 상황

팀원이 `wel-bridge` 프로젝트에서 문서 변경 PR을 준비한다. Agent를 이용해 Git 작업을 정리하고, 리뷰 일정을 캘린더에 등록하고, 리뷰 자료를 Drive에 업로드한다.

### 실습 순서

1. Issue 번호를 기준으로 branch 생성
2. 문서 수정
3. `git diff` 확인
4. commit 생성
5. PR 설명 초안 작성
6. 테스트 결과 또는 변경 요약 파일 생성
7. Drive에 업로드
8. 팀원에게 공유 권한 부여
9. Calendar 리뷰 일정 생성
10. PR 본문에 Drive 링크와 일정 정보를 첨부

### 통합 프롬프트

```text
wel-bridge 저장소에서 issue #23 문서 작업을 진행해줘.
branch는 docs/23으로 만들고, main에 직접 push하지 마.

작업 후 다음을 수행해줘.
1. git diff와 변경 요약을 확인한다.
2. PR 설명 초안을 Markdown으로 작성한다.
3. 변경 요약 파일을 Google Drive의 wel-bridge-review-assets 폴더에 업로드한다.
4. 리뷰어에게 reader 권한을 부여한다.
5. 오늘 19:00 이후 가능한 30분 리뷰 일정을 Google Calendar에 생성한다.

Drive 업로드와 Calendar 생성은 실행 전에 payload를 보여주고 승인받아라.
```

## 강의자료 슬라이드 구성안

1. 제목: Agent 기반으로 작업하기 위한 Git, GWS 사용 방법
2. 오늘 만들 산출물: branch, PR 초안, Skill, gws 명령, 통합 프롬프트
3. 실습 1: issue 번호 기반 branch 만들기
4. 실습 2: diff 확인과 PR 설명 초안 작성
5. 실습 3: 팀 PR/브랜치 규칙을 Skill로 만들기
6. 실습 4: gws Calendar 일정 payload 만들기
7. 실습 5: gws Drive 업로드 payload 만들기
8. 실습 6: GitHub + Skill + gws 통합 프롬프트 작성
9. 실행 전 안전 체크리스트
10. Q&A

## 1시간 실습 진행안

### 실습 0: 환경 확인

Agent 요청:

```text
현재 저장소의 git 상태를 확인하고,
main에서 직접 작업 중인지 확인해줘.
```

참가자 실행:

```bash
git status --short
git branch --show-current
```

산출물:

- 현재 branch 이름
- 작업 전 변경 파일 유무

### 실습 1: Agent에게 Git branch 작업 맡기기

```text
wel-bridge 저장소에서 issue #23 문서 작업 branch를 준비해줘.
branch는 docs/23으로 만들고 main에는 직접 push하지 마.
작업 전 git status를 확인하고, branch 생성 후 현재 branch를 보여줘.
```

참가자 확인:

```bash
git branch --show-current
git status --short
```

산출물:

- `docs/23` 형태의 branch
- 작업 전 상태 확인 결과

### 실습 2: PR 설명 초안 만들기

```text
현재 변경 사항을 기준으로 PR 설명 초안을 작성해줘.
변경 사항, 검증, 리뷰 포인트, 관련 이슈 섹션을 포함해줘.
아직 commit이나 push는 하지 마.
```

참가자 확인:

```bash
git diff
```

산출물:

- PR 설명 초안
- 사람이 확인한 diff 요약

### 실습 3: 팀 규칙을 Skill로 만들기

```text
우리 팀의 Git 협업 규칙을 Codex Skill로 만들어줘.
main 직접 작업 금지, issue 번호 기반 branch naming,
commit prefix, PR 체크리스트, credential commit 금지를 포함해줘.
```

참가자 작성 파일:

```text
team-git-workflow/
└── SKILL.md
```

산출물:

- `SKILL.md` frontmatter
- branch 규칙
- commit 규칙
- PR 체크리스트
- 금지 작업

### 실습 4: gws Calendar 일정 명령 만들기

```bash
gws calendar +insert \
  --summary "wel-bridge PR review" \
  --start "2026-05-07T19:00:00+09:00" \
  --end "2026-05-07T19:30:00+09:00" \
  --attendee teammate@example.com \
  --description "PR 리뷰 및 merge 기준 확인"
```

인증된 참가자만 실제 실행한다. 인증되지 않은 참가자는 시간, 참석자, 설명이 들어간 명령어만 작성한다.

산출물:

- Calendar 일정 생성 명령
- 실행 전 확인할 참석자, 시간대, 설명

### 실습 5: gws Drive 업로드 명령 만들기

```bash
gws drive +upload ./test-results/report.json \
  --parent FOLDER_ID \
  --name "20260507-pr23-test-report.json"
```

인증된 참가자만 실제 실행한다. 인증되지 않은 참가자는 폴더 ID, 파일명, 권한 payload를 작성한다.

산출물:

- Drive 업로드 명령
- 공유 권한 payload

### 실습 6: 통합 agent 프롬프트 작성

```text
wel-bridge 저장소에서 issue #23 문서 작업을 진행해줘.
팀 Git Skill을 따라 branch와 PR 규칙을 지켜줘.

작업 후 다음을 수행해줘.
1. git diff를 요약한다.
2. PR 설명 초안을 작성한다.
3. 리뷰 일정을 만들 Calendar payload를 보여준다.
4. 변경 요약 파일을 Drive에 업로드할 명령을 보여준다.

Calendar 생성과 Drive 업로드는 실행 전에 내 확인을 받아라.
```

산출물:

- 참가자별 통합 프롬프트 1개
- 실행 전 안전 체크리스트

## 실습 준비물

- GitHub 테스트 저장소 또는 `wel-bridge` 문서 작업용 issue
- Google 계정
- Google Cloud project 또는 `gws auth setup` 가능한 환경
- Node.js 18 이상 또는 Homebrew
- `gws` 설치
- 테스트용 Google Drive 폴더
- 테스트용 참석자 이메일
- `.gitignore`에 인증 파일 제외 규칙 추가
- 팀 Git 규칙을 담을 예시 `SKILL.md`

## 실무 적용 체크리스트

- [ ] 팀 branch naming 규칙이 있다.
- [ ] commit prefix 규칙이 있다.
- [ ] PR 템플릿이 있다.
- [ ] 팀 PR/브랜치 규칙을 agent Skill로 만들었다.
- [ ] Skill의 `description`이 branch, commit, PR 작업에서 trigger되도록 작성되어 있다.
- [ ] main 직접 push를 금지한다.
- [ ] agent가 실행해도 되는 명령과 안 되는 명령을 구분한다.
- [ ] Google Workspace scope를 최소 권한으로 설정한다.
- [ ] Drive 공유 권한 변경은 실행 전 payload를 확인한다.
- [ ] Calendar 일정 생성은 시간대와 참석자를 확인한다.
- [ ] 자동화 결과는 PR이나 회의록에 링크로 남긴다.

## 강의 마무리 문장

Agent 기반 개발의 핵심은 더 많은 일을 자동으로 시키는 것이 아니라, 자동화해도 안전한 작업 단위를 설계하는 것이다. Git은 변경 이력을 통제하고, PR은 팀 검토를 보장하며, gws CLI는 협업 도구의 반복 작업을 코드처럼 다룰 수 있게 해준다.

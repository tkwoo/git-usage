# git-usage

Agent 기반으로 Git과 Google Workspace CLI를 함께 사용하는 실습 강의자료입니다.

## 구성

- [docs/README.md](./docs/README.md): 강의자료 목차
- [docs/00_overview.md](./docs/00_overview.md): 1시간 실습 강의 개요
- [docs/01_git_pr_workflow.md](./docs/01_git_pr_workflow.md): Git branch, worktree, sparse checkout, PR 실습
- [docs/02_team_git_skill.md](./docs/02_team_git_skill.md): 팀 PR/브랜치 규칙을 Skill로 만들기
- [docs/03_gws_calendar_drive.md](./docs/03_gws_calendar_drive.md): gws CLI로 Calendar와 Drive 자동화하기
- [docs/04_integrated_practice.md](./docs/04_integrated_practice.md): GitHub + Skill + gws 통합 실습
- [slides/index.html](./slides/index.html): HTML 발표자료

## GitHub Pages

```text
https://tkwoo.github.io/git-usage/
```

## 로컬 슬라이드 실행

```bash
python3 -m http.server 8057
```

브라우저에서 다음 주소를 엽니다.

```text
http://localhost:8057/slides/
```

## 실습 주제

- issue 번호 기반 branch 생성
- `git worktree`로 작업 디렉터리 분리
- `git sparse-checkout`으로 필요한 경로만 checkout
- PR 설명 초안 작성
- 팀 Git 규칙을 `SKILL.md`로 작성
- gws CLI로 Calendar 일정과 Drive 업로드 payload 작성
- GitHub + Skill + gws 통합 agent 프롬프트 작성

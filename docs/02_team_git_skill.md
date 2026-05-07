# 02. 팀 PR/브랜치 규칙을 Skill로 만들기

## 목표

팀의 Git 협업 규칙을 매번 프롬프트에 쓰지 않고, agent가 반복 적용할 수 있는 `SKILL.md`로 만든다.

## 시간

10분

## 왜 Skill로 만드는가

- 팀마다 branch naming, PR 템플릿, 리뷰 기준이 다르다.
- 일반 Git 지식만으로는 팀 규칙을 정확히 따르기 어렵다.
- 반복 프롬프트는 빠뜨리기 쉽다.
- Skill은 특정 작업에서 agent가 자동으로 참고할 수 있는 규칙 문서가 된다.

## 실습 프롬프트

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

## 권장 파일 구조

```text
team-git-workflow/
└── SKILL.md
```

## SKILL.md 예시

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

## description 작성 기준

좋은 `description`은 스킬이 불려야 하는 상황을 구체적으로 말한다.

좋은 예:

```yaml
description: Use when working in the team's Git repository, creating branches, making commits, opening PRs, or asking an agent to follow team PR and branch rules.
```

부족한 예:

```yaml
description: Git rules.
```

## 검증 질문

- `main에 바로 작업해줘` 같은 요청을 받았을 때 막을 수 있는가?
- issue 번호가 없는 branch 요청을 받았을 때 확인 질문을 하도록 되어 있는가?
- PR 생성 전에 diff와 테스트 확인을 요구하는가?
- 팀원이 읽어도 바로 적용할 수 있을 만큼 간결한가?

## 산출물

- `team-git-workflow/SKILL.md`
- branch 규칙
- commit 규칙
- PR 체크리스트
- 금지 작업

# 01. Git Branch와 PR 실습

## 목표

Agent에게 Git 작업을 맡기되, 사람이 branch, diff, PR 내용을 검토하는 흐름을 익힌다.

## 시간

20분

## 실습 1: Git 상태 확인

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

확인할 것:

- 현재 branch가 `main`인지
- 작업 전 변경 파일이 있는지
- 실습 중 건드리면 안 되는 파일이 있는지

## 실습 2: issue 번호 기반 branch 생성

Agent 요청:

```text
wel-bridge 저장소에서 issue #23 문서 작업 branch를 준비해줘.
branch는 docs/23으로 만들고 main에는 직접 push하지 마.
작업 전 git status를 확인하고, branch 생성 후 현재 branch를 보여줘.
```

직접 실행할 경우:

```bash
git switch main
git pull origin main
git switch -c docs/23
```

## Git worktree 개념

`git worktree`는 하나의 Git 저장소에서 여러 작업 디렉터리를 동시에 쓰게 해준다. 같은 repository history를 공유하지만, 각 worktree는 독립적인 working tree와 branch를 가진다.

agent 기반 작업에서는 다음 상황에 유용하다.

- 현재 작업 중인 변경을 건드리지 않고 agent에게 별도 branch 작업을 맡길 때
- 문서 작업과 기능 작업을 동시에 진행할 때
- PR 리뷰용 branch를 따로 열어 확인할 때
- 실습 중 참가자별 작업 디렉터리를 분리하고 싶을 때

### worktree 기본 명령

```bash
git worktree list
git worktree add ../wel-bridge-docs-23 -b docs/23 main
cd ../wel-bridge-docs-23
git branch --show-current
git status --short
```

작업이 끝나면 제거한다.

```bash
cd ../wel-bridge
git worktree remove ../wel-bridge-docs-23
git worktree prune
```

주의할 점:

- 같은 branch를 두 worktree에서 동시에 checkout할 수 없다.
- worktree 디렉터리를 그냥 삭제하지 말고 가능하면 `git worktree remove`를 사용한다.
- agent에게 worktree 경로를 명확히 알려줘야 엉뚱한 작업 디렉터리에서 수정하지 않는다.

### Agent 요청 예시

```text
현재 작업 디렉터리는 건드리지 말고,
../wel-bridge-docs-23 경로에 git worktree를 만들어 issue #23 문서 작업을 준비해줘.
branch는 docs/23으로 만들고, 생성 후 해당 worktree의 git status를 보여줘.
```

## Git sparse checkout 개념

`git sparse-checkout`은 저장소 전체가 아니라 필요한 경로만 working tree에 펼치는 기능이다. 대형 저장소나 문서/특정 서비스만 수정하는 실습에서 불필요한 파일을 줄일 수 있다.

적합한 상황:

- monorepo에서 특정 서비스 폴더만 작업할 때
- 문서 폴더만 수정하는 agent 작업을 시킬 때
- 실습 환경에서 checkout 파일 수를 줄이고 싶을 때

### sparse checkout 기본 명령

이미 clone한 저장소에서 일부 폴더만 보이게 한다.

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

주의할 점:

- sparse checkout은 보안 기능이 아니다. 숨긴 파일을 권한상 못 보게 만드는 것이 아니라 working tree에 펼치지 않을 뿐이다.
- 빌드나 테스트가 다른 경로 파일에 의존하면 실패할 수 있다.
- agent에게 sparse checkout 상태임을 알려줘야 "파일이 없다"고 오해하지 않는다.

### Agent 요청 예시

```text
이 저장소는 sparse checkout으로 ideas와 meetings 폴더만 펼쳐서 작업해줘.
다른 경로가 필요하면 바로 수정하지 말고, 필요한 경로를 먼저 알려줘.
```

## Branch 규칙

- `feat/{issue-number}`: 기능 추가
- `fix/{issue-number}`: 버그 수정
- `docs/{issue-number}`: 문서 작업
- `refactor/{issue-number}`: 리팩터링

## Commit 규칙

```text
docs: add PR collaboration guide
feat: add calendar automation script
fix: handle missing drive folder id
chore: update gws setup notes
```

## 실습 3: PR 설명 초안 작성

Agent 요청:

```text
현재 변경 사항을 기준으로 PR 설명 초안을 작성해줘.
변경 사항, 검증, 리뷰 포인트, 관련 이슈 섹션을 포함해줘.
아직 commit이나 push는 하지 마.
```

확인 명령:

```bash
git diff
git status --short
```

## PR 템플릿

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

## 산출물

- issue 번호 기반 branch 이름
- 필요 시 별도 `git worktree` 작업 디렉터리
- 필요 시 `sparse-checkout` 대상 경로
- 변경 전후 `git status`
- 사람이 읽은 `git diff` 요약
- PR 설명 초안

## 안전 체크

- `main`에 직접 push하지 않는다.
- agent가 만든 변경도 사람이 `git diff`로 읽는다.
- 인증 파일, `.env`, token은 commit하지 않는다.
- push와 PR 생성은 팀 상황에 맞게 선택한다.
- worktree나 sparse checkout을 쓰는 경우 agent에게 현재 작업 경로와 checkout 범위를 명확히 알려준다.

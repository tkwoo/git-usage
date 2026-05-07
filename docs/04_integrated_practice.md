# 04. 통합 실습: GitHub + Skill + gws

## 목표

Git branch/PR 준비, 팀 규칙 Skill, Calendar/Drive 자동화를 하나의 agent 작업 요청으로 연결한다.

## 시간

9분

## 상황

팀원이 `wel-bridge` 프로젝트에서 문서 변경 PR을 준비한다. Agent를 이용해 Git 작업을 정리하고, 리뷰 일정을 캘린더에 등록할 payload를 만들고, 리뷰 자료를 Drive에 업로드할 명령을 준비한다.

## 통합 프롬프트

```text
wel-bridge 저장소에서 issue #23 문서 작업을 진행해줘.
팀 Git Skill을 따라 branch와 PR 규칙을 지켜줘.

작업 전:
1. git status를 확인한다.
2. 현재 branch를 확인한다.
3. main에 직접 작업 중이면 issue 번호 기반 branch docs/23을 만든다.

작업 후:
1. git diff를 요약한다.
2. PR 설명 초안을 작성한다.
3. 리뷰 일정을 만들 Calendar payload를 보여준다.
4. 변경 요약 파일을 Drive에 업로드할 명령을 보여준다.

제한:
- main에는 직접 push하지 마.
- commit, push, PR 생성은 내가 확인한 뒤 진행해.
- Calendar 생성과 Drive 업로드는 실행 전에 payload를 보여주고 승인받아.
- credential, .env, token은 절대 commit하지 마.
```

## 실행 전 체크리스트

- [ ] 현재 branch가 `main`이 아닌가?
- [ ] branch 이름에 issue 번호가 있는가?
- [ ] `git diff`를 사람이 읽었는가?
- [ ] PR 설명에 변경 사항, 검증, 리뷰 포인트, 관련 이슈가 있는가?
- [ ] Calendar 참석자, 시간대, 설명을 확인했는가?
- [ ] Drive 파일명, 폴더 ID, 공유 권한 role을 확인했는가?
- [ ] 인증 파일, `.env`, token이 commit 대상에 없는가?

## 참가자 최종 산출물

- 통합 agent 프롬프트 1개
- PR 설명 초안 1개
- Calendar payload 1개
- Drive upload 명령 1개
- 실행 전 안전 체크리스트

## 마무리 문장

Agent에게 일을 맡길수록 팀 규칙은 더 구체적이어야 한다. Git은 변경 이력을 통제하고, Skill은 팀 규칙을 반복 적용하게 만들며, gws CLI는 협업 도구의 반복 작업을 명령어와 payload로 관리하게 해준다.

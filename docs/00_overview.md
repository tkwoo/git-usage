# 00. 1시간 실습 강의 개요

## 강의 제목

Agent 기반으로 작업하기 위한 Git, GWS 사용 방법

## 목표

1시간 안에 참가자가 agent와 함께 Git branch, PR 초안, 팀 규칙 Skill, gws Calendar/Drive 자동화 명령을 직접 만들어 본다.

## 핵심 메시지

Agent 기반 개발의 핵심은 더 많은 일을 자동으로 시키는 것이 아니라, 자동화해도 안전한 작업 단위를 설계하는 것이다.

## 전체 실습 흐름

| 순서 | 주제 | 시간 | 산출물 |
| --- | --- | --- | --- |
| 1 | 환경 확인 | 5분 | 현재 branch, 작업 전 상태 |
| 2 | Git branch/worktree/sparse 실습 | 12분 | issue branch, worktree 경로, sparse 대상 |
| 3 | PR 준비 실습 | 8분 | PR 설명 초안 |
| 4 | 팀 규칙 Skill 작성 | 10분 | `SKILL.md` 초안 |
| 5 | gws Calendar 실습 | 8분 | 일정 생성 명령 또는 payload |
| 6 | gws Drive 실습 | 8분 | 업로드 명령 또는 payload |
| 7 | 통합 프롬프트 작성 | 6분 | agent 작업 요청 프롬프트 |
| 8 | 회고 | 3분 | 안전 체크리스트 |

## 참가자 준비물

- GitHub 계정과 테스트 저장소 접근 권한
- 로컬 Git 설치
- 실습용 issue 번호 1개
- Codex 또는 사용 중인 agent 실행 환경
- 선택: `gws` 설치와 Google 인증

## gws 사전 설치

```bash
npm install -g @googleworkspace/cli
gws auth setup
gws auth login --scopes drive,calendar
```

gws 인증이 어려운 참가자는 명령어와 payload 작성까지만 실습한다.

## 진행 원칙

- 설명은 각 실습 시작 전 1~2분으로 제한한다.
- 참가자는 각 단계에서 최소 하나의 결과물을 만든다.
- Git 실습은 commit 직전까지 진행하고, push/PR 생성은 팀 상황에 따라 선택한다.
- `git worktree`는 별도 작업 디렉터리가 필요한 상황에서 사용한다.
- `git sparse-checkout`은 큰 저장소에서 필요한 경로만 펼칠 때 사용한다.
- Calendar 생성, Drive 공유 권한 변경은 실행 전 사람이 확인한다.

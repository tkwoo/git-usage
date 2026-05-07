# 03. gws CLI로 Calendar와 Drive 자동화하기

## 목표

gws CLI로 PR 리뷰 일정과 코드 관련 산출물 업로드 명령을 만든다.

## 시간

16분

## gws 설치와 인증

```bash
npm install -g @googleworkspace/cli
gws auth setup
gws auth login --scopes drive,calendar
```

인증이 어려운 참가자는 실제 실행 대신 명령어와 JSON payload만 작성한다.

## 핵심 주의점

- `credentials.json`, `.env`, token은 Git에 commit하지 않는다.
- 처음에는 `drive,calendar`처럼 필요한 scope만 승인한다.
- Calendar 생성과 Drive 공유 권한 변경은 실행 전 사람이 확인한다.
- 공유 권한을 바꾸는 명령은 대상 파일 ID와 role을 확인한다.

## Calendar 실습

### 캘린더 목록 확인

```bash
gws calendar calendarList list
```

### 일정 생성 명령

```bash
gws calendar +insert \
  --summary "wel-bridge PR review" \
  --start "2026-05-07T19:00:00+09:00" \
  --end "2026-05-07T19:30:00+09:00" \
  --attendee teammate@example.com \
  --description "PR 리뷰 및 merge 기준 확인"
```

### API command 예시

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

## Drive 실습

### 최근 파일 확인

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

### 공유 권한 설정

```bash
gws drive permissions create \
  --params '{"fileId": "FILE_ID"}' \
  --json '{"type": "user", "role": "reader", "emailAddress": "teammate@example.com"}'
```

## Agent 요청 예시

```text
test-results/report.json을 Google Drive의 wel-bridge-review-assets 폴더에 업로드하고,
파일명을 20260507-pr23-test-report.json으로 바꿔줘.
업로드 후 teammate@example.com에게 reader 권한을 부여해줘.
실행 전에 대상 폴더 ID, 파일명, 권한 payload를 먼저 확인해줘.
```

## 산출물

- Calendar 일정 생성 명령 또는 payload
- Drive 업로드 명령 또는 payload
- 공유 권한 payload
- 실행 전 확인할 참석자, 파일 ID, role

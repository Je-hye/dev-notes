# WIZE — 구글폼 수강 신청 자동화 MVP

## Overview
- **목적:** 자연어로 자동화를 설정하면 구글폼 응답 → 시트 저장 → Gmail 발송까지 자동 실행
- **기간:** 2026.05.12 ~ 2026.05.18 (7일)
- **규모:** LikeLion 6기 팀 프로젝트
- **역할:** 백엔드 데모 구현 + 기능 1개 추가 (Claude 활용)
- **GitHub:** https://github.com/eunjii0722/2026_LikiLion_6
- **배포:** https://wize-mvp-production.up.railway.app

## Tech Stack
- **Frontend:** React 19, TypeScript, Vite 6, React Router 7, Tailwind CSS 4, Framer Motion
- **Backend:** Python 3.11, FastAPI, SQLite, Uvicorn
- **AI:** Anthropic Claude API (Tool Use)
- **Google APIs:** Sheets v4, Gmail, Drive v3 (Push Notification)
- **인프라:** Docker (multi-stage), Railway

## Architecture

자연어 입력 → `/parse` → Claude Tool Use → 워크플로우 JSON → 저장 + Drive Watch 등록 → 구글폼 응답 수신 → `/webhook/google` → 시트 저장 + Gmail 발송

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| AI 파싱 방식 | 일반 프롬프트 vs Tool Use | Tool Use | 아래 참고 |
| 실시간 감지 | Polling vs Drive Push Notification | Drive Push Notification | 서버 폴링 없이 이벤트 기반 수신 |
| 배포 | EC2 vs Railway | Railway | Dockerfile만으로 배포, 1인 MVP에 충분 |
| DB | PostgreSQL vs SQLite | SQLite | 워크플로우·실행 로그만 저장, 스케일 불필요 |

**Tool Use vs 일반 프롬프트**
일반 프롬프트로 JSON을 요청하면 Claude가 텍스트로 응답하는데, 항상 파싱 가능한 JSON이 오지 않는다 — 필드명 오타, 구조 누락, 마크다운 코드 블록 혼입 등. Tool Use는 `input_schema`로 JSON Schema를 선언하면 Claude가 해당 스키마를 만족하는 구조만 반환하도록 강제된다. 백엔드에서 별도 파싱·검증 없이 tool input을 그대로 워크플로우로 쓸 수 있었다.

**Docker 멀티스테이지 빌드**
React 앱은 빌드 시 Node.js가 필요하고, 실행 시에는 FastAPI(Python)만 있으면 된다. 멀티스테이지 빌드는 하나의 Dockerfile에 두 환경을 순서대로 정의한다 — 1스테이지(Node)에서 `npm run build`로 정적 파일을 생성하고, 2스테이지(Python)에서 그 결과물만 복사해 FastAPI로 서빙한다. 최종 이미지에 Node.js가 포함되지 않아 이미지 크기가 줄고, Railway에서 `docker build` 한 번으로 배포가 완결된다.

## Challenges

**Drive Push Notification 7일 만료**
- 원인: Google Drive Watch API는 최대 7일 유효 → 이후 webhook 수신 불가
- 해결: `/workflows/{id}/reactivate` 엔드포인트로 수동 갱신, README에 경고 문서화

**로컬 webhook 수신 (ngrok 의존)**
- 원인: Google이 보내는 Drive Push는 public URL이 필요한데, 로컬 개발 서버는 외부에서 접근 불가
- 해결: ngrok으로 임시 public URL 생성 후 `NGROK_URL` 환경변수로 주입, 배포 환경에서는 Railway URL로 대체

**이메일 중복 발송**
- 원인: Drive Push가 짧은 시간에 여러 번 트리거되는 경우가 있음
- 해결: webhook 핸들러에 30초 dedup 윈도우 적용, 워크플로우 생성 시 기존 watcher 비활성화

## 잘 된 것
- Claude Tool Use로 자연어 → 구조화 JSON 변환이 안정적으로 동작 — 필드 누락·타입 오류 없음
- Railway Dockerfile 배포 — 로컬과 운영 환경 차이 없음
- Google OAuth refresh token 방식으로 서버 재시작에도 인증 유지

## 다음엔 다르게 할 것
- Drive Watch 만료 자동 갱신 — cron job으로 7일마다 reactivate 호출
- 여러 사용자 계정 지원 — 현재 `DEMO_USER_ID` 고정
- Claude 파싱 실패(actions 빈 배열) 시 사용자에게 명확한 피드백 표시

## 남은 것 / 아쉬운 것
- 구글폼 실제 E2E 테스트가 덜 됨 (로컬에서 ngrok + 수동 webhook 트리거로만 확인)
- 팀 프로젝트였는데 내가 담당한 부분 외 전체 흐름을 깊이 파악하지 못함

# 일단 쉼 — 스트레스 태깅 기반 AI 스마트 스케줄러

## Overview
- **기간:** 2025.11 (1주)
- **규모:** 3인 팀 프로젝트
- **역할:** 기획 + FE
- **목적:** 일정의 심리적 부담을 수치화해 스트레스 과부하를 방지하는 스케줄러
- **GitHub:** https://github.com/getOffWork102/2025_AI_conic_Hackathon
- **시연:** https://youtu.be/af-y6OieZQU
- **수상:** 2025 AI-conic Hackathon 장려상

## Tech Stack
- **Frontend:** React.js, FullCalendar, Axios, Custom CSS
- **인증:** Google OAuth2
- **실시간 통신:** SSE (Server-Sent Events)

## Architecture
SPA 구조 — 로그인 → 홈 캘린더 → 일정 입력/수정 → 알림 확인

**SSE 실시간 알림:** `App.js` 최상단에서 전역 단일 렌더링 → 모든 페이지에서 알림 유지

**스트레스 통계 로직:**
- `getRealDaysCount`: 달력 뷰 타입별 실제 일수 정밀 계산 (월간 뷰의 표시 칸 수 ≠ 실제 일수)
- `fetchStressSummary`: 백엔드 데이터를 날짜별 map으로 변환 → 누적/최대/사용률 3가지 지표 산출
- 뷰 변경/날짜 이동 시 `HandleRangeChange` → React State 갱신 → 즉각 렌더링

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| 실시간 알림 방식 | Polling vs WebSocket vs SSE | SSE | 단방향(서버→클라이언트) 알림에 충분, 구현 단순, 1주 해커톤 일정에 적합 |
| OAuth 처리 위치 | 프론트 직접 처리 vs 백엔드 위임 | 백엔드 위임 | 클라이언트 시크릿 노출 방지, 쿠키 기반 세션 관리 일원화 |

## Challenges
**달력 월간 뷰의 일수 계산 오류**
- 원인: FullCalendar 월간 뷰는 6주(42칸) 고정 렌더링 → 단순 칸 수로 계산 시 실제 일수와 불일치
- 해결: 범위 중간 날짜로 현재 월 특정 → 해당 월의 실제 마지막 날 추출하는 로직 설계

## Learnings & Retrospective
- 1주 해커톤: 기획-설계-구현-발표 전 사이클을 빠르게 경험
- FullCalendar의 렌더링 방식과 실제 날짜 데이터의 차이를 구분해야 함
- SSE는 단방향 실시간 통신에서 WebSocket보다 훨씬 가볍고 충분한 선택지
- 스트레스가 삶의 회복 변곡점이 된 개인 경험에서 출발한 기획 — 동기가 명확할수록 설득력 있음

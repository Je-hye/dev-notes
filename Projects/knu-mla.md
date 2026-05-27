# KNU MLA — AI 기반 다국어 학습·생활 지원 챗봇

## Overview
- **기간:** 2025.12 ~ 2026.01 (4주)
- **규모:** 2인 팀 프로젝트
- **역할:** PM + BE 전담
- **목적:** 경북대 유학생 대상 대학 특화 다국어 AI 어시스턴트
- **GitHub:** https://github.com/EunHye-03/knu-mla
- **수상:** 2025학년도 CES 자율프로젝트 장려상

## Tech Stack
| 영역 | 기술 |
|------|------|
| Frontend | Next.js, TypeScript, Tailwind CSS, Context API |
| Backend | FastAPI, Python, Pydantic, SQLAlchemy |
| DB | PostgreSQL |
| AI/NLP | GPT-4o / GPT-4o-mini, Whisper API (STT) |
| 인증 | JWT, SMTP (이메일 인증) |
| 배포 | Vercel (FE), Render (BE) |
| 협업 | Git/GitHub (main/dev/feature 브랜치), Discord, Notion |

## Architecture
**시스템:** 프론트/백엔드 완전 분리 + 개발/배포 환경 분리

**경북대 특화 용어 설명 엔진 — 하이브리드 검색 아키텍처**
- 내부 용어 DB 우선 탐색 → 없을 때만 LLM 호출
- AI 추론 결과를 Upsert로 DB에 즉시 저장 (Self-Growing)

**로깅:** 미들웨어에서 모든 요청에 request_id + user_idx 주입, X-Request-ID 응답 헤더 포함

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| 채팅 로그 저장 시점 | 별도 저장 API 호출 vs 기능 API 내 통합 | 기능 API 내 통합 | 프론트 호출 부담 제거, 생성-저장 트랜잭션 단위 보장, UX 지연 방지 |
| 로그 추적 방식 | 서비스 계층별 개별 로그 vs 미들웨어 전역 주입 | 미들웨어 전역 주입 | 한정된 개발 시간 내 전체 흐름 추적 효율화 |
| 에러 핸들링 | 엔드포인트별 개별 처리 vs 전역 미들웨어 | 전역 미들웨어 | 공통 에러 응답 포맷 유지, 중복 제거, 유지보수성 향상 |
| 보안 토큰 저장 | 원문 저장 vs SHA-256 + Pepper 해시 | 해시 저장 | DB 유출 시에도 토큰 오용 방지 |

## Challenges
**1. 기능 API 호출 시 채팅 로그 미저장**
- 원인: 콘텐츠 생성 API와 저장 API가 분리되어 프론트에서 순차 2회 호출 필요
- 해결: 백엔드 서비스 레이어에서 저장 로직을 기능 API 내에 통합

**2. 타 사용자 데이터 접근 보안 취약점**
- 원인: 인증 로직이 auth/user API에만 집중, 채팅·메모·피드백 라우터에 누락
- 해결: 모든 개인 데이터 라우터에 `Depends(get_current_user)` 인증 주입

## Learnings & Retrospective
- FastAPI 기반 프로덕션 수준 백엔드 아키텍처 설계 경험
- 보안은 설계 단계에서 전체 라우터를 대상으로 점검해야 함 — 기능별 추가는 누락 위험
- PM 역할 병행: 일정 관리와 문서화가 협업 효율에 직결됨을 체감
- Self-Growing DB 패턴: AI 추론 결과를 바로 축적해 시스템이 스스로 고도화되는 설계

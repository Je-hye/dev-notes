# Hierar-Do — 계층형 AI Todo 서비스

## Overview
- **기간:** 2026.04 ~
- **규모:** 1인 프로젝트 (Claude와 협업)
- **역할:** 기획 + 전체 구현
- **목적:** 월간 목표를 자연어로 입력하면 AI가 주간 마일스톤 → 일일 할 일로 자동 분해
- **GitHub:** https://github.com/EunHye-03/Hierar-Do

## Tech Stack
| 영역 | 기술 |
|------|------|
| Backend | FastAPI, Python, SQLAlchemy, Pydantic |
| AI Pipeline | LangGraph, Claude API (claude-sonnet) |
| Database | PostgreSQL (SQLite for test) |
| Frontend | Next.js, TypeScript, Tailwind CSS |
| Infra | Docker, docker-compose |
| CI/CD | GitHub Actions (PR Agent — Gemini 기반) |
| Testing | pytest (10 tests: schedule 3 + goals 4 + todos 3) |

## Architecture
**LangGraph 4단계 파이프라인:**
```
raw_input → [parse_goal] → [decompose] → [schedule] → [store]
              Claude API     Claude API    Pure Python   DB Write
```

- LLM 호출은 parse/decompose 노드만 — schedule은 순수 Python으로 비용 효율화
- Semi-active 에이전트: AI가 제안 생성, 사용자가 승인/거절

**데이터 계층:** Goal(월) → Milestone(주, 4개) → Todo(일)

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| AI 파이프라인 방식 | 단일 LLM 호출 vs LangGraph 파이프라인 | LangGraph 파이프라인 | 노드별 역할 분리, 추후 멀티에이전트 확장 용이 |
| schedule 노드 구현 | LLM 호출 vs Pure Python | Pure Python | API 비용 절감, 가용 시간 계산은 결정론적 로직으로 충분 |
| map() vs submit() | concurrent.futures | submit() + as_completed() | 작업별 완료 순 즉시 처리, 파이프라인 회전율 극대화 |
| MVP 인증 | JWT 구현 vs 고정 user_id | 고정 user_id=1 | MVP 범위 집중, 인증은 이후 단계로 명시적 연기 |

## Challenges
**SQLite 호환성 이슈**
- PostgreSQL Enum 타입이 SQLite에서 동작하지 않아 테스트 실패
- 해결: SQLAlchemy `native_enum=False` 옵션 추가

**LLM 응답 JSON 파싱 불안정**
- Claude 응답에 마크다운 코드블록이 포함되어 파싱 실패
- 해결: JSON 추출 로직 강화 + 에러 핸들링 추가

## Learnings & Retrospective
- LangGraph 파이프라인 설계: 노드를 작게 유지하면 테스트와 디버깅이 훨씬 쉬워짐
- Subagent-driven development로 태스크별 독립 구현 → 스펙 컴플라이언스 리뷰 → 코드 품질 리뷰 사이클 경험
- LLM 응답은 항상 파싱 실패를 가정하고 방어적으로 처리해야 함
- PR Agent (Gemini 기반) CI 자동화: 코드 리뷰 자동화로 혼자 작업해도 품질 체크 가능

# Magic Academy — Multi-Agent 사회 시뮬레이션

## Overview
- **목적:** AI 학생들이 살아가는 마법학교에서 관계, 조직, 사건이 스스로 만들어지는 Multi-Agent 시뮬레이션
- **기간:** 2026.07 ~ 2026.09
- **규모:** 팀 프로젝트 (4인)
- **역할:** Tick Engine 설계·구현, Event Master Agent 구현
- **GitHub (구현):** https://github.com/magic-academy-ma/Magic-Academy
- **GitHub (목업):** https://github.com/Je-hye/magic-academy-mockup
- **Live Demo (목업):** https://magic-academy-ma.github.io/magic-academy-mockup/
- **Confluence:** https://jehye.atlassian.net/wiki/spaces/MA/overview

## Tech Stack
- **Backend:** Python, FastAPI, Uvicorn
- **Agent Runtime:** LangGraph
- **DB:** PostgreSQL + pgvector (벡터 검색 기반 Memory RAG)
- **Scheduler:** APScheduler (in-process)
- **Frontend:** React 19, TypeScript, Vite
- **LLM:** Claude Sonnet 4.6 (Event Master), Claude Haiku 4.5 (Agent Runtime)
- **실시간:** WebSocket (Outbox 패턴)
- **인프라:** Docker, Railway
- **CI:** GitHub Actions, CodeRabbit 자동 PR 리뷰

## Architecture

**시스템 의존성 방향 (단방향)**

```
Frontend (React)
    ↕ WebSocket /v1/ws/simulations/{simulation_id}
API Layer (FastAPI)
    ↓
Tick Engine (TickScheduler → TickOrchestrator → SimulationTickService)
    ↓
Event Master (Sonnet 4.6) → Magic Layer (Haiku 4.5)
    ↓
Agent Runtime batch (Haiku 4.5, LangGraph, asyncio.gather + Semaphore)
    ↓
Policy Engine → Conflict Resolver → Commit Service
    ↓
PostgreSQL + pgvector
```

**Tick 단위 시뮬레이션 구조**

| 항목 | 값 |
|------|-----|
| 1 Tick | 8분 (실제 시간) = 1블록 (시뮬레이션) |
| 1일 구조 | 3블록 (아침·오후·저녁) + 야간 대기 1 Tick |
| 생활 Agent | Student 5명 + Professor 1명 |
| 시스템 Agent | Event Master 1 + Magic Layer 1 |

**Agent 구성**

| Agent | 역할 | MBTI 슬롯 |
|-------|------|-----------|
| Student 4명 | 자율 행동 | ISTJ, ESTP, ENTJ, ESFJ |
| User Persona | 사용자가 선택·설정 | INFP (기본) |
| Professor | schedule/Event 조건부 실행 | - |

**한 Tick의 실행 순서**

```
[1] SimulationTickService — world state snapshot, schedule snapshot 준비
[2] Event Master         — 동적 일반 Event 생성 (Sonnet 4.6)
[3] Magic Layer          — 일반 Event 마법 세계관 변환 + 특수 사건 생성
[4] Agent Runtime batch  — preselected Agent 병렬 실행 (asyncio.gather + Semaphore)
[5] Policy Engine        — 정성적 Reaction signal → 수치 delta 변환
[6] Conflict Resolver    — 충돌·중복 효과 조정, metric clamp
[7] Commit Service       — PostgreSQL batch write (READ COMMITTED + fence_token)
[8] WebSocket Outbox     — 변경분(delta)만 프론트엔드 push
```

**핵심 설계 결정**

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| Agent 행동 결정 방식 | 완전 규칙 기반 / LLM 전담 / 혼합 | LLM 행동 결정 + 규칙 기반 수치 계산 | 서사적 다양성 + 수치 안정성 동시 확보 |
| Agent 런타임 프레임워크 | 직접 구현 vs LangGraph | LangGraph | 상태·전환·조건 분기 그래프로 선언, 복잡한 흐름 관리 |
| DB | MySQL vs PostgreSQL | PostgreSQL + pgvector | Memory RAG용 벡터 검색 내장 |
| Tick 동시성 제어 | 낙관적 잠금 vs DB lease | Simulation별 PostgreSQL lease + fence_token | 동일 Simulation에 running Tick 최대 1개 보장 |
| Event 효과 처리 | LLM이 수치까지 결정 vs 분리 | LLM은 정성적 signal, Policy Engine이 수치 계산 | LLM 임의 수치 생성 방지, 중복 효과 제거 가능 |
| Memory 검색 | 전체 로드 vs RAG | 최신 2개 + pgvector RAG top-3 | Agent당 최대 5개로 제한, 프롬프트 비용 제어 |
| 배포 | EC2 vs Railway | Railway | Dockerfile만으로 배포, Docker 멀티스테이지 |
| WebSocket 신뢰성 | 직접 발행 vs Outbox | Transactional Outbox | Commit 실패 Tick의 메시지 발행 원천 차단 |

## 구현 범위 (Slice 1-7 완료)

| Slice | 내용 |
|-------|------|
| 1 | Agent Runtime 기본 파이프라인, Student 1명 + 조건부 Professor |
| 2 | Policy Engine, Agent 관계·상태·방향성 관계 변화 |
| 3 | Memory 서비스, pgvector RAG, Inspector API |
| 4 | 6-Agent 통합, User Persona 구현 |
| 5 | Event Master, Magic Layer, 캠페인 (Tick 10 통합) |
| 6 | 설정 저장·Snapshot·Replay·시점 복원·분기 트리 |
| 7 | 설정 공유·가져오기·Railway 배포 |

## 담당한 것

**Tick Engine 설계 및 구현 (전담)**
- TickScheduler: APScheduler lifecycle, Simulation별 job 등록·중단, 8분 주기 Trigger
- TickOrchestrator: 활동·야간 분기, 전체 파이프라인 호출 순서, 실패 처리, Commit 후 발행 조율
- SimulationTickService: world state snapshot, AcademicScheduleSnapshot, valid target 범위 준비
- Simulation별 PostgreSQL lease + fence_token 동시성 제어 설계
- Tick 격리 수준 (Snapshot: REPEATABLE READ, Commit: READ COMMITTED + FOR UPDATE) 확정

**Event Master Agent 구현**
- Sonnet 4.6 단일 LLM 호출로 동적 일반 Event (GROUP_PROJECT, MEETING 등) 생성
- AgentSummary·RelationshipSummary·AcademicScheduleSnapshot 기반 참여자·장소·영향도 결정
- CLASS·EXAM 등 예정 Event는 AcademicScheduleSnapshot에서 별도 활성화

## Challenges

**Tick 동시성 — 중첩 실행 차단**
- 자동 Scheduler Trigger가 실행 중인 Tick과 경합할 때 중복 실행 방지 필요
- `simulation_ticks` 테이블에 `status='running'` partial unique index + PostgreSQL lease + fence_token 조합으로 해결
- 만료된 실행은 `stale` 처리, Commit Service가 fence_token을 최종 검증해 지연 Commit 차단

**WebSocket 신뢰성 — 커밋 실패 메시지 차단**
- Commit 실패했는데 WebSocket 메시지는 이미 발행된 경우를 막아야 함
- Transactional Outbox로 도메인 변경과 발행 이벤트를 같은 transaction에 저장 → Commit 성공 후에만 dispatch

**Tick 구조화 응답 검증 안정화** (PR #235)
- Tick 루프 중 LLM 응답 파싱 실패가 전체 Tick 실패로 전파되는 문제
- Agent 단위 fallback(`WAIT` 반환)으로 격리, Tick은 계속 진행

**Runtime 일정·위치·관계 상태 정합화** (PR #236)
- Agent 상태와 실제 DB 저장값 사이 불일치
- snapshot 준비 단계와 Commit 경계 책임을 명확히 분리해 해결

## 잘 된 것

- Tick Engine 설계 문서(Confluence)를 먼저 확정하고 구현하니 팀원 간 책임 경계가 명확했음
- Agent Runtime이 DB write 권한을 갖지 않는 구조 덕분에 Tick 단위 rollback이 깔끔함
- PostgreSQL lease + fence_token 설계가 분산 환경 없이도 동시성 문제를 해결
- Outbox 패턴으로 WebSocket 신뢰성을 애플리케이션 레벨에서 보장
- Confluence ADR을 팀 공용으로 관리하니 나중에 결정을 추적하기 쉬웠음

## 다음엔 다르게 할 것

- Tick 전체 자동 재시도 정책을 초기에 확정하지 못해 후반에 수정 비용 발생 → 재시도 정책은 설계 초기에 ADR로 확정
- APScheduler in-process 구조는 서버 재시작 시 진행 중인 Tick이 stale 처리됨 → 스케일아웃이 필요하면 별도 큐(Celery 등)로 분리 검토
- LLM 모델 선택(Sonnet vs Haiku)을 Slice 중반에 결정해서 초기 테스트 비용이 높았음

## Mockup (설계 산출물)

UI/UX 기준점으로 사용한 15개 화면 — 빌드 없이 GitHub Pages로 공유

| 화면 | 핵심 기능 |
|------|----------|
| Design System | 컬러 팔레트, 타이포그래피, 모션 토큰 |
| Simulation Main | 월드맵 관찰, 에이전트 목록, 사건 알림 |
| Inspector Drawer | 6단계 인과 추적 시각화 |
| Relationship Graph | 에이전트 간 신뢰도·갈등 노드 그래프 |
| Timeline Replay | 타임라인 스크러버, 배속, 사건 핀 |
| Branch Tree | What-If 시나리오 분기 트리 |
| Magic Comparison | Magic ON vs OFF Side-by-Side 비교 |

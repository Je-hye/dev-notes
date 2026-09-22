# Magic Academy — Multi-Agent 사회 시뮬레이션

## Overview
- **목적:** AI 학생들이 살아가는 마법학교에서 관계, 조직, 사건이 스스로 만들어지는 Multi-Agent 시뮬레이션
- **기간:** 2026.07 ~ 진행 중
- **규모:** 팀 프로젝트
- **역할:** 백엔드 + 시뮬레이션 엔진 설계 및 구현
- **GitHub (구현):** https://github.com/magic-academy-ma/Magic-Academy
- **GitHub (목업):** https://github.com/Je-hye/magic-academy-mockup
- **Live Demo (목업):** https://magic-academy-ma.github.io/magic-academy-mockup/

## Tech Stack
- **Backend:** Python, FastAPI, Uvicorn
- **Simulation:** LangGraph (Agent 런타임 예정)
- **DB:** PostgreSQL + pgvector (예정)
- **Frontend:** React 19, TypeScript, Vite
- **인프라:** Docker Compose (예정)
- **CI:** GitHub Actions, CodeRabbit (자동 PR 리뷰)

## Architecture

**백엔드 모듈 구조 (도메인 중심)**

```
backend/app/
├── api/          # HTTP 엔드포인트
├── core/         # 설정, 공통 유틸
├── domain/       # 도메인 모델 (Student, Event 등)
├── repositories/ # DB 접근 레이어
├── services/     # 비즈니스 로직
└── simulation/
    ├── agent_runtime.py    # 에이전트 행동 실행
    ├── tick_engine.py      # 틱 단위 시뮬레이션 진행
    ├── event_master.py     # 사건 생성 및 관리
    └── magic_layer.py      # Magic ON/OFF 레이어
```

**시뮬레이션 핵심 개념**

- **Tick:** 시뮬레이션 시간 단위 — 각 틱마다 에이전트 행동 → 사건 → 관계 변화 순으로 처리
- **Magic Layer:** 마법 이벤트를 현실 인과 추적과 분리해 ON/OFF 비교 가능하게 구성
- **인과 추적 6단계:** Condition → Candidate → Policy → Conflict Resolution → Commit → Runtime Effect

**기술 결정**

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| Agent 런타임 | 직접 구현 vs LangGraph | LangGraph | 상태·전환·조건 분기를 그래프로 선언, 복잡한 에이전트 흐름 관리 용이 |
| DB | MySQL vs PostgreSQL | PostgreSQL + pgvector | 벡터 유사도 검색 내장, 관계 임베딩 저장 예정 |
| 초기 Agent 수 | 5 vs 8~10 | 초기 5 / MVP 8~10 | 너무 많은 에이전트는 초기 디버깅을 어렵게 함 |
| 목업 방식 | Figma vs 정적 HTML | 정적 HTML | 인터랙션 시연이 필요했고 빌드 없이 공유 가능 |

## Mockup (설계 산출물)

총 15개 화면 완성 — 실제 구현 전 UI/UX 기준점으로 사용

| 화면 | 핵심 기능 |
|------|----------|
| Design System | 컬러 팔레트, 타이포그래피, 모션 토큰 |
| Simulation Main | 월드맵 관찰, 에이전트 목록, 사건 알림 |
| Inspector Drawer | 6단계 인과 추적 시각화 |
| Relationship Graph | 에이전트 간 신뢰도·갈등 노드 그래프 |
| Timeline Replay | 타임라인 스크러버, 배속, 사건 핀 |
| Branch Tree | What-If 시나리오 분기 트리 (Git Graph 형태) |
| Magic Comparison | Magic ON vs OFF Side-by-Side 비교 |

## 현재 상태 (Week 3)

**완료**
- FastAPI + React 기본 폴더 구조 및 서버 실행 확인 (`GET /health`, React dev server)
- 더미 학생 Agent 목록 API (`GET /students` — 20명, 주인공 강조 표시)
- GitHub Actions CI, CodeRabbit 자동 PR 리뷰, 이슈·PR 템플릿 설정

**남은 것**
- LangGraph 기반 Agent 런타임 연결
- Tick 엔진 실제 구현 (현재 파일 구조만 있음)
- Magic Layer 로직 구현
- PostgreSQL + pgvector 연결
- Docker Compose 구성

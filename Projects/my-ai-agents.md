# my-ai-agents — Personal AI Developer Agents

## Overview
- **목적:** 반복되는 개발·학습 작업 자동화
- **성격:** 완결된 프로젝트가 아닌 지속적으로 확장 중인 개인 도구 모음
- **GitHub:** https://github.com/EunHye-03/my-ai-agents

---

## Tech Stack
- **플랫폼:** Claude Code (서브에이전트 + 스킬)
- **에이전트 형식:** `.claude/agents/<name>.md` — YAML frontmatter + 시스템 프롬프트
- **스킬 관리:** `~/.agents/skills/` (별도 저장소 `my-agents`)
- **문서:** `docs/superpowers/` — 설계 문서, 구현 플랜, pre-mortem 리포트

---

## Agents

| 에이전트 | 역할 | 주요 통합 |
|----------|------|-----------|
| `blog-writer` | PAAR 구조 기술 블로그 작성 | Tistory, GitHub, Obsidian |
| `weekly-report` | 연구실 주간보고서 작성 | Notion, 로컬 저장 |
| `schedule-briefer` | 일간 브리핑 (캘린더·Notion·Gmail·Slack·학교 공지) | Slack (`--slack` 플래그로 전송) |
| `project-notes` | 개발 마일스톤 문서화 | `~/Notes/Projects/`, dev-notes |

---

## Architecture

**설계 원칙**
- 에이전트는 단일 책임 — 하나의 반복 작업만 담당
- 스킬은 에이전트 간 공유 가능한 단위로 분리 (`korean-polishing` 등)
- 설계 결정은 `docs/superpowers/specs/`, 구현 플랜은 `plans/`, pre-mortem은 `pre-mortem/`에 기록

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| 스킬 저장 위치 | 레포 내 vs 별도 저장소 | 별도 저장소 (`my-agents`) | 여러 프로젝트에서 공유, 독립적 버전 관리 |
| 에이전트 포맷 | Python 스크립트 vs 마크다운 | 마크다운 (`agents/*.md`) | Claude Code 네이티브 포맷, 별도 런타임 불필요 |
| 문서화 방식 | CLAUDE.md 규칙 vs 에이전트 | 에이전트 (`project-notes`) | 개발과 병렬 실행 가능, CLAUDE.md 비대화 방지 |

---

## Learnings

- 에이전트 프롬프트는 짧을수록 동작이 예측 가능해짐
- pre-mortem을 설계 단계에서 쓰는 것이 구현 후 발견하는 것보다 비용이 훨씬 낮음
- 스킬(`korean-polishing`)을 여러 에이전트가 공유하니 문체 일관성이 자연스럽게 유지됨
- 통합 범위가 넓은 에이전트(schedule-briefer)는 외부 서비스 연동이 병목 — 작게 시작해야 함
- CLAUDE.md에 규칙으로 넣는 것보다 에이전트로 빼는 게 나음 — 규칙은 잊히지만 에이전트는 호출 시점이 명확함

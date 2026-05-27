# Claude Skills

상황에 맞게 자동 발동됨. 직접 호출 시 `/스킬명` 사용.

---

## Workflow (superpowers)

| 스킬 | 발동 조건 |
|------|----------|
| `superpowers:brainstorming` | 기능/컴포넌트 만들기 전 |
| `superpowers:writing-plans` | 다단계 작업 계획 전 |
| `superpowers:executing-plans` | 작성된 계획 실행 시 |
| `superpowers:test-driven-development` | 기능/버그픽스 구현 시 |
| `superpowers:systematic-debugging` | 버그/테스트 실패 시 |
| `superpowers:verification-before-completion` | 작업 완료 주장 전 |
| `superpowers:requesting-code-review` | 코드 리뷰 요청 시 |
| `superpowers:receiving-code-review` | 코드 리뷰 피드백 받을 때 |
| `superpowers:finishing-a-development-branch` | 구현 완료 후 머지/PR 전 |
| `superpowers:dispatching-parallel-agents` | 독립적인 작업 2개 이상 병렬 처리 시 |
| `superpowers:subagent-driven-development` | 구현 계획 병렬 실행 시 |
| `superpowers:using-git-worktrees` | 격리된 작업공간 필요 시 |
| `superpowers:writing-skills` | 새 스킬 만들거나 수정 시 |

---

## Development (addyosmani)

| 스킬 | 발동 조건 |
|------|----------|
| `spec-driven-development` | 새 기능 스펙 작성 시 |
| `planning-and-task-breakdown` | 작업 분해/계획 시 |
| `incremental-implementation` | 단계적 구현 시 |
| `test-driven-development` | 로직 구현/버그 수정 시 |
| `debugging-and-error-recovery` | 에러/버그 발생 시 |
| `code-review-and-quality` | 코드 리뷰 (5축: 정확성/가독성/아키텍처/보안/성능) |
| `code-simplification` | 리팩토링/복잡도 줄이기 시 |
| `api-and-interface-design` | API 설계 시 |
| `frontend-ui-engineering` | UI/프론트엔드 작업 시 |
| `security-and-hardening` | 보안 강화 작업 시 |
| `performance-optimization` | 성능 최적화 시 |
| `browser-testing-with-devtools` | 브라우저 테스트 시 |
| `git-workflow-and-versioning` | Git 워크플로우 시 |
| `ci-cd-and-automation` | CI/CD 설정 시 |
| `shipping-and-launch` | 배포 시 |
| `documentation-and-adrs` | 문서화/설계 결정 기록 시 |
| `deprecation-and-migration` | 마이그레이션/deprecated 처리 시 |
| `idea-refine` | 아이디어 구체화 시 |
| `interview-me` | 요구사항 인터뷰 시 |
| `doubt-driven-development` | 불확실한 부분 검토 시 |
| `source-driven-development` | 소스 기반 개발 시 |
| `context-engineering` | 컨텍스트 관리 시 |

| `adversarial-reviewer` | PR 머지 전, 보안 민감한 코드 리뷰 시 (Saboteur / New Hire / Security Auditor 3개 페르소나) |
| `pre-mortem` | 계획/설계 확정 전, 실패 시나리오 사전 분석 시 (6+ 페르소나 시뮬레이션) |
| `the-fool` | 아이디어/계획/결정 도전 시 (5가지 모드: Socratic/Dialectic/Pre-mortem/Red Team/Falsification) |
| `logic-review` | 계획·설계·제안의 논리 정합성 검토 시 (내부 일관성/가정 감사/맥락-논리 정렬/반박 탐색) |

---

## Domain (기술별)

| 스킬 | 발동 조건 |
|------|----------|
| `claude-api` | Anthropic SDK 사용 시 |
| `fastapi-expert` | FastAPI 작업 시 |
| `oauth` | OAuth 설정 시 |

---

## Utilities

| 스킬 | 발동 조건 |
|------|----------|
| `simplify` | 변경된 코드 품질 검토 시 |
| `find-skills` | 새 스킬 찾을 때 |
| `init` | CLAUDE.md 초기화 시 |
| `review` | PR 리뷰 시 |
| `security-review` | 현재 브랜치 보안 검토 시 |
| `update-config` | Claude Code 설정 변경 시 |
| `keybindings-help` | 키보드 단축키 설정 시 |
| `fewer-permission-prompts` | 권한 요청 줄이기 시 |
| `loop` | 반복 작업 스케줄링 시 |
| `schedule` | 원격 에이전트 스케줄링 시 |

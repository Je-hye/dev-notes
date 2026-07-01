# Troubleshooting

## [Open WebUI] 파일 업로드 후 모델이 PDF 내용을 읽지 못함
**상황:** disasterTV 프로젝트 — Open WebUI(latest main, 2026-07-01) 에서 PDF 파일만 먼저 업로드 시 모델이 "PDF를 분석할 수 없다"고 응답. 로그 상 파일은 RAG 벡터DB에 93개 청크로 처리됨은 확인.
**원인:** 확인 필요 — RAG 청킹이 원인인지, 업그레이드로 인한 변경인지 미검증
**해결:** 프롬프트 전송 시 PDF 파일을 매번 함께 첨부하면 정상 응답. 파일 단독 사전 업로드 시 재현됨

## [DB] test_db.py DB 연결 실패
**상황:** DB 연결 테스트 중
**원인:** 비밀번호가 str 타입이 아님
**해결:** DB 연결 설정에서 비밀번호를 문자열로 명시적 변환

---

## [Python] VS Code Python 버전 불일치
**상황:** VS Code에서 .py 실행 시 터미널 결과와 다른 결과가 나옴
**원인:** VS Code가 사용하는 Python과 터미널 Python이 다름 (예: 3.14.0 vs 3.10.11)
**해결:** VS Code에서 인터프리터 경로 직접 지정

**문법/임포트 확인 명령어:**
```bash
python3 -c "import sys; sys.path.append('.'); import step4_evaluate; print('Syntax and Imports OK')"
```

---

## [Spring] 로컬 백엔드 서버 실행 방법
**상황:** Spring Boot 프로젝트 로컬 환경 세팅 시
**원인:** application.yaml 없이 실행 시 서버 구동 안됨
**해결:**
1. `/workeBackEnd/main/resources/`에 `application.yaml` 파일 추가
2. VS Code에서 `workeBackEnd/WorkeBackEndApplication.java` 실행

> ※ `application.yaml`은 Git에 절대 올리지 않기

---

## [Python] backend/app/agent/embeddings.py IndentationError: unexpected indent
**상황:** `test_todos_api.py` 실행 시 백엔드 초기화 과정에서 `embeddings.py`를 임포트하던 중 `IndentationError` 발생
**원인:** `store_goal_embedding` 함수 내부의 `async with AsyncSessionLocal() as db:` 세션 블록 인덴트가 중간에 끊기고, 이후 DB 저장 관련 로직에서 들여쓰기(indentation)가 불일치하여 발생
**해결:** `store_goal_embedding` 함수의 모든 DB 관련 비즈니스 로직을 `async with` 세션 블록 하위로 올바르게 들여쓰기하여 감싸도록 수정

---

## [Codex MCP] PyCharm MCP 서버 연결 실패
**상황:** Codex 시작 후 MCP 도구 목록 로딩 중 `pycharm` 서버가 응답하지 않음
**원인:** `/Users/User/.codex/config.toml`에 `http://127.0.0.1:64342/stream` PyCharm MCP 서버가 등록되어 있었지만 해당 포트에 리스닝 프로세스가 없었음
**해결:** 사용하지 않는 `pycharm` MCP 서버 등록을 `.codex/config.toml`에서 제거

---

## [Antigravity] CLI 로그인 및 MCP 설정 오류
**상황:** `agy models`와 Antigravity CLI 실행 시 로그인되지 않았다는 오류와 MCP JSON 파싱 오류가 발생
**원인:** Antigravity keyring 인증이 완료되지 않았고 `~/.gemini/config/mcp_config.json` 등 MCP 설정 파일 3개가 빈 파일이었음
**해결:** Antigravity 대화형 CLI로 Google 계정 로그인을 완료하고 빈 MCP 설정 파일을 유효한 `{}` JSON으로 수정한 뒤, `agy models`와 실제 모델 요청 성공을 확인

---

## [Docker Compose] 정규화된 네트워크 출력 형식 불일치
**상황:** eunhye-wiki의 Public/Private 네트워크 격리 회귀 테스트 실행 중 실패
**원인:** `docker compose config --format json`이 서비스 네트워크를 배열이 아닌 객체로 정규화했지만 테스트가 배열을 가정함
**해결:** 정규화된 네트워크 객체의 키를 집합으로 변환해 기대한 네트워크 이름과 비교하도록 수정

---

## [Next.js] Next 및 PostCSS 취약점 감사 실패
**상황:** eunhye-wiki 웹 의존성 잠금 파일 생성 후 `npm audit`에서 high 및 moderate 취약점 발견
**원인:** `next@15.5.7`의 알려진 취약점과 Next가 의존하는 `postcss<8.5.10`의 XSS 취약점
**해결:** Next를 `15.5.19`로 올리고 npm override로 `postcss@8.5.10`을 고정해 `npm audit` 0건을 확인

---

## [Docker] Next.js 이미지 빌드 시 public 디렉터리 누락
**상황:** eunhye-wiki 웹 Docker 이미지의 런타임 스테이지에서 `/app/public` 복사 실패
**원인:** 정적 자산이 없어 `public` 디렉터리가 생성되지 않았지만 Dockerfile이 해당 경로가 항상 존재한다고 가정함
**해결:** 빌드 스테이지에서 `public` 디렉터리를 보장하고 `.dockerignore`로 `node_modules`와 `.next`를 제외한 뒤 전체 이미지 빌드 성공을 확인

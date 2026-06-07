# Troubleshooting

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

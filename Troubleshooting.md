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

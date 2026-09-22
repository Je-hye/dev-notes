# Python 명령어

**가상환경 (venv)**
```bash
python -m venv venv             # 가상환경 생성
source venv/bin/activate        # 활성화 (macOS/Linux)
venv\Scripts\activate           # 활성화 (Windows)
deactivate                      # 비활성화
```

**패키지 관리 (pip)**
```bash
pip install <패키지>             # 패키지 설치
pip install <패키지>==1.2.3      # 버전 지정 설치
pip install -r requirements.txt  # requirements 일괄 설치
pip uninstall <패키지>           # 패키지 제거
pip list                         # 설치된 패키지 목록
pip freeze > requirements.txt    # requirements 내보내기
pip show <패키지>                # 패키지 정보 확인
pip install --upgrade <패키지>   # 패키지 업그레이드
```

**실행**
```bash
python <파일>.py                 # 스크립트 실행
python -m <모듈>                 # 모듈 실행
python -c "print('hello')"       # 한 줄 실행
python -i <파일>.py              # 실행 후 인터랙티브 모드
```

**디버깅**
```bash
python -m pdb <파일>.py         # pdb 디버거 실행
python -m pytest                # pytest 실행
python -m pytest -v             # 자세한 출력
python -m pytest -k <테스트명>  # 특정 테스트만
python -m pytest --tb=short     # 짧은 traceback
```

**타입 검사 / 린트**
```bash
mypy <파일>.py                  # 타입 검사
ruff check .                    # 린트
ruff format .                   # 포맷
black .                         # 포맷 (black)
```

**성능 / 프로파일링**
```bash
python -m cProfile <파일>.py    # 성능 프로파일링
python -m timeit "<코드>"        # 실행 시간 측정
```

**패키지 빌드**
```bash
python -m build                 # 패키지 빌드
pip install -e .                # 개발 모드로 설치 (editable)
```

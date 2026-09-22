# Jupyter Notebook 단축키

**모드 전환**
- 편집 모드 진입: `Enter`
- 명령 모드 진입: `Esc`

**명령 모드 (파란 테두리)**
- Markdown 셀로 변경: `M`
- Code 셀로 변경: `Y`
- 셀 실행 후 아래 이동: `Shift+Enter`
- 셀 실행 (이동 없음): `Ctrl+Enter`
- 위에 셀 삽입: `A`
- 아래에 셀 삽입: `B`
- 셀 삭제: `D` → `D`
- 셀 복사: `C`
- 셀 붙여넣기: `V`
- 실행 중단: `I` → `I`
- 커널 재시작: `0` → `0`
- 모든 셀 실행: `Ctrl+F9`

**편집 모드 (초록 테두리)**
- 자동완성: `Tab`
- 함수 도움말 (docstring): `Shift+Tab`
- 주석 토글: `Ctrl+/`
- 들여쓰기: `Tab`
- 내어쓰기: `Shift+Tab`

**서버 실행**
```bash
jupyter notebook                        # 현재 디렉터리에서 실행
jupyter notebook --port 8888            # 포트 지정
jupyter lab                             # JupyterLab 실행
jupyter notebook --no-browser           # 브라우저 자동 실행 없이
jupyter notebook list                   # 실행 중인 서버 목록
jupyter notebook stop 8888              # 서버 중지
```

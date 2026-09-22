# Claude Code CLI 명령어

**세션**
```bash
claude                          # 새 세션 시작
claude -c                       # 마지막 세션 이어서 시작 (--continue)
claude -r                       # 세션 목록에서 선택해서 재개 (--resume)
claude "질문 또는 명령"          # 단발성 명령 실행
```

**모델**
```bash
claude --model claude-opus-4-8  # 모델 지정
claude --model claude-sonnet-4-6
claude --model claude-haiku-4-5-20251001
```

**출력**
```bash
claude -p "프롬프트"             # --print: 출력만 하고 종료
claude --output-format json     # JSON으로 출력
claude --output-format stream-json
```

**권한**
```bash
claude --dangerously-skip-permissions   # 모든 권한 자동 허용 (CI 환경용)
claude --allowedTools "Bash,Read,Write" # 허용할 도구 지정
```

**슬래시 커맨드**
```
/help           도움말
/clear          대화 초기화
/compact        컨텍스트 압축
/config         설정 변경
/cost           현재 세션 비용 확인
/doctor         환경 진단
/bug            버그 리포트
/memory         메모리 파일 열기
/vim            Vim 입력 모드 토글
/model          모델 변경
/fast           Fast 모드 토글 (Opus 고속)
```

**MCP**
```bash
claude mcp add <서버명> <명령어>    # MCP 서버 등록
claude mcp list                    # 등록된 MCP 서버 목록
claude mcp remove <서버명>          # MCP 서버 제거
```

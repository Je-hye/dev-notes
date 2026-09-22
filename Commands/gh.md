# GitHub CLI (gh) 명령어

**인증**
```bash
gh auth login                   # GitHub 로그인
gh auth status                  # 인증 상태 확인
gh auth logout                  # 로그아웃
```

**저장소**
```bash
gh repo create                  # 저장소 생성 (대화형)
gh repo clone <owner>/<repo>    # 저장소 클론
gh repo view                    # 현재 저장소 정보
gh repo list                    # 내 저장소 목록
gh repo fork <owner>/<repo>     # 저장소 포크
```

**PR (Pull Request)**
```bash
gh pr create                    # PR 생성 (대화형)
gh pr create --title "제목" --body "내용"
gh pr list                      # PR 목록
gh pr view <번호>               # PR 상세 보기
gh pr checkout <번호>           # PR 브랜치 체크아웃
gh pr merge <번호>              # PR 병합
gh pr close <번호>              # PR 닫기
gh pr diff <번호>               # PR diff 보기
gh pr review --approve          # PR 승인
gh pr review --request-changes --body "내용"
```

**Issue**
```bash
gh issue create                 # 이슈 생성 (대화형)
gh issue create --title "제목" --body "내용"
gh issue list                   # 이슈 목록
gh issue view <번호>            # 이슈 상세 보기
gh issue close <번호>           # 이슈 닫기
gh issue comment <번호> --body "내용"
```

**Workflow (Actions)**
```bash
gh workflow list                # 워크플로우 목록
gh run list                     # 실행 목록
gh run view <run-id>            # 실행 상세
gh run watch                    # 실행 중인 워크플로우 모니터링
gh workflow run <파일명>         # 워크플로우 수동 실행
```

**API**
```bash
gh api <경로>                   # GitHub API 직접 호출
gh api repos/<owner>/<repo>/issues --jq '.[].title'
```

**기타**
```bash
gh browse                       # 현재 저장소를 브라우저로 열기
gh browse --issues              # 이슈 페이지 열기
gh gist create <파일>           # Gist 생성
gh release list                 # 릴리즈 목록
gh release create <태그>        # 릴리즈 생성
```

# Git 명령어

**연결**
```bash
git init
git remote add origin {HTTPS 주소}
git remote -v
```

**Pull**
```bash
git pull origin main
git pull origin main --allow-unrelated-histories   # 원격 + 내 파일 유지 및 merge
```

**Staging**
```bash
git status
git add .
git add <파일명>
git rm --cached <파일명>   # add 취소
```

**Commit**
```bash
git commit -m "메시지"
git commit --amend -m "수정된 메시지"
git log
git log --oneline
```

**Push**
```bash
git push -u origin main
```

**충돌 시**
```bash
git stash
git pull origin main
git stash pop
```

**Merge**
```bash
git add .
git commit -m "feat: something"
git push origin feature/a

git switch develop
git pull origin develop
git switch feature/b
git merge develop
```

**Branch**
```bash
git checkout -b [브랜치이름]   # 생성 + 이동
git switch -c [브랜치이름]     # 생성 + 이동
git branch [브랜치이름]        # 생성
git branch                     # 브랜치 확인
git branch -M main             # 브랜치명 변경
```

**기타**
```bash
git diff --cached --stat       # add된 변경사항 파일목록 및 수정 라인 수
conda env export --no-builds > environment.yml   # GitHub Action 환경 설정
python3 -m pip install ~~~
```

**초기 세팅 시 브랜치 오류 해결**
```bash
git branch -M main
git push -u origin main
```

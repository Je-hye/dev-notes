# npm / Node.js 명령어

**프로젝트 초기화**
```bash
npm init                        # 대화형 초기화
npm init -y                     # 기본값으로 초기화
```

**패키지 관리**
```bash
npm install                     # package.json 기준 설치
npm install <패키지>             # 패키지 설치 (dependencies)
npm install -D <패키지>         # 개발 의존성 설치 (devDependencies)
npm install -g <패키지>         # 전역 설치
npm uninstall <패키지>           # 패키지 제거
npm update                      # 패키지 업데이트
npm list                        # 설치된 패키지 목록
npm list -g                     # 전역 설치 목록
npm outdated                    # 업데이트 가능한 패키지 확인
```

**스크립트**
```bash
npm run <스크립트>               # package.json 스크립트 실행
npm run dev                     # 개발 서버 시작
npm run build                   # 프로덕션 빌드
npm run test                    # 테스트 실행
npm start                       # start 스크립트 실행
```

**감사 / 보안**
```bash
npm audit                       # 취약점 감사
npm audit fix                   # 취약점 자동 수정
npm audit fix --force           # 강제 수정 (major 버전 업 포함)
```

**캐시 / 환경**
```bash
npm cache clean --force         # 캐시 초기화
npm config list                 # 설정 목록
node -v                         # Node.js 버전 확인
npm -v                          # npm 버전 확인
```

**npx**
```bash
npx <패키지> [args]             # 설치 없이 실행
npx create-react-app <앱이름>
npx create-next-app@latest <앱이름>
```

**nvm (Node 버전 관리)**
```bash
nvm install <버전>              # Node.js 버전 설치
nvm use <버전>                  # 버전 전환
nvm ls                          # 설치된 버전 목록
nvm alias default <버전>        # 기본 버전 설정
```

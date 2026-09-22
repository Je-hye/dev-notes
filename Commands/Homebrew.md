# Homebrew / iTerm2 명령어

**패키지 (formula) 관리**
```bash
brew install <패키지>           # 패키지 설치
brew uninstall <패키지>         # 패키지 제거
brew upgrade <패키지>           # 특정 패키지 업그레이드
brew upgrade                    # 설치된 모든 패키지 업그레이드
brew update                     # 패키지 목록 갱신 (brew 자체 업데이트)
brew list                       # 설치된 패키지 목록
brew info <패키지>              # 패키지 정보 확인
brew search <키워드>            # 패키지 검색
brew outdated                   # 업데이트 가능한 패키지 확인
brew pin <패키지>               # 자동 업그레이드에서 제외
brew unpin <패키지>             # 고정 해제
```

**Cask (GUI 앱) 관리**
```bash
brew install --cask <앱>        # GUI 앱 설치
brew uninstall --cask <앱>      # GUI 앱 제거
brew upgrade --cask <앱>        # 특정 앱 업그레이드
brew upgrade --cask             # 설치된 모든 Cask 앱 업그레이드
brew list --cask                # 설치된 Cask 앱 목록
brew upgrade --cask iterm2      # iTerm2 업데이트
```

**정리**
```bash
brew cleanup                    # 불필요한 구버전 파일 정리
brew cleanup -n                 # 정리 대상 목록만 확인 (실제 삭제 안 함)
brew autoremove                 # 의존성 없는 고아 패키지 제거
```

**진단**
```bash
brew doctor                     # 환경 문제 진단
brew missing                    # 누락된 의존성 확인
brew deps <패키지>              # 패키지 의존성 트리
brew uses --installed <패키지>  # 해당 패키지를 사용하는 패키지 목록
```

**Tap (서드파티 저장소)**
```bash
brew tap <owner>/<repo>         # Tap 추가
brew untap <owner>/<repo>       # Tap 제거
brew tap                        # 등록된 Tap 목록
```

**서비스**
```bash
brew services list              # 서비스 목록 및 상태
brew services start <서비스>    # 서비스 시작
brew services stop <서비스>     # 서비스 중지
brew services restart <서비스>  # 서비스 재시작
```

# WSL 명령어

**설치 및 버전**
```bash
wsl --install                   # WSL2 + Ubuntu 설치
wsl --list --verbose            # 설치된 배포판 목록
wsl --set-default-version 2     # WSL2를 기본값으로
wsl --update                    # WSL 커널 업데이트
```

**시작 / 종료**
```bash
wsl                             # 기본 배포판 시작
wsl -d Ubuntu                   # 특정 배포판으로 시작
wsl --shutdown                  # 모든 WSL 인스턴스 종료
wsl --terminate Ubuntu          # 특정 배포판 종료
```

**패키지 관리 (Ubuntu)**
```bash
sudo apt update                 # 패키지 목록 갱신
sudo apt upgrade                # 설치된 패키지 업그레이드
sudo apt install <패키지>       # 패키지 설치
sudo apt remove <패키지>        # 패키지 제거
```

**파일 시스템 접근**
```bash
# Windows에서 Linux 파일 접근
\\wsl$\Ubuntu\home\<user>

# Linux에서 Windows 드라이브 접근
cd /mnt/c/Users/<user>
```

**네트워크**
```bash
cat /etc/resolv.conf            # WSL의 DNS 및 호스트 IP 확인
hostname -I                     # WSL IP 주소 확인
```

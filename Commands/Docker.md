# Docker 명령어

**이미지**
```bash
docker images                           # 이미지 목록
docker pull <이미지>:<태그>              # 이미지 다운로드
docker build -t <이름>:<태그> .         # 이미지 빌드
docker rmi <이미지>                     # 이미지 삭제
docker image prune                      # 미사용 이미지 정리
```

**컨테이너**
```bash
docker ps                               # 실행 중인 컨테이너 목록
docker ps -a                            # 전체 컨테이너 목록
docker run -it <이미지> bash            # 컨테이너 실행 (인터랙티브)
docker run -d -p 8000:8000 <이미지>    # 백그라운드 실행 + 포트 바인딩
docker run --rm <이미지>               # 실행 후 자동 삭제
docker stop <컨테이너>                  # 컨테이너 중지
docker start <컨테이너>                 # 컨테이너 시작
docker rm <컨테이너>                    # 컨테이너 삭제
docker logs <컨테이너>                  # 로그 확인
docker logs -f <컨테이너>              # 로그 실시간 스트림
docker exec -it <컨테이너> bash        # 실행 중인 컨테이너에 접속
```

**Docker Compose**
```bash
docker compose up                       # 서비스 시작
docker compose up -d                    # 백그라운드 시작
docker compose up --build               # 빌드 후 시작
docker compose down                     # 서비스 중지 및 컨테이너 제거
docker compose down -v                  # 볼륨까지 제거
docker compose logs -f                  # 로그 스트림
docker compose ps                       # 서비스 상태
docker compose exec <서비스> bash       # 서비스 컨테이너에 접속
docker compose build                    # 이미지만 빌드
docker compose restart <서비스>         # 특정 서비스 재시작
```

**볼륨 / 네트워크**
```bash
docker volume ls                        # 볼륨 목록
docker volume rm <볼륨>                 # 볼륨 삭제
docker network ls                       # 네트워크 목록
docker inspect <컨테이너>               # 컨테이너 상세 정보
```

**정리**
```bash
docker system prune                     # 미사용 리소스 일괄 정리
docker system prune -a                  # 이미지까지 모두 정리
docker system df                        # 디스크 사용량 확인
```

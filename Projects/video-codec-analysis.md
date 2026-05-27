# Video Codec Analysis & Visualization Pipeline

## Overview
- **기간:** 2026.02 (4주)
- **규모:** 1인 프로젝트
- **목적:** VCM ROI 기반 영상 데이터의 HEVC/VVC 압축 성능 분석 자동화
- **GitHub:** https://github.com/EunHye-03/video-codec-analysis

## Tech Stack
- **Language:** Python
- **핵심 라이브러리:** NumPy, Pandas, concurrent.futures
- **인프라:** FFmpeg, VTM (VVC Test Model), WSL2, tmux, nohup
- **분석 지표:** BD-Rate, PSNR, Bitrate

## Architecture
단방향 파이프라인 구조 (Input → Analysis → Processing → Evaluation → Output)

- **Input Layer:** 원본 영상 + ROI 데이터
- **Analysis Step:** 영상 복잡도 분석 → Meta_df 생성
- **Processing Layer:** Priority Scheduler → Parallel Encoder (HEVC/VVC)
- **Evaluation Layer:** 데이터 무결성 검증 + PSNR/Bitrate 산출
- **Output Layer:** 분석 리포트 + 시각화

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| 병렬 처리 방식 | ThreadPoolExecutor vs ProcessPoolExecutor | ProcessPoolExecutor | Python GIL 제약 우회, CPU 멀티코어 실질 활용 |
| Future 처리 방식 | map() vs submit() + as_completed() | submit() + as_completed() | 영상별 압축 시간 편차가 커 map()은 병목 발생, 완료 순 즉시 처리로 파이프라인 회전율 극대화 |
| 압축 우선순위 가중치 | 다양한 지표 조합 | Temporal(0.5) > Edge(0.4) > Pixel(0.1) | 비디오 인코딩 난이도의 지배 요소는 프레임 간 중복성(Temporal redundancy) |
| 데이터 관리 | 매번 원본 조회 vs Meta_df 구조 | Meta_df | 핵심 컬럼만 추출해 메모리 최적화, 조회 속도 개선 |

## Challenges
**로컬 하드웨어 한계 (RAM 8GB, 8 Cores)로 인한 프로세스 강제 종료**
- `nohup` + `tmux`로 백그라운드 세션 유지
- `htop` 모니터링 → FFmpeg `-threads 1` 제한으로 컨텍스트 스위칭 억제
- 근본 해결: 지도교수 협의 후 고사양 인프라로 이전

## Learnings & Retrospective
- OS 이론(CPU 스케줄링, 프로세스 vs 스레드)을 실제 코드에 적용하는 경험
- 소프트웨어 레벨 최적화의 한계와 인프라 판단 시점을 배움
- 연구 R&D 프로세스 — 가설 수립 → 실험 → 기록의 흐름 체득

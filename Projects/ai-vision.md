# AI Vision Gesture Events

## Overview
- **목적:** 실시간 영상 스트림에서 손 제스처를 감지하고 이벤트 로그를 브라우저 대시보드로 제공
- **GitHub:** https://github.com/EunHye-03/AI-vision

## Tech Stack
- **Language:** Python
- **Framework:** FastAPI
- **영상 처리:** OpenCV (RTSP / HLS / HTTP 스트림 지원)
- **AI:** Roboflow Hosted API (객체 감지)
- **로그:** JSONL 이벤트 로그 + 스냅샷 저장

## Architecture

**파이프라인**

영상 스트림 샘플링 → Roboflow API 감지 → 제스처 이벤트 변환 → JSONL 로그 저장 → 브라우저 대시보드 표시

**설정 파라미터**

| 파라미터 | 설명 |
|----------|------|
| `confidence_threshold` | 감지 결과 신뢰도 하한 |
| `sample_fps` | 영상에서 추출할 초당 프레임 수 |
| `consecutive_frames` | 이벤트로 인정하기 위한 연속 감지 프레임 수 |
| `cooldown` | 같은 이벤트 연속 발생 방지 대기 시간 (초) |
| `target_classes` | 감지할 제스처 클래스 목록 |

**기술 결정**

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| AI 추론 방식 | 로컬 모델 vs Hosted API | Roboflow Hosted API | 모델 학습 환경 없이 빠르게 프로토타이핑 가능 |
| 스트림 처리 | 단일 소스 vs 다중 소스 통일 | 통일 인터페이스 | RTSP/HLS/HTTP를 추상화해 입력 교체 용이 |
| 로그 형식 | CSV vs JSONL | JSONL | 스냅샷 경로, 메타데이터 등 구조화 데이터에 적합 |

## Challenges

**다중 스트림 소스 통일 인터페이스**
- RTSP/HLS/HTTP 각각 OpenCV `VideoCapture` 동작 방식이 다름 (재연결 처리, 버퍼링)
- URL 스킴으로 소스 유형을 감지해 동일한 샘플링 루프에서 처리하는 추상화 레이어 작성

**이벤트 중복 발생 방지**
- 같은 제스처가 연속 프레임에서 계속 감지되면 이벤트가 폭증
- `consecutive_frames` 카운터 + `cooldown` 타이머 조합으로 실질적 이벤트만 기록

## 잘 된 것

- Roboflow Hosted API 덕분에 모델 학습 없이 객체 감지 파이프라인을 빠르게 구성
- `consecutive_frames` + `cooldown` 조합이 이벤트 중복 문제를 단순하면서도 효과적으로 해결
- JSONL + 스냅샷 저장 구조 덕분에 이벤트 재현 및 디버깅이 편함

## 다음엔 다르게 할 것

- 로컬 ONNX 모델로 교체해 네트워크 지연과 API 비용 제거
- Drive Push Notification 만료처럼 Watch 갱신이 필요한 외부 의존성 자동화
- 대시보드에 실시간 스트림 미리보기 추가 (현재는 이벤트 로그만 표시)

## Learnings

- Roboflow Hosted API 연동 및 실시간 스트림 처리 경험
- FastAPI 기반 REST API + 대시보드 동시 제공 구조
- 영상 파이프라인에서 이벤트 품질을 높이려면 신뢰도 임계값보다 연속 프레임 조건이 더 유효함

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
**파이프라인:** 영상 스트림 샘플링 → Roboflow API 감지 → 제스처 이벤트 변환 → 브라우저 대시보드 표시

**설정 파라미터:** confidence threshold, sample FPS, consecutive-frame trigger, cooldown, target classes

## Challenges
- RTSP/HLS/HTTP 다양한 스트림 소스를 통일된 인터페이스로 처리

## Learnings & Retrospective
- Roboflow Hosted API 연동 및 실시간 스트림 처리 경험
- FastAPI 기반 REST API + 대시보드 동시 제공 구조

# LLM-to-Notes — Chrome Extension

## Overview
- **목적:** ChatGPT / Gemini 대화를 원클릭으로 Notion 데이터베이스에 자동 백업
- **GitHub:** https://github.com/EunHye-03/LLM-to-Notes

## Tech Stack
- **Language:** JavaScript (Vanilla JS)
- **플랫폼:** Chrome Extension
- **API:** Notion API (v2022-06-28)
- **CI/CD:** GitHub Actions (Push 시 아티팩트 자동 생성)

## Architecture
**흐름:** 대화창 버튼 클릭 → 현재 세션 파싱 → Notion API 호출 → DB 저장

**저장 항목:** 대화 제목(Name), 출처(Source), 원본 URL

**지원 플랫폼:** ChatGPT, Gemini

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| 빌드 자동화 | 수동 빌드 vs GitHub Actions | GitHub Actions | Push마다 아티팩트 자동 생성, 배포 일관성 확보 |

## Challenges
- ChatGPT / Gemini 각각의 DOM 구조가 달라 플랫폼별 파싱 로직 분리 필요

## Learnings & Retrospective
- Chrome Extension 구조 (manifest, content script, popup) 처음 습득
- AI와의 대화를 지식 자산으로 체계화하려는 실제 필요에서 출발한 개인 도구
- GitHub Actions를 통한 CI/CD 파이프라인 첫 경험

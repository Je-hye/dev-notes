# SkillSeeker (MindView) — 면접 기록 및 AI 피드백 서비스 FE

## Overview
- **목적:** 면접 직후 2분 복기 → 자동 피드백·미션으로 다음 행동 1개를 고정하는 면접 학습 플랫폼
- **기간:** 2026.02.06 ~ 2026.02.07 (무박 2일 해커톤)
- **규모:** 3인 팀 (FE 2, BE 1)
- **역할:** FE — Company 페이지, MyPage 구현, PPT 제작
- **GitHub:** https://github.com/skillseeker-team/skillseeker-fe

## Tech Stack
- **Frontend:** React, Vite, react-router-dom, react-hook-form, lucide-react, Custom CSS
- **AI 활용:** Gemini CLI (개발 보조), Vinsign (디자인 생성 + 코드 구현)
- **Backend (팀원):** Spring Boot, PostgreSQL (Supabase), Spring Security

## 기여 내역
- Company 페이지, MyPage 구현 (통계·멘탈케어 — mock 데이터)
- 프로필 이미지 버튼, 레이아웃 수정
- PPT 제작

## Learnings
- **PRD의 힘** — `spec/REQUIREMENTS.md`에 User Journey, 기능 요구사항, 수용 기준, 데이터 모델까지 미리 정리한 덕분에 무박 2일에도 팀이 방향을 잃지 않았음. 짧은 일정일수록 PRD가 먼저 있어야 한다.
- **AI-native 개발 첫 경험** — Gemini CLI를 코딩 보조로 처음 써봄. 단순히 코드 생성 도구가 아니라 개발 흐름 자체를 AI 중심으로 설계하는 것의 차이를 느낌.
- **Vinsign → 코드** — AI로 디자인 생성 후 코드로 바로 추출하는 흐름이 빠른 프로토타이핑에 유효함.

## 남은 것 / 아쉬운 것
- MyPage 통계(빈출 실수·멘탈케어 효과)가 mock 데이터로 고정 — 시간 부족으로 API 연동 못함
- AI 인사이트 생성(`POST /mypage/insight`) 미구현
- 무박 2일이라 테스트 전혀 없음

# LLM-to-Notes — Chrome Extension

## Overview
- **목적:** ChatGPT / Gemini 대화를 원클릭으로 Notion 데이터베이스에 자동 백업
- **배경:** AI와의 대화를 지식 자산으로 체계화하려는 실제 필요에서 출발한 개인 도구
- **규모:** 개인 프로젝트, 본인만 사용
- **상태:** 2026-06-06 아카이브 — AI 도구를 직접 활용하는 방식으로 전환하여 필요성 소멸
- **GitHub:** https://github.com/EunHye-03/LLM-to-Notes

---

## Tech Stack
- **Language:** JavaScript (Vanilla JS)
- **플랫폼:** Chrome Extension (Manifest V3)
- **API:** Notion API (v2022-06-28)
- **CI/CD:** GitHub Actions (Push 시 설치 가능한 .zip 아티팩트 자동 생성)

---

## Architecture

**전체 흐름:**
```
사용자 클릭
  → content.js: DOM 파싱 → HTML-to-Markdown 변환
  → chrome.runtime.sendMessage (SAVE_TO_NOTION)
  → background.js (service worker): Notion API POST /pages
  → Notion 데이터베이스에 페이지 생성
```

**저장 항목:** 대화 제목(Name), 출처(Source: ChatGPT/Gemini), 원본 URL

**플랫폼 분기:** `window.location.hostname`으로 ChatGPT / Gemini 구분 후 각각 다른 DOM 셀렉터 적용

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| API 호출 위치 | content script vs background service worker | background.js | content script에서 직접 외부 API 호출 시 CORS 문제 발생 |
| 빌드 자동화 | 수동 빌드 vs GitHub Actions | GitHub Actions | Push마다 아티팩트 자동 생성, 배포 일관성 확보 |
| 버튼 주입 방식 | 정적 삽입 vs MutationObserver | MutationObserver | ChatGPT·Gemini는 SPA라 초기 DOM에 body가 없는 경우가 있어 동적 감지 필요 |
| 자격증명 저장 | localStorage vs chrome.storage.sync | chrome.storage.sync | 크롬 계정 간 동기화, Extension API와의 자연스러운 통합 |

---

## Challenges

**1. ChatGPT / Gemini DOM 구조 차이**
두 플랫폼의 HTML 구조가 완전히 달라 플랫폼별 파싱 로직을 분리해야 했음.
ChatGPT는 `[data-message-author-role]` + `.markdown`, Gemini는 `user-query` / `model-response` 커스텀 엘리먼트.

**2. HTML → Markdown 직접 구현**
Notion API는 plain text 또는 블록 구조를 요구하므로, 코드 블록·인라인 코드·리스트·볼드 등을 직접 파싱하는 `htmlToMarkdown()` 함수를 작성했음.

**3. Notion API 2000자 제한**
Notion `rich_text` 블록 하나에 최대 2000자 제한이 있어, 긴 텍스트를 1900자 단위로 청킹하는 `chunkText()` 로직이 필요했음.

**4. 크롬에서 확장 프로그램이 계속 사라지는 문제**
웹스토어 미등록 + 개발자 모드(Load unpacked) 상태에서 크롬 정책 업데이트 또는 재시작 시 강제 비활성화됨.
→ 결국 해결하지 않은 채로 프로젝트 종료.

---

## Learnings & Retrospective

**배운 것**
- Chrome Extension 구조(manifest, content script, popup, service worker)를 처음부터 구현하며 MV3 아키텍처 습득
- content script ↔ background service worker 간 메시지 패싱 패턴 (`chrome.runtime.sendMessage` + `return true`)
- GitHub Actions를 통한 CI/CD 파이프라인 첫 경험
- SPA 특성에 맞는 DOM 조작 (MutationObserver 패턴)

**아쉬운 점 / 다음에 다르게 할 것**
- 웹스토어에 등록하지 않아 "사라지는 문제"가 구조적으로 해결되지 않았음 — 본인만 쓰더라도 비공개 등록이 나았을 것
- 중복 URL에 대한 PATCH 로직을 끝내 구현하지 못함 — 같은 대화를 두 번 저장하면 중복 페이지가 생겼음
- AI 도구가 발전하면서 "대화를 Notion에 수동으로 저장"하는 니즈 자체가 없어짐 → 도구가 문제를 해소해버린 케이스

**종료 이유**
AI 도구를 직접 활용하는 방식이 발전하면서 대화를 별도로 노션에 백업할 필요성이 사라짐. 유지보수 부담 대비 실사용이 거의 없어 2026-06-06 아카이브 처리.

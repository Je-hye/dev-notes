# math-handwriting-solver — 수학 손글씨 풀이 생성기

## Overview
- **목적:** 수학 문제 사진·PDF를 입력받아 사람이 직접 손으로 푼 것처럼 보이는 풀이를 원본 이미지에 오버레이해서 반환하는 CLI 도구
- **기간:** 2026.06
- **규모:** 개인 프로젝트
- **GitHub:** https://github.com/Je-hye/math-handwriting-solver

## Tech Stack
- **Language:** Python 3.11+
- **AI:** Claude Vision API (문제 인식), Claude API (풀이 생성)
- **렌더링:** Pillow + NanumPenScript 폰트 + jitter 효과
- **PDF:** PyMuPDF
- **수치 검증:** sympy
- **패키징:** hatchling (`math-solver` CLI 엔트리포인트)

## Architecture

**파이프라인 (6단계)**

```
입력 파일 (JPG/PNG/PDF)
  ↓
[1] parse_input       — 파일 형식 감지, DPI 정규화, 이미지 추출
  ↓
[2] extract_problem   — Claude Vision으로 문제 텍스트·수식·그래프 영역 파악 → ProblemData
  ↓
[3] generate_solution — Claude로 단계별 풀이 + annotation 목록 생성 → SolutionData
  ↓
[4] layout_annotations — annotation 위치 계산 (72dpi 기준 → 실제 DPI 변환)
  ↓
[5] render_overlay    — PIL로 원본 이미지에 손글씨 annotation 렌더링
  ↓
[6] save_output       — 입력과 같은 형식으로 새 파일 저장 (원본 보존)
```

**핵심 데이터 모델**

| 모델 | 역할 |
|------|------|
| `ProblemData` | 문제 텍스트, problem_type, 그래프·도형 영역(bbox), 72dpi 정규화 이미지 |
| `SolutionData` | 풀이 단계, 최종 답, annotation 목록, confidence, sympy 검증 여부 |
| `Annotation` | type(text·circle·arrow 등), 위치, 색상(blue·red·green·orange·purple) |

**색상 규칙 (annotation)**

| 색상 | 용도 |
|------|------|
| blue | 계산 과정, 수식 변환 |
| red | 강조, 동그라미 |
| green | 정답 체크마크 |
| orange | 좌표·수치 라벨 |
| purple | 풀이 하단 개념 설명 박스 (항상 포함) |

**기술 결정**

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| 손글씨 느낌 구현 | 실제 손글씨 모델 vs 폰트+jitter | NanumPenScript + position jitter | 모델 없이 "선생님이 써준 것 같은" 느낌 충분히 구현 가능 |
| 수치 검증 | LLM 결과 신뢰 vs 별도 검증 | sympy 검증 후 confidence 반영 | 풀이 틀려도 오버레이는 생성, confidence로 신뢰도 표시 |
| 입력 DPI 처리 | 원본 DPI 유지 vs 정규화 | 72dpi 기준 정규화 | Vision API 입력 비용 절감, 좌표 계산 단순화 |
| 원본 파일 처리 | 덮어쓰기 vs 새 파일 | 새 파일 생성 (`_solved` 접미사) | 원본 보존 원칙 — 항상 새 파일 |

## Challenges

**annotation 위치 정합성**
- Claude가 반환한 좌표가 실제 이미지 DPI와 맞지 않아 오버레이 위치가 어긋나는 문제
- 모든 좌표를 72dpi 기준으로 정규화하고, render 직전에 실제 DPI로 변환하는 단계를 분리해 해결

**수식 superscript 렌더링**
- PIL 기본 폰트에서 `x²` 같은 위첨자가 깨지거나 평문으로 출력되는 문제 (PR fix: render superscripts manually)
- 수식 파싱 후 superscript를 수동 레이아웃으로 직접 렌더링

**LLM 응답 파싱 안정화**
- Claude가 JSON 외에 마크다운 코드블록이나 설명 텍스트를 함께 반환하는 경우 파싱 실패
- `strip_md_json` 유틸로 코드블록 제거 후 파싱, 실패 시 명시적 예외 발생

## 잘 된 것

- 6단계 파이프라인 분리 덕분에 각 단계를 독립적으로 테스트할 수 있었음
- sympy 검증 + confidence 필드 조합이 LLM 풀이 오류를 사용자에게 투명하게 전달
- NanumPenScript + jitter만으로 "손글씨" 느낌이 충분히 나옴 — 별도 모델 불필요
- 오픈소스 polish (README, LICENSE, CONTRIBUTING, AGENTS.md)까지 완료

## 다음엔 다르게 할 것

- annotation 위치 좌표를 LLM이 직접 결정하는 구조라 문제 크기·레이아웃이 달라지면 위치가 빗나감 → 위치 계산은 코드(rule-based)가, 내용만 LLM이 결정하도록 분리 검토
- sympy 검증 범위가 대수 방정식에 한정 — 기하·통계 문제는 confidence가 항상 낮게 나옴
- Phase 1은 단일 페이지 PDF만 지원 — 다중 페이지 PDF 처리 미구현

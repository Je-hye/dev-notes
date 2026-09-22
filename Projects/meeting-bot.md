# Craig 회의록 봇

## Overview
- **목적:** Craig 녹음 링크를 Discord 채팅에 붙여넣으면 Gemini AI가 다중 트랙 오디오를 분석해 회의록을 자동 생성
- **배경:** ENACTUS KNU PIC 3팀 회의록 작성 자동화 — 매 회의마다 수기 작성하던 부담을 제거
- **기간:** 2026.09
- **규모:** 1인 프로젝트
- **배포:** Fly.io (`meeting-bot-pic-3`, nrt 리전)

## Tech Stack
- **Runtime:** Python
- **Discord:** discord.py 2.3.2
- **AI:** Google Gemini API (`gemini-3.6-flash`, 멀티모달 오디오)
- **HTTP:** aiohttp
- **배포:** Fly.io (Docker, 512MB / 1 CPU)
- **테스트:** pytest, pytest-asyncio

## Architecture

**처리 파이프라인**

```
Craig 링크 감지
  → Craig Job API 폴링 (start → wait → complete)
  → ZIP 다운로드 (스트리밍, 5MB마다 Discord 진행 상황 갱신)
  → 오디오 파일 추출 (스트리밍 압축 해제, RAM 급증 방지)
  → Gemini 파일 업로드 (트랙별 병렬)
  → 회의록 생성 (System Prompt + 다중 트랙 동시 전달)
  → Discord #회의록 채널 전송 (2000자 초과 시 .md 파일 첨부)
```

**기술 결정**

| Decision | Options Considered | Choice | Reason |
|----------|--------------------|--------|--------|
| Craig 오디오 접근 | 직접 다운로드 vs Job API | Job API 폴링 | Craig는 비동기 처리 — Job이 없으면 생성, 있으면 완료 대기 |
| AI 모델 | Whisper + GPT vs Gemini | Gemini 멀티모달 | 오디오 파일을 직접 업로드해 화자별 트랙을 한 번에 분석 가능 |
| ZIP 압축 해제 | z.read() vs shutil.copyfileobj | 스트리밍(copyfileobj) | 대용량 ZIP을 메모리에 통째로 올리면 OOM 위험, 512MB 제한 환경 |
| 배포 | EC2 vs Fly.io | Fly.io | Dockerfile만으로 배포, 단일 Python 봇에 EC2는 과함 |
| 회의록 전송 | 텍스트 vs 파일 | 2000자 기준 분기 | Discord 메시지 길이 제한 대응 — 길면 `.md` 파일로 첨부 |

**Discord 커맨드**

| 커맨드 | 설명 |
|--------|------|
| (Craig 링크 붙여넣기) | 자동 감지 후 회의록 생성 |
| `!회의록` | 사용 안내 출력 |
| `!회의록수정 <message_id> <내용>` | 생성된 회의록 텍스트 수정 |
| `!회의록제목수정 <message_id> <제목>` | 첨부된 .md 파일의 H1 제목 수정 |

## Challenges

**Craig Job API 상태 처리**
- Craig는 녹음을 즉시 제공하지 않고 Job을 비동기 처리
- `job_exists(400)` 응답 시 새 Job 생성 없이 기존 Job 폴링으로 전환 — 중복 요청 방지
- 타임아웃(600초) 초과 시 사용자에게 실패 메시지 전송

**대용량 ZIP 메모리 관리**
- ZIP 내 오디오 파일을 `z.read()`로 한 번에 읽으면 Fly.io 512MB 환경에서 OOM 가능
- `z.open()` + `shutil.copyfileobj()`로 청크 스트리밍 추출로 해결

**System Prompt 설계**
- 팀 특화 정보(멤버 실명·Discord ID 매핑, ENACTUS 배경지식)를 System Prompt에 고정
- 녹음 속 발언을 배경 정보로 오인하거나 없는 결론을 추가하는 할루시네이션 방지 규칙 명시
- 음성 트랙 순서 ≠ 참석자 순서임을 명시해 화자 오배정 방지

## 잘 된 것

- Gemini 멀티모달 오디오 업로드가 화자별 트랙 분리를 별도 STT 없이 처리
- `job_exists` 분기로 이미 완료된 Job을 중복 생성 없이 재사용
- 진행 상황 Discord 메시지 실시간 갱신으로 처리 중 침묵 없음
- System Prompt에 팀 정보와 할루시네이션 방지 규칙을 함께 넣어 일관된 회의록 품질 유지

## 다음엔 다르게 할 것

- 팀 정보(멤버, 배경 지식)를 코드에 하드코딩 → 환경 변수 또는 설정 파일로 분리해 다른 팀도 사용 가능하게
- 회의록 생성 후 Notion DB 자동 저장 연동
- Fly.io 무료 플랜 sleep → 유료 전환 또는 keep-alive 설정 필요

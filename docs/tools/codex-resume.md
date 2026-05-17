# codex-resume — Codex CLI 세션 검색/재개 도구

> 코드 리포: https://github.com/ytkim4558/codex-resume

## 한 줄 요약

OpenAI Codex CLI 의 로컬 세션 로그를 직접 읽어 검색, preview, resume 를 빠르게
수행하는 Windows 친화형 CLI/picker.

## 만든 이유

Codex CLI 는 `codex resume` 으로 이전 세션을 이어갈 수 있지만, 세션이 많아지면
원하는 작업을 빠르게 찾기 어렵다. `codex-resume` 는 `~/.codex/sessions` 아래의
JSONL 로그를 직접 인덱싱해 제목, 작업 디렉터리, 최근 응답 preview 를 기준으로
세션을 찾고 `codex resume <session-id>` 로 넘긴다.

## 주요 기능

- `%USERPROFILE%\.codex\sessions` 재귀 스캔
- Codex JSONL 포맷 파싱 (`session_meta`, `user_message`, `response_item`, `event_msg`)
- 시스템성 bootstrap 메시지 제외 후 첫 실제 user 메시지로 제목 생성
- `doctor`, `list`, `list --json`, `index`, `resume <id>` 명령 제공
- 외부 npm 의존성 없는 `dist/main.js` 실행 파일 제공
- PowerShell/CMD 래퍼 제공

## 구현 포인트

- 세션 ID 는 `session_meta.payload.id` 를 우선 사용하고, 없으면 파일명 UUID suffix 를 fallback 으로 사용
- `updatedAt` 은 파일 mtime 대신 JSONL top-level timestamp 를 사용
- 캐시는 `%USERPROFILE%\.codex\codex-resume\session-index.json` 에 저장
- picker 는 Node.js 기본 `readline` keypress 이벤트로 구현

## 명령 예시

```powershell
node .\dist\main.js doctor
node .\dist\main.js list --limit 10
node .\dist\main.js list --query codex-resume --json
node .\dist\main.js index
node .\dist\main.js resume <session-id>
```

## claude-resume 와의 차이

`claude-resume` 는 Claude Code 의 기본 resume UX 를 Textual 기반 TUI 와 백그라운드
LLM 요약으로 보완한다. `codex-resume` 는 Codex CLI 의 native resume 명령을 그대로
사용하면서, 로컬 JSONL 인덱싱과 빠른 검색에 초점을 둔다.

## 포트폴리오 문장

OpenAI Codex CLI 세션을 검색하고 이어가기 위한 Windows 친화형 도구
`codex-resume` 를 구현했다. 로컬 JSONL 세션 로그를 직접 파싱해 작업 디렉터리,
첫 사용자 요청, 최근 응답 preview 를 기준으로 세션을 찾고, 선택한 항목을
`codex resume <session-id>` 로 재개하는 흐름을 구성했다.

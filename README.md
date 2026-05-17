# claude-toolkit

[Claude Code](https://claude.com/claude-code) 를 활용하면서 직접 만든 도구·스킬·
워크플로의 인덱스. 각 항목은 [Wiki](../../wiki) 에 상세 문서가 있고,
실제 코드가 있는 것은 별도 리포지토리로 분리되어 있다.

> 이 리포는 "지표"역할. 코드는 각 도구 리포에, 사용법/설계 결정은 `docs/` 에.

## 🧰 Tools (외부 도구로 동작)

| 이름 | 설명 | 코드 | 문서 |
|---|---|---|---|
| **[claude-resume](https://github.com/ytkim4558/claude-resume)** | Claude Code 세션 피커 (Textual TUI + 백그라운드 요약) | [repo](https://github.com/ytkim4558/claude-resume) | [Wiki](docs/tools/claude-resume.md) |
| **[codex-resume](https://github.com/ytkim4558/codex-resume)** | OpenAI Codex CLI 세션 검색/재개 도구 (Windows 친화형 picker) | [repo](https://github.com/ytkim4558/codex-resume) | [Wiki](docs/tools/codex-resume.md) |

## 🎯 Skills (`~/.claude/skills/` 에서 동작)

| 이름 | 설명 | 위치 | 문서 |
|---|---|---|---|
| **linkedin-update** | LinkedIn 포스트/프로필 섹션 초안 생성 | `~/.claude/skills/linkedin-update/` | [Wiki](docs/skills/linkedin-update.md) |
| **firefly-prompt** | Adobe Firefly 영문 프롬프트 + 사용 가이드 생성 | `~/.claude/skills/firefly-prompt/` | [Wiki](docs/skills/firefly-prompt.md) |
| **portfolio-update** | 포트폴리오를 자연어 한 문장으로 갱신 (양쪽 repo 자동 sync + push) | `~/.claude/skills/portfolio-update/` | [Wiki](docs/skills/portfolio-update.md) |

## 📝 워크플로 노트

특정 종류의 작업을 Claude Code 로 풀어낼 때의 패턴/체크리스트는 `docs/` 에 둔다.

- 도구별: `docs/tools/<name>.md`
- 스킬별: `docs/skills/<name>.md`

## 라이선스

각 도구의 코드는 해당 리포지토리의 LICENSE 를 따른다.
이 인덱스 리포의 문서/README 는 [MIT](LICENSE).

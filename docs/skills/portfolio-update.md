# portfolio-update — 포트폴리오 자동 갱신 스킬

> 위치: `~/.claude/skills/portfolio-update/SKILL.md`

## 한 줄 요약

"포트폴리오에 X 추가해", "재직중 표시 끝으로 바꿔" 같은 자연어 한 문장으로
GitHubPageMaker(Jekyll 소스) + ytkim4558.github.io(빌드 결과) 양쪽 repo 를
동시에 편집하고 자동 commit + push 까지 처리하는 스킬. 사용자는 GitHub 안
만져도 됨.

## 만든 배경

[ytkim4558.github.io/portfolio/](https://ytkim4558.github.io/portfolio/) 가
**몇 년간 업뎃 안 됐던 핵심 이유**가 워크플로 자체의 friction 이었다:

1. GitHubPageMaker 클론 → 마크다운 편집 → Jekyll 빌드 → 결과 HTML 을 다른
   repo(ytkim4558.github.io)에 복사 → 양쪽 commit + push
2. 단계가 많고 도구 셋업도 필요
3. = 어쩌다 한 번 큰맘 먹고 갱신, 그것도 귀찮아서 안 함

이 스킬은 **그 friction 을 자연어 1문장으로 압축**.

## 설계 결정

### 1. 소스 + 빌드 양쪽 동시 편집

이상적으로는 소스(md)만 편집 → Jekyll 로 빌드 → 빌드 결과 push 가 정석.
하지만 사용자 PC 에 Ruby/Jekyll 미설치 + 설치하면 또 friction 추가.

→ 스킬이 **양쪽 파일을 동시에 수동으로** 편집해서 두 repo 가 sync 상태 유지.
HTML 패턴은 Jekyll(kramdown) 이 생성하는 그대로 흉내.

drift 위험은 있지만 단순 콘텐츠 추가 수준에선 문제 없음. 나중에 Ruby 설치되면
자동 빌드로 전환 가능.

### 2. 묻기 최소화

사용자가 자연어로 던지면 빠진 정보만 1회 일괄 질문, 나머진 합리적 기본값.
"안 묻고 그냥 해주는 것" 이 핵심 가치.

### 3. ytkim4558(본명) 전용

LinkedIn 같은 본명 채널에 가는 이력서. yougif(필명) 콘텐츠는 별도 도구로
처리되어야 함 (`user-identity` 메모리 참조).

### 4. 라이브 URL + 캐시 안내

push 후 GitHub Pages CDN 캐시 때문에 1-2분 지연 있음. 사용자에게 안내해
"안 보이는데?" 혼란 방지.

## 사용 예시

```
> 포트폴리오에 회사 경력 추가:
> 회사: ABC Corp, 직책: Senior Backend Engineer
> 기간: 2024.03 ~ 2025.02
> 담당업무: 검색 시스템 백엔드, Elasticsearch 클러스터 운영
```

스킬이:
1. 섹션 2(경력요약) 에 짧은 요약 추가
2. 섹션 6(경력기술서) 에 `6.X ABC Corp` 상세 항목 추가
3. portfolio/index.md 와 portfolio/index.html 양쪽 동기화
4. 두 repo 각각 commit + push
5. https://ytkim4558.github.io/portfolio/#6x-abc-corp 라이브 URL 출력

다른 예시:
- "CJ 재직중 표시를 2025.06.30 로 변경" → 섹션 2 + 6.1 헤더 동시 수정
- "핵심역량에 Kubernetes 운영 경험 추가" → 섹션 4 에 불릿 1줄
- "자격증 정보처리기사 추가" → 섹션 8 에 한 줄

## 한계 + 향후 개선

- **이미지 첨부 자동화 미지원** — 스크린샷이 들어가는 프로젝트는 직접 복사
  필요 (assets/portfolio/ 에 두고 마크다운에서 참조)
- **HTML 미러 drift 위험** — 복잡한 마크다운(중첩 리스트 등)은 Jekyll 빌드와
  살짝 다를 수 있음. Ruby/Jekyll 설치되면 `bundle exec jekyll build` 로
  자동화 권장
- **변경 이력 추적 없음** — `~/.claude/drafts/portfolio/` 에 매 변경
  로그 남기는 기능 미구현 (향후)
- **LinkedIn PDF 자동 파싱 미지원** — 현재는 사용자가 텍스트로 paste

## 워크플로 효과

**Before** (몇 년간 정체):
- 회사 이직 → 포트폴리오 업뎃 시도 → GitHub clone/edit/build/push 단계 7-8개
  → 30분 이상 → 결국 안 함

**After** (이 스킬 도입):
- 회사 이직 → "포트폴리오에 X 회사 추가" 한 문장 → Claude Code 가 자동 처리
  → 2분 → 항상 최신

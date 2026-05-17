# firefly-prompt — Adobe Firefly 프롬프트 생성기

> 위치: `~/.claude/skills/firefly-prompt/SKILL.md`

## 한 줄 요약

한국어로 만들고 싶은 이미지를 설명하면 Adobe Firefly 가 잘 알아듣는 영문
프롬프트(주제 + 스타일 + 구도 + 조명 + 색감)로 변환하고, 어디서 생성할지 +
권장 비율 + 결과 안 좋을 때 조정 팁까지 출력하는 Claude Code 스킬.

## 만든 배경

Adobe Creative Cloud 구독에는 Firefly 가 포함돼있어 매월 ~1000 generative
credit 을 쓸 수 있지만, 두 가지 문제가 있다:
1. **API 가 없다** — 개인 CC 구독은 Firefly REST API 권한 미포함 (Enterprise
   라이선스만 가능). 자동화 워크플로에 못 끼움.
2. **프롬프트 노하우가 필요하다** — Firefly 는 자연어보다 명사구 + 스타일
   키워드 콤마 나열에서 결과가 훨씬 안정적인데, 처음 쓰면 매번 시행착오.

→ **자동 호출은 어차피 불가**하니, 프롬프트 노하우 부분만 LLM 으로 해결하는
스킬. linkedin-update 와 동일한 "준비물 차려서 손에 쥐어주는" 패턴.

## 설계 결정

### 1. Firefly 의 약점을 패턴화

- **부정 표현(negative prompt) 약함** → "~없이" 같은 표현을 양성 표현으로
  자동 변환 ("without text" → "abstract shapes only")
- **긴 문장 깨짐** → 30단어 이내 가이드라인
- **상표/실명 거부** → 일반 명사 자동 대체

### 2. 모드별 비율/도구 매핑

이미지 용도에 따라 Firefly 안에서도 적합한 진입점이 다름. 스킬이 자동으로
안내:

| 용도 | 비율 | 진입점 |
|---|---|---|
| 블로그/아티클 표지 | 16:9, 1792×1024 | Firefly Web — Text to Image |
| LinkedIn/SNS | 1:1 또는 1.91:1 | Firefly Web 또는 Express |
| 기술 일러스트 | 자유 | Firefly Web (반복 시드 제어) |
| 배경 텍스처 | 정사각 + Seamless | Firefly Web — Generate Pattern |
| 기존 이미지 부분 채우기 | 영역 선택 | Photoshop Generative Fill |

### 3. 드래프트 파일 보관

`~/.claude/drafts/firefly/YYYY-MM-DD-<slug>.md` 에 저장. 이유:
- 어떤 프롬프트로 어떤 이미지를 만들었는지 이력 추적
- 비슷한 주제 재작업 시 과거 프롬프트 참고
- 조정 팁 / 다음 변형 후보를 같이 메모

## 사용법

Claude Code 세션에서 자연어로:

```
> 블로그 표지로 쓸 미니멀한 디버깅 컨셉 이미지 만들고 싶어
```

스킬이 트리거되면서 묻는다:
1. mode (header / social / diagram / texture / freeform)
2. style (minimalist / isometric / line-art / ...)
3. mood (dark / light / vibrant / muted / ...)

응답으로 받는 것:
- 영문 프롬프트 (그대로 복사 가능)
- 권장 비율 + 어디서 만들지
- 조정 팁 2-3개
- 드래프트 파일 경로

## 다른 모델로 확장하기

현재는 Firefly 전용. 다음 후보들로 분기/확장 가능:

- **로컬 Stable Diffusion / Flux** (사용자 RTX 5090 환경에 적합) — ComfyUI
  API 연결 시 진짜 자동화(이미지 파일까지 받기) 가능. 별도 `local-image-gen`
  스킬로 신설 권장.
- **DALL-E 3 (ChatGPT Pro 내장)** — 사용자가 ChatGPT 에 직접 입력하는 수동
  워크플로용 프롬프트도 거의 호환됨 (DALL-E 는 자연어 친화라 살짝 풀어쓰기만
  하면 OK).

## ChatGPT Pro 와의 조합

사용자는 ChatGPT Pro 구독자. Firefly 와 DALL-E 양쪽 모두 활용 가능한
워크플로:

1. 이 스킬로 프롬프트 생성
2. **Firefly Web** 에 붙여 1차 결과 — Adobe 상업적 사용 보증
3. 마음에 안 들면 같은 프롬프트(살짝 풀어서) **ChatGPT DALL-E** 에 시도
4. 둘 중 좋은 거 채택, 본인 워크플로(블로그/LinkedIn)에 사용

## 향후 개선 후보

- [ ] **시리즈 일관성** — 같은 블로그의 여러 표지 만들 때 동일 스타일
      자동 유지 (스타일 시드/키워드 캐싱)
- [ ] **참조 이미지 모드** — 본인이 갖고 있는 이미지 스타일로 새 주제 생성
      (Firefly Web 의 Structure/Style Reference 활용 안내)
- [ ] **블로그/LinkedIn 글과 자동 연계** — `linkedin-update` 결과의 톤·주제를
      읽어서 어울리는 표지 프롬프트 자동 제안
- [ ] **로컬 OSS 분기** — ComfyUI 셋업된 환경에선 자동으로 그쪽 호출 옵션

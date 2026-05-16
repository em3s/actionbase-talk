# Actionbase Talk — 작업 가이드

발표 자료를 계속 만들어가는 작업 공간. 현재 `slides.md`는 시리즈 1/3 (본인 차례)로 완결 상태.

## 디렉토리 구조

- `slides.md`, `index.html`, `css/` — Reveal.js 슬라이드 (GitHub Pages)
- `docs/` — **계속 재사용하는 도구함**. git 추적.
  - `overview.md` — 시리즈 구성, 톤 원칙, 핵심 메시지
  - `narrative.md` — 서사 원칙
  - `design.md` — 디자인 시스템, 레이아웃 결정 로그
  - `references/` — 외부 자료 보존본 (verbatim). 원문이 사라져도 살아남도록.
- `.claude/` — gitignored. **특정 발표에만 종속된 임시 작업물**.
  - `talk1/` — 1세션 작업 메모 (script-draft, script-final 등)

## 작업 원칙

- **References는 verbatim으로**. 원문이 사라질 수 있으니 사본을 두고, 개인 정보·상태값은 제외.
- **화자 이름은 익명화** — 기존 commit `7db1708` 정책 따름 (`[발표자]` 등).
- **재사용 가능한 자산은 `docs/`**, 일회성 메모는 `.claude/talkN/`.
- 톤 원칙: 담담한 회고 · 진행형, 미완 인정 (자세한 건 `docs/overview.md`).

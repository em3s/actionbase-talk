# Actionbase Talk — 작업 가이드

발표 자료를 계속 만들어가는 작업 공간. 현재 `talks/talk1/slides.md`가 시리즈 1/3 (본인 차례)로 완결 상태.

## 디렉토리 구조

```
.
├── index.html              # 랜딩 페이지 (talks 목록)
├── css/                    # 공유 스타일
│   ├── theme.css           #   슬라이드용 디자인 시스템
│   └── landing.css         #   랜딩용 스타일
├── talks/talkN/            # 개별 발표 — 발표마다 완결된 set
│   ├── index.html          #   Reveal.js entry
│   ├── slides.md           #   슬라이드 본문 + speaker notes
│   ├── notes.md            #   발표 메타·톤·thesis 매핑
│   ├── narrative.md        #   서사 구조 (몇 막·핵심 메시지 등)
│   └── design.md           #   슬라이드 레이아웃·구성 결정
├── docs/                   # 재사용 도구함 — 모든 talk 공유
│   ├── overview.md         #   시리즈 구성·우산 모델·세션 간 경계
│   ├── design.md           #   디자인 시스템·빌드·단축키
│   └── references/         #   외부 자료 보존본 (verbatim)
├── .claude/                # 작업 메모 + harness 설정
│   ├── settings.json       #   Claude Code 권한 (tracked)
│   ├── settings.local.json #   개인 override (gitignored)
│   └── talkN/              #   발표별 script-draft, script-final 등
└── CLAUDE.md
```

## 자료 배치 원칙

- **`docs/`** — 여러 talk에 걸쳐 재사용되는 것. 시리즈 구조, 디자인 시스템, 외부 레퍼런스.
- **`talks/talkN/`** — 특정 발표 1개의 산출물 일체 (슬라이드 + 그 발표의 narrative·design·notes).
- **`.claude/talkN/`** — 그 발표를 만드는 과정의 작업 메모 (스크립트 드래프트 등).

새 talk 추가 시: `talks/talkN/` 폴더 만들고 위 파일들 채움. `.claude/talkN/`은 필요할 때 작성.

## 작업 원칙

- **References는 verbatim으로**. 원문이 사라질 수 있으니 사본을 두고, 개인 정보·상태값은 제외.
- **화자 이름은 익명화** — 기존 commit `7db1708` 정책 따름 (`[발표자]` 등).
- 발표 톤은 발표별로 정의 (`talks/talkN/notes.md`). 시리즈 전체 공통 원칙은 `docs/overview.md`.

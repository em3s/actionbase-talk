# actionbase-talk

액션베이스 발표 자료 모음. Reveal.js · GitHub Pages.

> 이 파일이 원장. `README.md`는 이 파일의 symlink.

URL: https://em3s.github.io/actionbase-talk/ (랜딩) · `/talks/talkN/` (각 발표).

## 디렉토리 구조

```
.
├── CLAUDE.md               # 원장 (이 문서)
├── README.md               # → CLAUDE.md symlink
├── index.html              # 랜딩 페이지 (talks 목록)
├── css/
│   ├── theme.css           #   슬라이드용 디자인 시스템
│   └── landing.css         #   랜딩용 스타일
├── talks/talkN/            # 개별 발표 — 발표마다 완결된 set
│   ├── index.html          #   Reveal.js entry
│   ├── slides.md           #   슬라이드 본문 + speaker notes (워크플로가 덮어씀)
│   ├── issue               #   해당 talk의 GitHub Issue 번호
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
└── .github/workflows/
    └── sync-slides.yml     # Issue → slides.md 수동 sync 워크플로
```

## 자료 배치 원칙

- **`docs/`** — 여러 talk에 걸쳐 재사용되는 것. 시리즈 구조, 디자인 시스템, 외부 레퍼런스.
- **`talks/talkN/`** — 특정 발표 1개의 산출물 일체 (슬라이드 + 그 발표의 narrative·design·notes).
- **`.claude/talkN/`** — 그 발표를 만드는 과정의 작업 메모 (스크립트 드래프트 등).

새 talk 추가:
1. `talks/talkN/` 폴더 + 기존 talk1 복사
2. GitHub Issue 만들고 번호를 `talks/talkN/issue` 에 commit
3. 랜딩 `index.html` 의 `<ul class="talks">` 에 `<li>` 항목 추가

## 슬라이드 편집

### Issue 편집 → 워크플로 동기화 (권장)

각 talk의 슬라이드 source는 GitHub Issue 본문. 매핑은 `talks/talkN/issue` 파일에 commit되어 있음.

1. https://github.com/em3s/actionbase-talk/issues/1 — talk 1 source
2. Issue 본문 편집 (markdown — Reveal.js comment·speaker notes 그대로 됨)
3. Actions 탭 → **Sync slides from issue** → **Run workflow** → `talk: 1` 입력
4. 워크플로가 `talks/talk1/slides.md` 덮어쓰고 commit + push → Pages 재배포 (~30s)

워크플로는 issue **body만** 사용. title·label·comments는 무시.

### 직접 편집

`talks/talkN/slides.md` 를 PR/직접 commit으로 편집해도 작동함. 단 이러면 Issue와 어긋남 — 다음 sync에서 Issue 본문이 덮어씀.

### 마크다운 규칙 (Reveal.js)

- `---` (단독 줄) = 슬라이드 구분
- `--` (단독 줄) = 같은 슬라이드 안 vertical 분할
- `Note:` = speaker notes

## 로컬 미리보기

mermaid plugin이 ESM이라 정적 서버 필요:

```bash
python3 -m http.server 8000
# 또는
npx serve .
```

- `http://localhost:8000/` — 랜딩
- `http://localhost:8000/talks/talk1/` — 개별 발표

## 발표 단축키

| 키 | 동작 |
|---|---|
| `→` `←` `Space` | 슬라이드 이동 |
| `S` | Speaker view (다른 모니터) |
| `F` | 풀스크린 |
| `O` | 슬라이드 개요 |
| `.` | 화면 검정 / `B` 화면 흰색 |
| `?` | 단축키 도움말 |

## 인쇄용 PDF

발표 URL에 `?print-pdf` 붙여 브라우저 인쇄:

```
http://localhost:8000/talks/talk1/?print-pdf
```

## 디자인 시스템 요약

| 변수 | HEX | 용도 |
|---|---|---|
| `--bg` | `#FAF8F5` | 배경 |
| `--fg` | `#2A2A2A` | 본문 |
| `--muted` | `#8B8680` | 보조 |
| `--accent` | `#3D5A6C` | 강조 |

자세한 건 [`docs/design.md`](docs/design.md). mermaid theme도 같은 팔레트.

## 작업 원칙

- **References는 verbatim**으로. 원문이 사라질 수 있으니 사본 두고, 개인 정보·상태값은 제외.
- **화자 이름은 익명화** (`[발표자]`) — commit `7db1708` 정책.
- 발표 톤은 발표별로 정의 (`talks/talkN/notes.md`). 시리즈 공통 원칙은 `docs/overview.md`.

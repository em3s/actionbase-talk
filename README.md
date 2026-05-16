# actionbase-talk

액션베이스 발표 자료 모음. Reveal.js · GitHub Pages.

## 미리보기

로컬에서 정적 서버로 실행 (mermaid plugin이 ESM이라 file:// 로는 안 됨):

```bash
python3 -m http.server 8000
# 또는
npx serve .
```

- `http://localhost:8000/` — 랜딩 페이지 (talks 목록)
- `http://localhost:8000/talks/talk1/` — 개별 발표

발표 조작:
- 화살표 키 / Space로 이동
- `S`로 스피커 노트 창 (다른 모니터에 띄움)
- `F`로 풀스크린
- `O`로 슬라이드 개요

## 구조

```
.
├── index.html              랜딩 페이지 (talks 목록)
├── css/
│   ├── theme.css           슬라이드용 디자인 시스템
│   └── landing.css         랜딩용 스타일
├── talks/
│   └── talk1/
│       ├── index.html      Reveal.js entry, mermaid initialize
│       └── slides.md       슬라이드 + speaker notes
├── docs/                   재사용 도구함 (overview, narrative, design, references)
└── .claude/                Claude Code 설정 + 발표별 작업 메모
```

## 새 talk 추가

```bash
cp -r talks/talk1 talks/talk2
# talks/talk2/slides.md 편집
# index.html의 .talks 리스트에 <li> 항목 추가
```

## 슬라이드 작성

- `---` (단독 줄) = 슬라이드 구분
- `--` (단독 줄) = 같은 슬라이드 안의 vertical 분할
- `Note:` = speaker notes

## 디자인 시스템

CSS variables in `css/theme.css`:

| Variable | Hex |
|---|---|
| `--bg` | `#FAF8F5` |
| `--fg` | `#2A2A2A` |
| `--muted` | `#8B8680` |
| `--accent` | `#3D5A6C` |

Mermaid theme은 같은 팔레트로 talks/talkN/index.html 안에서 wiring.

## GitHub Pages 배포

repo settings → Pages → Source: `main` branch, `/` (root) → Save.

`https://em3s.github.io/actionbase-talk/` 에서 랜딩, `/talks/talk1/` 에서 발표.

## 인쇄용 PDF

쿼리 파라미터 `?print-pdf` 붙여서 브라우저에서 열고 PDF로 출력:

```
http://localhost:8000/talks/talk1/?print-pdf
```

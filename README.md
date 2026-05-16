# actionbase-talk

"액션베이스가 자라온 길" — Reveal.js 발표 자료.

## 미리보기

로컬에서 정적 서버로 실행 (mermaid plugin이 ESM이라 file:// 로는 안 됨):

```bash
python3 -m http.server 8000
# 또는
npx serve .
```

`http://localhost:8000` 열기.

- 화살표 키 / Space로 이동
- `S`로 스피커 노트 창 (다른 모니터에 띄움)
- `F`로 풀스크린
- `O`로 슬라이드 개요

## 구조

```
.
├── index.html          Reveal.js entry, mermaid initialize
├── slides.md           16장 슬라이드 + speaker notes
├── css/theme.css       디자인 시스템 (off-white + charcoal)
└── assets/             (필요한 이미지)
```

`slides.md`만 편집하면 됩니다.

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

Mermaid theme is wired to the same palette in `index.html`.

## GitHub Pages 배포

저장소 만든 뒤:

```bash
git init
git add .
git commit -m "Initial slides"
git branch -M main
git remote add origin git@github.com:em3s/actionbase-talk.git
git push -u origin main
```

GitHub repo settings → Pages → Source: `main` branch, `/` (root) → Save.

`https://em3s.github.io/actionbase-talk/` 에서 접근 가능.

## 인쇄용 PDF

쿼리 파라미터 `?print-pdf` 붙여서 브라우저에서 열고 PDF로 출력:

```
http://localhost:8000/?print-pdf
```

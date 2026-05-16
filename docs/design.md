# Design System

> 슬라이드 레이아웃·구성 결정 등 발표별 디자인 결정은 `talks/talkN/design.md`에 둠.

## 포맷 선택

| 검토 | 결정 |
|---|---|
| Marp | ✗ |
| Keynote | ✗ |
| Google Slides | ✗ |
| **Reveal.js + GitHub Pages** | ✓ |

**선택 이유**:
- 원격 코딩 환경에서 검증 가능
- mermaid native 렌더
- 마크다운 소스 — `slides.md` 한 파일 관리
- 버전 관리 + Pages 자동 빌드
- 발표용/PDF용 둘 다 가능
- 최종 폴리시는 본인이 직접

## 컬러 팔레트

CSS variables in [`../css/theme.css`](../css/theme.css):

| 변수 | HEX | 용도 |
|---|---|---|
| `--bg` | `#FAF8F5` | 배경 (off-white) |
| `--fg` | `#2A2A2A` | 본문 (charcoal) |
| `--muted` | `#8B8680` | 캡션, 보조 (warm gray) |
| `--accent` | `#3D5A6C` | 강조 1~2곳 (muted teal) |
| `--rule` | `#D8D3CC` | 구분선 |

**원칙**: 한 슬라이드에 accent는 1~2곳만. 강조 인플레이션 방지.

## 타이포그래피

- **한글**: Pretendard (CDN)
- **영문**: Pretendard (한글과 자연 매칭)
- **모노**: SF Mono / JetBrains Mono

## 다이어그램 (mermaid)

mermaid theme도 같은 팔레트로 통일 — 각 talk의 `index.html`의 `themeVariables`에서 설정.

```js
themeVariables: {
    fontFamily: 'Pretendard, sans-serif',
    primaryColor: '#FAF8F5',
    primaryTextColor: '#2A2A2A',
    primaryBorderColor: '#2A2A2A',
    lineColor: '#2A2A2A',
    secondaryColor: '#3D5A6C',
    tertiaryColor: '#FAF8F5',
    background: '#FAF8F5'
}
```

**원칙**: 화살표 5개 이하. 강조하려는 노드만 accent로 채워서 시선 유도.

## 빌드·배포

- **로컬 미리보기**: `python3 -m http.server 8000` (정적 서버 필요 — mermaid plugin이 ESM)
  - 랜딩: `http://localhost:8000/`
  - 개별 발표: `http://localhost:8000/talks/talk1/`
- **PDF 출력**: 발표 URL에 `?print-pdf` 붙여서 브라우저 인쇄
- **배포**: `git push` → GitHub Pages 자동 재빌드 (~30초)
- **URL**: https://em3s.github.io/actionbase-talk/ (랜딩) · `/talks/talkN/` (각 발표)

## 발표 단축키

| 키 | 동작 |
|---|---|
| `→` `←` `Space` | 슬라이드 이동 |
| `S` | Speaker view (다른 모니터) |
| `F` | 풀스크린 |
| `O` | 슬라이드 개요 |
| `.` | 화면 검정 (잠시 시선 떼기) |
| `B` | 화면 흰색 |
| `?` | 단축키 도움말 |

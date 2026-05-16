# Design

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
- 마크다운 소스 — slides.md 한 파일 관리
- 버전 관리 + Pages 자동 빌드
- 발표용/PDF용 둘 다 가능
- 최종 폴리시는 본인이 직접

## 디자인 시스템

### 컬러 팔레트 (CSS Variables)

| 변수 | HEX | 용도 |
|---|---|---|
| `--bg` | `#FAF8F5` | 배경 (off-white) |
| `--fg` | `#2A2A2A` | 본문 (charcoal) |
| `--muted` | `#8B8680` | 캡션, 보조 (warm gray) |
| `--accent` | `#3D5A6C` | 강조 1~2곳 (muted teal) |
| `--rule` | `#D8D3CC` | 구분선 |

**원칙**: 한 슬라이드에 accent는 1~2곳만. 강조 인플레이션 방지.

### 타이포그래피

- **한글**: Pretendard (CDN)
- **영문**: Pretendard (한글과 자연 매칭)
- **모노**: SF Mono / JetBrains Mono

### 다이어그램 (mermaid)

mermaid theme도 같은 팔레트로 통일 — `index.html`의 `themeVariables`에서 설정.

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

**원칙**: 화살표 5개 이하. 액션베이스 노드만 accent로 채워서 시선 유도.

## 슬라이드 레이아웃 매핑

총 16장. 6가지 패턴 재사용.

| # | 슬라이드 | 패턴 |
|---|---|---|
| 1 | 제목 | Center title |
| 2 | 액션베이스란 + 왜 | 텍스트 본문 (vertical 분할) |
| 3 | Focuses / Avoids | 좌우 2열 비교 |
| 4 | 핵심 모델 | Pull quote + 코드 |
| 5 | 두 가지 도입 방식 | 좌우 2열 |
| 6 | Wish 기존 + 벽 | 다이어그램 + 캡션 |
| 7 | 5단계 마이그레이션 | 표 |
| 8 | Stage 1~2 | 다이어그램 + 캡션 |
| 9 | Stage 3~5 | 다이어그램 + 캡션 |
| 10 | 배운 것 | ladder 리스트 |
| 11 | Friends | 다이어그램 + 캡션 |
| 12 | 원장 vs 뷰 | 비교 표 |
| 13 | 사다리 | ladder 리스트 |
| 14 | 검증 4 레이어 + 핵심 | 표 + pull quote |
| 15 | 핸드오프 | 좌우 2열 (질문) |
| 16 | 닫기 | Center title |

## 슬라이드 14 — 특수 처리

발표 전체의 핵심 한 줄을 land 시키는 슬라이드.

```
[상단 60%]  검증 4 레이어 표
─────────  (hr 구분선)
[하단 40%]  큰 카피 (pull quote)
            "문제가 안 생기는 시스템을 만든 게 아니라,
             생겨도 알아채고 고칠 수 있는 시스템을 만들었습니다."
```

- 카피의 핵심 단어 (`알아채고 고칠 수 있는`)만 accent 색 + 굵게
- 발표 시 표 설명 후 **잠시 멈춤** → 카피로 시선 이동

## 익명 처리

다음 두 발표자는 **○○님**으로 익명 표기. 실명은 발표 직전 본인이 슬라이드/스크립트에서 직접 치환.

해당 위치:
- 슬라이드 13 (Lv.4 멀티홉 → ○○)
- 슬라이드 14 (Shadow Testing → ○○)
- 슬라이드 15 (세션 2 ○○, 세션 3 ○○)
- 각 슬라이드의 speaker notes

## 구성 결정

### 핵심 한 줄의 위치

슬라이드 14에 큰 카피로 시각화. 발표 시 표 설명 후 잠시 멈춤으로 시선 이동.

### 오프닝 / 클로징

- **오프닝**: "3년의 이야기인데, 시간 순이 아니라 어떤 길을 걸어왔는지로 풀어볼게요."
- **클로징**: "오늘 이 길의 한 구간을 같이 걸어봤습니다. 다음 두 분이 그 다음 구간을 안내해 주실 겁니다. 감사합니다."

### Recent Views (Async) 변형

원장 패턴의 변형 사례. **15분 트림에서 제외.** 원장 핵심에는 영향이 없어 시간 절약.

### 멀티홉 · 섀도 깊이

**티저까지만.** 각각 2세션·3세션의 main topic이라 1세션에서는 elaborate 금지.

- 멀티홉 → 사다리 마지막 칸에서 한 줄, "잠시 후 ○○님이 보여드립니다"로 전환
- Shadow Testing → 검증 표의 한 칸, "○○님이 자세히"로 전환

## 빌드·배포

- **로컬 미리보기**: `python3 -m http.server 8000` (정적 서버 필요 — mermaid plugin이 ESM)
- **PDF 출력**: URL에 `?print-pdf` 붙여서 브라우저 인쇄
- **배포**: `git push` → GitHub Pages 자동 재빌드 (~30초)
- **URL**: https://em3s.github.io/actionbase-talk/

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

<!-- .slide: class="center-title" -->

# 액션베이스가 자라온 길

#### 시리즈 1/3

<div class="meta">발표자 · 2026</div>

Note:
안녕하세요. 오늘은 액션베이스가 어떻게 자라왔는지를 말씀드립니다.

3년의 이야기인데, 시간 순이 아니라 — 어떤 길을 걸어왔는지로 풀어볼게요.

시리즈 세 세션 중 첫 번째고, 다음 두 분이 그 안의 두 챕터를 깊이 land 시켜주실 겁니다. 저는 우산을 펼치는 역할이에요.

---

## 액션베이스란 — 그리고 왜 만들었나

----

좋아요·찜·최근 본 상품·친구 관계 — **사용자 상호작용 데이터베이스**

2024년부터 카카오 커머스에서 서비스 운영 중 · 분당 백만 건 이상

----

서비스마다 비슷한 데이터 구조와 처리를 **반복해서 설계하고 있었습니다.**

**"누가, 무엇을, 어디에 했다"** 라는 관계 모델로 같은 방식으로 다뤄볼 수 없을지 — 거기서 시작했습니다.

Note:
액션베이스는 좋아요, 찜, 최근 본 상품 같은 사용자 상호작용 데이터를 다루는 데이터베이스입니다. 카카오 커머스에서 개발돼서 2024년부터 서비스 운영 중이고, 지금 선물하기의 찜·최근 본 상품, 카카오톡 친구 관계에서 쓰입니다. 분당 백만 건 이상의 요청이 흐르고 있어요.

왜 만들었느냐. 이런 기능들을 개발하다 보면 서비스마다 형태는 다른데, 비슷한 데이터 구조와 처리를 반복해서 설계하게 됩니다. 같은 문제를 다른 방법으로 풀고 있는 거죠.

"누가, 무엇을, 어디에 했다"라는 관계 모델로 정리해서 같은 방식으로 다뤄볼 수 없을지 — 거기서 시작했습니다.

---

## 무엇을 하고, 무엇을 안 하는가

<div class="two-col muted-right">

### Focuses on

- 사용자 상호작용 실시간 처리
- 한정된 액세스 패턴 (GET·COUNT·SCAN)
- 지속적 쓰기, 즉각 읽기
- WAL/CDC를 Kafka로
- Pluggable storage

### Explicitly avoids

- 범용 그래프 쿼리
- 무한 traversal·분석
- 배치 적재, 지연 인덱싱
- 다운스트림 처리 소유
- 또 다른 storage engine 제작

</div>

Note:
액션베이스가 무엇이 아닌지부터 말씀드릴게요. 왼쪽이 하는 일, 오른쪽이 의도적으로 안 하는 일입니다.

분석은 분석 시스템이, 검색은 검색 시스템이. 우리는 OLTP만. 모든 걸 다 하려고 하면 어떤 것도 잘 못 합니다. 이 선이 시스템의 모양을 결정했어요.

---

## 핵심 모델 — 엣지 하나

<div class="pull">누가 &nbsp;—&nbsp; 무엇을 &nbsp;—&nbsp; 어디에 했다</div>

```
좋아요    (user, liked,  post)
찜        (user, wished, product)
최근 본   (user, viewed, product)
친구      (user, follows, user)
```

Note:
모델은 단순합니다. 엣지 하나. 누가, 무엇을, 어디에 했다.

좋아요, 찜, 본 적, 친구 — 다 다른 기능 같지만 액션베이스 안에서는 모두 한 엣지입니다. 네 가지를 따로 만들면 네 개의 시스템이고, 같은 모델로 보면 한 시스템이에요.

정확한 카운트, 일관된 토글, 순서 보장. 어떤 종류의 상호작용이든 결국 이 세 가지를 요구합니다.

---

## 두 가지 도입 방식

<div class="two-col">

### 원장으로 (SSOT)

데이터의 **출처**가 되는 길

→ Wish 사례

### 뷰로 (CQRS)

기존 시스템 **옆**에 붙는 길

→ Friends 사례

</div>

Note:
이 모델이 실제 서비스에는 어떻게 들어왔을까요. 두 가지 도입 방식으로 보여드립니다.

하나는 원장으로 들어가는 길. 데이터의 출처가 되는 길입니다. 다른 하나는 뷰로 들어가는 길. 기존 시스템 옆에 붙는 길이에요. 각각 사례 하나씩.

---

## Wish — 기존 아키텍처와 벽

<pre class="mermaid">
flowchart LR
    App[App] --> MySQL[(MySQL)]
    MySQL --> Batch[Spring Batch]
    Batch --> Redis[(Redis)]
</pre>

<div class="caption">테이블 크기 · MySQL ↔ Redis 일관성 · 샤딩의 또 다른 복잡도</div>

Note:
선물하기의 찜은 원래 이 구조였습니다. MySQL이 원장, Spring Batch가 상품별 카운트 집계, Redis가 빠르게 내려주는 — 익숙한 조합이죠.

트래픽이 늘면서 한계가 보였습니다. 테이블이 커졌고, MySQL과 Redis 사이 일관성이 점점 복잡해졌어요. 샤딩도 검토했는데 샤딩 자체의 복잡도가 또 다른 문제를 만들었습니다.

그래서 액션베이스가 그때 첫 번째 본진을 받았습니다. 다만 그 시점에 액션베이스는 신생이었어요. 한 번에 옮기기엔 검증이 부족했습니다. 그래서 천천히 옮겼습니다.

---

## 5단계 마이그레이션

| Stage | 무엇을 |
|---|---|
| 1. Dual Write | 쓰기를 양쪽으로 |
| 2. Validation | 1개월 비교 |
| 3. Dual Read | shadow read |
| 4. Read Cutover | 읽기 전환 |
| 5. Cleanup | 기존 시스템 제거 |

<div class="caption">신뢰가 쌓일 때마다 한 칸씩 · 각 단계마다 롤백할 길을 남긴다</div>

Note:
5단계로 나눠 옮겼습니다. 한 번에 갈아끼우는 게 아니라 — 신뢰가 쌓일 때마다 한 칸씩. 각 단계마다 롤백할 길을 남겼습니다. 죽지 않을 길을 둘 이상.

---

## Stage 1 — 2 · Dual Write + Validation

<pre class="mermaid">
flowchart LR
    App --> Existing[기존 시스템]
    App --> AB[(Actionbase)]
    App -.read.-> Existing
</pre>

<div class="caption"><strong>MySQL Dump</strong> ↔ <strong>Actionbase CDC Snapshot</strong> · 1개월 매일 비교</div>

Note:
Stage 1, 쓰기를 양쪽에 다 했습니다. 읽기는 그대로 기존 시스템. 액션베이스에 데이터가 차오르기 시작했어요. 히스토리 데이터는 한 번에 옮겼습니다. MySQL을 dump하고, 액션베이스에 bulk load하고, 그 사이 변경은 WAL을 replay해서 따라잡았어요. 무중단으로 가능했습니다.

Stage 2, 쓰기가 양쪽에 들어간다고 해서 데이터가 같다는 보장은 없습니다. 그래서 1개월 동안 매일 비교했어요. MySQL dump를 진실로, 액션베이스 CDC 누적 스냅샷을 검증 대상으로. 두 독립된 경로로 만든 데이터가 같은지를 본 거죠.

---

## Stage 3 — 5 · Shadow Read → Cutover → Cleanup

<pre class="mermaid">
flowchart LR
    App --> AB[(Actionbase)]
    App --> Existing[기존 시스템<br/>backup]
    App -.read.-> AB
</pre>

<div class="caption">Shadow Read · 읽기 전환 (원장) · 수개월 후 Cleanup</div>

Note:
Stage 3, 데이터가 맞는 것만으로는 부족했습니다. 트래픽도 받아내야 해요. 같은 쿼리를 그림자로 액션베이스에 보냈습니다. 결과는 안 쓰고, 패턴만.

Stage 4, 여기서 비로소 읽기를 액션베이스로 돌렸습니다. 원장이 된 시점이에요. 다만 기존 시스템은 그대로 두었습니다. 무슨 일이 생기면 즉시 되돌리려고요.

Stage 5, 수개월 후 이상 없음을 확인하고 나서야 기존 시스템을 내렸습니다. 그 후로 batch도, Redis 동기화도 없어요. 한 시스템으로 정리됐습니다.

---

## 원장 도입에서 배운 것

<ul class="ladder">
    <li><span class="name">단계별 진행이 위험을 줄인다</span></li>
    <li><span class="name">롤백 경로를 항상 열어둔다</span></li>
    <li><span class="name">WAL replay로 무중단 bulk load</span></li>
</ul>

<div class="caption">이 5단계가 그 후 다른 서비스 마이그레이션의 template이 됐다</div>

Note:
이 5단계가 그 후 다른 서비스 마이그레이션의 template이 됐습니다. 단계별로 가면 위험이 줄고, 롤백 경로를 열어두고, WAL replay로 무중단으로 옮긴다.

---

## Friends — 옆에 붙는 길

<pre class="mermaid">
flowchart LR
    Existing[기존 시스템] --> Debezium[Debezium CDC]
    Debezium --> Kafka
    Kafka --> AB[(Actionbase)]
    App -.write.-> Existing
    App -.read.-> Existing
    App -.read.-> AB
</pre>

<div class="caption">소스 DB는 한 줄도 안 바꿨습니다 · CDC로 흘려보내면 뷰가 만들어진다</div>

Note:
카카오톡 친구 관계는 이미 잘 돌아가고 있었습니다. 수십 개 MySQL 샤드에 HBase view까지. 다만 view가 특정 access pattern에만 맞춰져 있어서, 새 쿼리 타입을 추가할 때마다 어려움이 있었어요.

갈아끼우는 게 아니라 옆에 붙이는 결정을 했습니다. Debezium CDC로 MySQL 변경을 캡처해서 Kafka 통해 액션베이스에 적용합니다. 액션베이스가 유연한 뷰 레이어가 되는 거죠.

소스 DB는 한 줄도 안 바꿨습니다. dual write도, cutover도 없어요. CDC를 흘려보내고 뷰가 만들어지면 끝입니다.

---

## 원장 vs 뷰

|  | 원장 (SSOT) | 뷰 (CQRS) |
|---|---|---|
| 데이터 소유 | 액션베이스 | 기존 시스템 |
| 도입 비용 | 마이그레이션 (5단계) | CDC 연결 (3단계) |
| 일관성 | Strong | CDC 지연만큼 eventual |
| 언제 | 원장을 함께 갈아낄 결심 | 기존 유지하며 쿼리 확장 |

<div class="caption">어느 한 쪽이 우월하지 않습니다 · 자기에게 맞는 길을 고른다</div>

Note:
어느 한 쪽이 우월하지 않습니다. 팀의 상황과 데이터 owner 관계에 따라 두 길 중 자기에게 맞는 걸 고릅니다.

그런데 — 원장으로 들어왔든 뷰로 들어왔든, 일단 들어오면 같은 기능들이 따라옵니다.

---

## 가능해진 것들 — 사다리

<ul class="ladder">
    <li><span class="lvl">Lv.1</span> <span class="name">GET / COUNT / SCAN</span> <span class="ex">"내가 찜한 것"</span></li>
    <li><span class="lvl">Lv.2</span> <span class="name">Actionbase Now</span> <span class="ex">"이 상품, 지금 32명"</span></li>
    <li><span class="lvl">Lv.3</span> <span class="name">Actionbase Now+</span> <span class="ex">"지금 핫한 상품 top 10"</span></li>
    <li><span class="lvl">Lv.4</span> <span class="name">멀티홉</span> <span class="ex">"친구가 찜한 상품" &nbsp;→&nbsp; <strong>도키</strong></span></li>
</ul>

<div class="caption">처음부터 설계한 게 아니라 — 운영되면서 한 칸씩 자란 흔적</div>

Note:
일단 들어오면 이 사다리가 따라옵니다.

Lv.1, 엣지 모델이니까 GET·COUNT·SCAN이 자연스럽게 됩니다.

Lv.2, Actionbase Now. Mutation 시점에 집계가 끝납니다. "이 상품, 지금 32명이 함께 보는 중" — 처음엔 Count를 비동기로 했는데 stale이 문제가 됐어요. 그래서 Mutation 안으로 집어넣었습니다.

Lv.3, Now+. Now가 "이 상품 몇 명?"이라면, Now+는 거꾸로 "어느 상품이 제일 핫?". 같은 집계, 다른 축. 그래서 plus입니다.

Lv.4, 멀티홉. "내 친구가 찜한 상품은 뭐지?" — 한 엣지가 아니라 두 엣지를 잇는 질문이에요. 어떻게 풀었는지는 잠시 후 도키님이 보여드립니다.

이 사다리는 처음부터 설계한 게 아닙니다. 운영되면서 서비스가 요구할 때마다 한 칸씩 자란 흔적이에요.

---

## 검증 4 레이어

| 단계 | 방법 | 목적 |
|---|---|---|
| Dev | Tests as Contracts | 약속한 동작을 정의하고 지킨다 |
| Pre-Deploy | Shadow Testing &nbsp;→&nbsp; **데이브** | 프로덕션 트래픽으로 검증 |
| Migration | Comparison Verification | Source ↔ Actionbase 일치 |
| Runtime | HBase Consistency | State · Index · Count 일관성 |

<hr/>

<div class="pull">
문제가 안 생기는 시스템을 만든 게 아니라,<br/>
생겨도 <strong>알아채고 고칠 수 있는</strong> 시스템을 만들었습니다.
</div>

Note:
신뢰는 한 가지 방법으로 만들지 않았습니다. 시스템 생애의 각 단계마다 다른 검증을 두었어요.

Dev 단계엔 Tests as Contracts — 서비스 팀과의 계약이 곧 시나리오 테스트입니다. 깨지면 PR이 막혀요.

Pre-Deploy 단계엔 Shadow Testing — 진짜 프로덕션 트래픽으로 새 버전을 검증합니다. 데이브님이 자세히 보여주실 거예요.

Migration 단계엔 Comparison Verification — 같은 데이터를 두 독립된 경로로 만들어 비교. Wish 5단계의 1개월 비교가 이겁니다.

Runtime엔 HBase Consistency — State는 진실, Index·Count는 파생. 어긋나면 State 기준으로 재생성합니다.

어느 layer 하나로는 충분하지 않았습니다. (잠시 멈춤) 문제가 안 생기는 시스템을 만든 게 아니라, 생겨도 알아채고 고칠 수 있는 시스템을 만들었습니다.

---

## 두 질문이 남았습니다

<div class="two-col">

### 질문 1

이 그래프로 **무엇이 가능한가**

→ 세션 2 · **도키** · 멀티홉/마스터 그래프

### 질문 2

프로덕션에서 **정말 믿어도 되는가**

→ 세션 3 · **데이브** · 섀도 테스트

</div>

Note:
오늘 액션베이스가 어떻게 들어왔는지를 두 길로 보여드렸고, 들어온 다음 어떤 기능과 검증이 따라왔는지도 보여드렸습니다. 자연스럽게 두 가지 질문이 남아요.

첫 번째 — "이 그래프로 무엇이 가능한가." 도키님이 멀티홉으로 답해주실 겁니다. 사다리 가장 위에 있던 그 멀티홉이에요.

두 번째 — "이걸 프로덕션에서 정말 믿어도 되는가." 데이브님이 섀도 테스트로 답해주실 겁니다. 검증 4가지 중 하나였던 그 섀도예요.

저는 오늘 우산을 펼친 거고, 두 분이 그 안의 두 챕터를 land 시켜주실 겁니다.

---

<!-- .slide: class="center-title" -->

오늘 이 길의 한 구간을 같이 걸어봤습니다.

다음 두 분이 그 다음 구간을 안내해 주실 겁니다.

# 감사합니다

Note:
오늘 이 길의 한 구간을 같이 걸어봤습니다. 다음 두 분이 그 다음 구간을 안내해 주실 거예요. 감사합니다.

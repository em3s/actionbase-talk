<!-- .slide: class="center-title" -->

# 액션베이스 소개

#### 시리즈 1/3

<div class="meta">발표자 · 2026</div>

Note:
안녕하세요. 발표 시작합니다.

오늘 시리즈는 세 발표로 이어집니다. 저는 첫 번째로 액션베이스가 어떤 시스템인지 소개합니다. 이어서 두 번째 발표는 멀티홉, 세 번째 발표는 섀도 테스트입니다.

---

## 3종 관계

<div class="three-col">

### 사용자 ↔ 사용자  (U2U)

친구 · 선물 주고받은 사이

### 사용자 ↔ 상품  (U2I)

찜 · 최근 본 · 구매

### 상품 ↔ 상품  (I2I)

연관 상품

</div>

<hr/>

<div class="pull">
U2U는 <strong>카카오의 자산</strong> · U2I는 <strong>실시간 OLTP</strong> · I2I는 <strong>추천의 축</strong>
</div>

Note:
사용자 상호작용 데이터는 세 가지 관계로 나뉩니다. 추천 분야에서는 U2U, U2I, I2I라고 부릅니다.

U2U — 사용자와 사용자. 친구, 선물 주고받은 사이. 사람과 사람을 *1차로* 잇는 관계는 국내에서 카카오가 가진 게 가장 강합니다. "내가 본 상품을 본 사람" 같은 *파생* U2U도 있지만 서비스에 녹이기 까다로워요. 우리 강점은 1차 관계 쪽입니다.

U2I — 사용자와 상품. 찜, 최근 본, 구매. 어느 커머스에나 있지만, 우리에겐 이게 *실시간 OLTP*로 일어난다는 게 핵심입니다.

I2I — 상품과 상품. 연관 상품. 추천의 축입니다. 보통 배치로 만들지만 앞단 U2I가 실시간이라 결과는 실시간으로 보여요.

세 가지를 다 가지고, 게다가 *연결*할 수 있을 때 — 가치가 만들어집니다.

---

## 3종 관계 — 예제

<div class="three-col">

### 사용자 ↔ 사용자

- 카카오톡 친구
- 선물 주고받은 사이
- 팔로우 · 팔로워

### 사용자 ↔ 상품

- 찜
- 좋아요
- 최근 본
- 장바구니
- 구매

### 상품 ↔ 상품

- 연관 상품
- 함께 본 상품
- 함께 산 상품

</div>

Note:
각 관계가 실제 서비스에서 어떤 모양으로 나타나는지 — 조금 더 구체적으로.

U2U는 카카오톡 친구, 선물 주고받은 사이, 팔로우 같은 사람-사람 관계.

U2I는 찜, 좋아요, 최근 본, 장바구니, 구매 — 사람이 상품에 대해 남기는 흔적.

I2I는 연관·함께 본·함께 산 상품 — 상품 사이의 관계.

세 종류가 모두 한 시스템 안에 있어야 비로소 *연결*할 수 있게 됩니다.

---

## 셋이 결국 같은 모양

```
친구       (사용자, 친구,   사용자)
찜         (사용자, 찜,     상품)
최근 본    (사용자, 봄,     상품)
연관       (상품,   연관,   상품)
```

**엣지 하나로 일반화한 데이터베이스** — 액션베이스입니다.

Note:
세 관계가 다르게 보여도 — 결국 같은 모양입니다. 무엇이, 어떤 동작으로, 무엇을. 엣지 하나.

서비스마다 비슷한 걸 따로 만들고 있었어요. 한 시스템으로 다루면 어떨까. 거기서 시작했습니다. 그게 액션베이스고, 2024년부터 카카오 커머스에서 운영 중입니다.

같은 엣지로 다루면 *셋을 연결*할 수도 있습니다. "친구가 찜한 상품" 같은 U2U와 U2I를 잇는 질문. 어떻게 연결하느냐는 2편 멀티홉에서 다룹니다.

---

## 우리가 하는 일 — 세 축

<div class="three-col">

### 엔진

액션베이스를 **만든다**

↔ MySQL 개발

### 플랫폼

SaaS로 **제공한다**

↔ MySQL 운영

### 서비스

데이터 서비스를 **올린다**

↔ MySQL 활용

</div>

<hr/>

<div class="pull">셋 중 하나가 아니라 — <strong>한 팀이 셋을 같이</strong> 봅니다</div>

Note:
한 가지 명확히 해두고 싶습니다. 액션베이스 팀이 무엇을 하는가.

세 축이 있어요. MySQL에 비유하면 분명해집니다.

엔진 — 액션베이스 자체를 만듭니다. MySQL을 개발하는 일에 해당해요.

플랫폼 — 만든 액션베이스를 SaaS로 운영해서 서비스 팀에 내놓습니다. MySQL을 운영하는 일에 해당해요.

서비스 — 액션베이스 위에 데이터 서비스를 올립니다. MySQL을 활용해 데이터 서비스를 만드는 일에 해당해요.

셋 중 하나가 아닙니다. 한 팀이 세 축을 같이 봅니다. 뒤에서 나올 Wish/Friends는 서비스 축의 증거고, 검증 4단계는 플랫폼 축의 증거고, 바로 다음 "한다/안 한다"는 엔진 축의 경계입니다.

---

## 무엇을 하고, 무엇을 안 하는가

<div class="two-col muted-right">

### 한다

- 사용자 상호작용 실시간 처리
- GET · COUNT · SCAN
- 지속적 쓰기, 즉각 읽기

### 안 한다

- 범용 그래프 쿼리
- 무한 traversal · 분석
- 배치 적재, 지연 인덱싱

</div>

Note:
실시간 OLTP만 합니다. 분석·검색·범용 그래프 쿼리는 안 합니다.

선을 그은 이유는 단순해요. 모든 걸 다 하려고 하면 어떤 것도 잘 못 합니다.

---

## 실제 연동 — 세 가지 사례

<div class="three-col">

### Wish

원장 (SSOT)

5단 마이그레이션

<div class="caption">Strong consistency</div>

### Recent Views

원장+ (SSOT)

즉각성과 정확성 분리 · WAL → Spark

<div class="caption">Eventual consistency</div>

### Friends

뷰 (CQRS)

기존 시스템 옆 · CDC 연결

<div class="caption">옆에 붙는 길</div>

</div>

Note:
실제 서비스에는 세 가지 길로 들어갔습니다.

Wish — 선물하기의 찜. 원장이 되는 길입니다. MySQL+Redis 구조를 5단계 마이그레이션으로 옮겼어요. 매 단계마다 되돌릴 수 있는 길을 남겼고, 이 5단계가 그 후 다른 서비스 마이그레이션의 template이 됐습니다. Strong consistency가 핵심.

Recent Views — 같은 원장이지만 쓰기가 폭발적이라 처리 모드를 바꿨어요. 쓰기를 WAL에 queue=true로 적고 즉시 응답, Spark Streaming이 백그라운드로 처리합니다. 즉각성과 정확성을 분리한 거죠. Eventual consistency 허용.

Friends — 카카오톡 친구 관계. 기존 시스템 옆에 붙는 길입니다. 소스 DB는 한 줄도 안 바꾸고, CDC로 변경을 흘려보내서 뷰만 만들었어요. CQRS 패턴.

셋 다 같은 엔진 위에서 일어나는 일입니다. 팀 상황과 데이터 owner 관계에 맞춰 고릅니다.

---

## 연동하면 — 가능해지는 것들

<ul class="ladder">
    <li><span class="lvl">Lv.1</span> <span class="name">GET / COUNT / SCAN</span> <span class="ex">"내가 찜한 것"</span></li>
    <li><span class="lvl">Lv.2</span> <span class="name">Now</span> <span class="ex">"이 상품, 지금 32명"</span></li>
    <li><span class="lvl">Lv.3</span> <span class="name">Now+</span> <span class="ex">"지금 핫한 상품 top 10"</span></li>
    <li><span class="lvl">Lv.4</span> <span class="name">멀티홉</span> <span class="ex">"친구가 찜한 상품"</span></li>
</ul>

Note:
일단 연동되면 이 기능들이 따라옵니다.

Lv.1 GET·COUNT·SCAN. 엣지 모델이니까 자연스럽게 됩니다.

Lv.2 Now. Mutation 시점에 집계가 끝납니다. "지금 32명이 함께 보고 있다" 같은 카피가 가능해져요.

Lv.3 Now+. "어느 상품이 제일 핫?" — 같은 집계, 다른 축.

Lv.4 멀티홉. "내 친구가 찜한 상품" — 한 엣지가 아니라 두 엣지를 잇는 질문입니다. 다음 발표에서 자세히 다룹니다.

---

## 안정적으로 쓸 수 있다

| 단계 | 방법 |
|---|---|
| Dev | Tests as Contracts |
| Pre-Deploy | Shadow Testing |
| Migration | Comparison Verification |
| Runtime | HBase Consistency |

<div class="caption">완벽한 시스템이 아니라, <strong>틀려도 고칠 수 있는</strong> 시스템</div>

Note:
운영에서 신뢰할 수 있어야 의미가 있습니다. 단계마다 다른 검증을 두었어요.

개발 중에는 Tests as Contracts. 서비스 팀과의 계약이 곧 시나리오 테스트입니다.

배포 전에는 Shadow Testing. 진짜 프로덕션 트래픽으로 새 버전을 검증합니다.

마이그레이션에서는 Comparison Verification. 두 독립된 경로의 결과를 비교합니다.

런타임에서는 HBase Consistency. State는 진실, Index·Count는 파생. 어긋나면 State 기준으로 재생성합니다.

완벽한 시스템을 만든 게 아니라, 틀려도 고칠 수 있는 시스템을 만들었습니다.

---

## 앞으로 가야 할 길

<div class="two-col">

### 그래프 쿼리의 깊이

다음 발표 · **멀티홉**

→ ○○

### 검증의 깊이

다음 발표 · **섀도 테스트**

→ ○○

</div>

Note:
앞으로 가야 할 길이 두 갈래입니다.

하나는 그래프 쿼리의 깊이. 멀티홉이 그 시작입니다. 이어서 ○○님이 다뤄주십니다.

다른 하나는 검증의 깊이. 섀도 테스트가 그 시작입니다. 마지막에 ○○님이 다뤄주십니다.

---

<!-- .slide: class="center-title" -->

# 감사합니다

Note:
들어주셔서 감사합니다.


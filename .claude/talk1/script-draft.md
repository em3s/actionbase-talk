# Script — Draft (장황 버전, 트림 전)

원본 작업 자료. 1~2시간 분량으로 작성된 장황 버전. 이후 15분으로 트림됨 (최종은 [03-script-final.md](03-script-final.md) 참고).

---

## 메타

- **제목**: 액션베이스가 자라온 길
- **시간**: 1~2시간 가정 (이후 15분으로 트림)
- **톤**: 담담, 회고, 진행형. 자랑 ✗, 비장 ✗, 재정의 ○
- **구조**: 우산 모델 — 시니어가 너비, 다음 두 분이 깊이
- **voice 레퍼런스**: GitHub Discussion #32 *"Did We Need a Dedicated Interaction Database — or Did We Overbuild?"*

## 스켈레톤 (7막)

```
① 정체성 — Actionbase가 뭐고 왜 만들었나
② 원장으로 (SSOT) — Wish 사례
③ 뷰로 (CQRS) — Friends 사례
④ 가능해진 것들 — Now · Now+ · 멀티홉 (티저)
⑤ 검증 4 레이어 — 살아남는 방식 (티저)
⑥ 핸드오프 — 2세션 · 3세션
⑦ 닫기 — 자라온 길
```

---

## ① 정체성

### 슬라이드 1 — 제목

```
액션베이스가 자라온 길
(발표자 이름 / 팀 / 날짜)
```

**멘트 (담담히 시작)**:
> "안녕하세요. 오늘은 액션베이스가 어떻게 자라왔는지를 말씀드리려고 합니다. 3년의 이야기인데, 시간 순으로가 아니라 — 어떤 길을 걸어왔는지로 풀어볼게요."

### 슬라이드 2 — 액션베이스란

> 액션베이스는 좋아요·최근 본 상품 같은 **사용자 상호작용 데이터**를 다루기 위해 카카오 커머스 내부에서 개발되어, **2024년부터 서비스 형태로 운영되어 온** 데이터베이스 시스템입니다.
>
> 현재 선물하기의 찜, 최근 본 상품, 카카오톡 친구 관계 등에서 사용되고 있습니다.

**멘트 보조**:
- "여러분이 매일 쓰시는 기능들 뒤에서 동작하고 있습니다."
- 숫자: "분당 백만 건 이상의 요청을 처리하고 있습니다."

### 슬라이드 3 — 왜 만들었나

> 이러한 기능을 개발하다 보면, 서비스마다 형태는 조금씩 다르지만 **유사한 데이터 구조와 처리 방식을 반복해서 설계하게 되는 경우**가 많습니다.
>
> 액션베이스는 이를 **"누가, 무엇을, 어디에 했다"**라는 관계 모델로 정리하고, 공통된 방식으로 다뤄볼 수 없을지 — **그 고민에서 출발했습니다.**

**멘트 보조**:
- "팀마다 스택이 달랐고, 스키마가 달랐고, 망가지는 방식이 달랐습니다."
- "같은 문제를 열 가지 다른 방법으로 풀고 있었습니다."

### 슬라이드 4 — Focuses on / Explicitly avoids

| Focuses on | Explicitly avoids |
|---|---|
| 사용자 상호작용 실시간 처리 (좋아요, 본 적, 팔로우) | 범용 그래프 쿼리 |
| 한정된 액세스 패턴 (GET, COUNT, SCAN) | 무한 traversal·분석 |
| 지속적 쓰기, 즉각 읽기 | 배치 적재, 지연 인덱싱 |
| WAL/CDC를 Kafka로 (yours or ours) | 다운스트림 처리 소유 |
| Pluggable storage (HBase 현재, 향후 확장) | 또 다른 storage engine 제작 |

**멘트**:
> "오해를 줄이기 위해 — 액션베이스가 무엇이 **아닌지**부터 말씀드립니다. 이 표의 왼쪽은 우리가 하는 일, 오른쪽은 우리가 **의도적으로 안 하는 일**입니다."
>
> "할 줄 모르는 게 아니라, **의도적으로 안 하는 겁니다.** 모든 걸 다 하려고 하면 어떤 것도 잘 못 합니다."
>
> "분석은 분석 시스템이, 검색은 검색 시스템이. 우리는 OLTP만."

### 슬라이드 5 — 핵심 모델 한 컷

```
누가  —  무엇을  —  어디에 했다
(source) (action)  (target)

예시:
user-123  —  wished  —  product-456
```

### 슬라이드 6 — 같은 모델, 다른 서비스

```
좋아요       (user, liked, post)
찜          (user, wished, product)
최근 본     (user, viewed, product)
친구        (user, follows, user)
```

**멘트**:
> "이 네 가지를 따로 만들면 네 개의 시스템이 됩니다. 같은 모델로 보면 — 한 시스템이 됩니다."
>
> "정확한 카운트, 일관된 토글, 순서 보장 — 어떤 종류의 상호작용이든 결국 이 세 가지를 요구합니다."

---

## ② 원장으로 (SSOT) — Wish

### 슬라이드 7 — 두 가지 도입 방식

```
원장으로 (SSOT)      뷰로 (CQRS)
   |                    |
   ↓                    ↓
Wish 사례           Friends 사례
```

### 슬라이드 8 — Wish의 기존 아키텍처

```
App → MySQL → Spring Batch → Redis
```

- **MySQL**: wish 데이터 저장 + 사용자별 forward 쿼리
- **Spring Batch**: reverse 카운트 집계 (상품별 찜한 사람 수)
- **Redis**: reverse 카운트 캐시

### 슬라이드 9 — 다음 단계가 필요해진 시점

- 테이블 크기 증가
- MySQL과 Redis 사이 일관성 유지 비용 상승
- MySQL 샤딩도 고민했지만 — 샤드 키 관리, 크로스 샤드 쿼리, 운영 오버헤드
- 액션베이스가 첫 본진을 받은 시점

**멘트 (정직 톤)**:
> "그때 액션베이스는 아직 신생 시스템이었습니다. 본진을 맡기에는 검증이 부족했어요. 그래서 **천천히 옮겨갔습니다.**"

### 슬라이드 10~14 — 5단계 마이그레이션

```
Stage 1: Dual Write           쓰기를 양쪽으로
Stage 2: Validation           1개월 비교
Stage 3: Dual Read            shadow read
Stage 4: Read Cutover         읽기 전환
Stage 5: Cleanup              기존 시스템 제거
```

**핵심 한 줄**:
> *"각 단계마다 롤백할 길을 남겨뒀습니다. 죽지 않을 길을 항상 둘 이상."*

#### Stage 1 — Dual Write
- 액션베이스 쓰기 추가 (읽기는 아직 기존 시스템)
- 히스토리 데이터: MySQL dump → bulk load → WAL replay
- 다운타임 없이 일관된 스냅샷 확보

#### Stage 2 — Validation (1개월)
- MySQL dump (진실) ↔ 액션베이스 CDC 스냅샷
- 매일 비교, 데이터 일치 확인 → 일관성 확보 신호

#### Stage 3 — Dual Read (Shadow Read)
- 액션베이스에 shadow read — 호출은 하지만 결과는 사용 안 함
- 프로덕션 트래픽 패턴을 액션베이스가 받아낼 수 있는지 검증

#### Stage 4 — Read Cutover
- 읽기를 액션베이스로 전환
- 이 시점부터 액션베이스가 **source of truth**
- 기존 시스템은 백업으로 유지 (롤백 경로)

#### Stage 5 — Cleanup
- 수개월 후, 이상 없음 확인
- 기존 시스템 제거
- 더 이상 배치 잡, 일관성 이슈 없음

### 슬라이드 15 — 원장 도입에서 배운 것

- **단계별 진행이 위험을 줄인다.** Dual write → dual read → cutover. 각 단계가 다음 단계를 검증.
- **롤백 경로를 항상 열어둔다.** Cutover 후에도 수개월 백업 유지.
- **WAL replay로 무중단 bulk load.** Dump → load → replay.

### 슬라이드 16 — 변형: Recent Views (Async)

- 원장 패턴이지만 **쓰기가 폭발적**인 케이스
- 동기 처리하면 응답 지연·백프레셔
- 해법: 비동기 처리

```
App → AB (queue=true) → WAL → Spark Streaming → Mutation
App → AB (read)
```

- 쓰기 요청 → WAL에 queue=true로 즉시 응답
- Spark Streaming이 WAL을 consume → mutation으로 변환
- 수십 ms 안에 반영

**멘트**:
> "같은 원장 패턴인데, 처리 모드만 바꿨습니다. 사용자는 즉시 응답을 받고, 실제 mutation은 백그라운드에서. **즉각성과 정확성을 분리한 거죠.**"

---

## ③ 뷰로 (CQRS) — Friends

### 슬라이드 17 — Friends의 기존 아키텍처

```
App → MySQL Shards → (sync) → HBase View
App → MySQL Shards (read)
App → HBase View (read)
```

- 카카오톡 친구 관계는 수십 개 MySQL 샤드에 분산
- HBase 기반 view layer가 이미 존재 (특정 access pattern용)

### 슬라이드 18 — 한계

- HBase view는 특정 access pattern에 최적화
- 새로운 쿼리 타입·스키마 변경에 유연성 부족

**멘트**:
> "기존 시스템이 잘못된 게 아닙니다. 다만 더 유연한 쿼리가 필요해졌어요. 그래서 — **갈아끼우는 게 아니라, 옆에 붙이는** 결정을 했습니다."

### 슬라이드 19 — 액션베이스를 옆에 붙이기

```
Existing → Debezium CDC → Kafka → Actionbase
App → Existing (write)
App → Existing (read)
App → Actionbase (read)
```

### 슬라이드 20 — 3단계 도입

```
Stage 1: CDC 파이프라인     Debezium → Kafka → Actionbase
Stage 2: Bulk Load          dump → load → WAL replay
Stage 3: New Query Layer    Actionbase를 새 쿼리 layer로
```

### 슬라이드 21 — 뷰 도입에서 배운 것

- **액션베이스는 CQRS 뷰로도 가치 있음.** 원장 갈아끼우지 않아도 됨.
- **Source DB CDC가 비침습적 통합을 가능하게 한다.** 쓰기 경로 변경 없음.
- **스키마 유연성이 새 use case를 해금.**

### 슬라이드 22 — 원장 vs 뷰 트레이드오프

| | 원장 (SSOT) | 뷰 (CQRS) |
|---|---|---|
| 데이터 소유 | 액션베이스 | 기존 시스템 |
| 도입 비용 | 마이그레이션 (5단계) | CDC 연결 (3단계) |
| 일관성 | Strong | CDC 지연만큼 eventual |
| 장점 | 깊은 통합, 모든 기능 | 가벼움, 비침습 |
| 단점 | 갈아끼우는 위험 | 원장 의존 유지 |
| 언제 | 원장도 함께 갈아낄 결심 | 기존 시스템 유지하며 쿼리 확장 |

---

## ④ 가능해진 것들

### 슬라이드 23 — 기능 사다리

```
Lv.1  GET / COUNT / SCAN       쌓인 데이터 조회        "내가 찜한 것"
Lv.2  Actionbase Now           실시간 집계 (per item)  "이 상품, 지금 32명"
Lv.3  Actionbase Now+          Now의 top-k             "지금 핫한 상품 top 10"
Lv.4  멀티홉                   그래프 순회             "친구가 찜한 상품"
```

### Lv.1 기본 (GET / COUNT / SCAN)

- 쌓인 엣지를 조회
- 시나리오:
  - GET: "내가 이 상품 찜했나?"
  - COUNT: "이 상품을 찜한 사람이 몇 명?"
  - SCAN: "내가 찜한 상품 목록 (최근 순)"

### Lv.2 Actionbase Now

- **Mutation 시점에 집계가 끝납니다** (per target 실시간)
- 시나리오: "이 상품, 지금 32명이 함께 보는 중"
- 기술적 핵심: Count가 1급 시민. Mutation 트랜잭션 안에 집계 포함.

**멘트**:
> "처음엔 Count를 비동기로 했는데, N초 stale이 어떤 서비스 요구를 못 맞췄어요. 그래서 Mutation 안으로 집어넣었습니다. 그게 Now입니다."

### Lv.3 Actionbase Now+

- **Now의 집계를 점수로 다시 쿼리**
- 시나리오: "지금 함께 보는 사람이 많은 상품 top 10"
- 데이터 축의 변화:
  - Now: (target → count) — point query
  - Now+: (count desc → target) — ranking query
- 같은 집계, 다른 축. 그래서 "+"

### Lv.4 멀티홉

- 한 엣지에서 다음 엣지로
- 시나리오: "내 친구가 찜한 상품" → 추천 시드
- 기술적 핵심:
  - Narrow row로는 N RPC 폭발 (N명 친구 = N개 Scan)
  - Wide row로 1 RPC (MultiGet)
  - 단, row가 무한히 자라니 Pruner 필요

**티저까지만**:
> "이건 한 엣지가 아니라 두 엣지를 잇는 질문입니다. 친구 엣지 + 찜 엣지. 이걸 어떻게 풀었는지 — 잠시 후 ○○님이 자세히 보여드립니다."

**핵심**: 멀티홉은 **반드시 elaborate 하지 말 것** — 2세션의 main topic.

---

## ⑤ 검증 4 레이어

| 단계 | 방법 | 목적 |
|---|---|---|
| **Dev** | Tests as Contracts | 약속한 동작을 정의하고 지킨다 |
| **Pre-Deploy** | Shadow Testing | 프로덕션 트래픽으로 새 버전 검증 |
| **Migration** | Comparison Verification | Source ↔ Actionbase 데이터 일치 |
| **Runtime** | HBase Consistency | State·Index·Count 일관성 |

### Tests as Contracts (Dev 단계)

- 서비스 팀과의 계약 = 시나리오 테스트 그 자체
- 테스트 = 스펙 = 문서 = 가드 (한 source of truth)
- 계약이 깨지면 PR 차단

### Shadow Testing (Pre-Deploy 단계)

```
Client → Nginx → Prod Actionbase
Nginx → Access Log → Kafka → Replay → Test Actionbase
Prod ↔ Test (compare)
```

- Nginx access log를 Kafka로 흘려보냄
- 같은 요청을 테스트 환경에서 replay
- 응답 비교, 차이 나면 배포 차단
- 라이브 트래픽에 영향 없음 (log 기반)

**티저까지만** — 3세션의 main topic.

### Comparison Verification (Migration 단계)

```
Source DB Dump        ↔        Actionbase CDC Snapshot
       (진실)                          (재구성)
              비교 (1개월)
```

- 같은 데이터를 두 독립된 경로로 만들어 비교
- Wish는 5단계 마이그레이션, 단계마다 검증

### HBase Consistency (Runtime)

- 액션베이스가 HBase에 저장하는 세 가지: **State**, **Index**, **Count**
  - State = source of truth
  - Index, Count = 파생 데이터
- HBase batch는 atomic이 아님 → partial failure 가능
- 주기적 검증: snapshot export → Spark verify → repair queue
- **State는 진실, Index·Count는 재생성 가능**

### 통합 메시지

> *"완벽한 시스템을 만든 게 아니라, 틀려도 고칠 수 있는 시스템을 만들었습니다."*

---

## ⑥ 핸드오프

```
질문 1.  "이 그래프로 무엇이 가능한가?"
질문 2.  "이걸 프로덕션에서 정말 믿어도 되는가?"
```

```
세션 2 — ○○님
   "이 그래프로 무엇이 가능한가"
   → 멀티홉, 마스터 그래프

세션 3 — ○○님
   "이걸 프로덕션에서 정말 믿어도 되는가"
   → 섀도 테스트, 그리고 신뢰
```

---

## ⑦ 닫기

**담담 버전**:
> 오늘 이 길의 한 구간을 같이 걸어봤습니다.
> 다음 두 분이 그 다음 구간을 안내해 주실 거예요.
> 감사합니다.

---

## 참고 자료

- stories/index.mdx
- stories/use-cases/kakaotalk-gift-wish.mdx
- stories/use-cases/kakaotalk-gift-recent-views.mdx
- stories/use-cases/kakaotalk-friends.mdx
- stories/engineering/pipeline.mdx
- stories/how-we-survived/index.mdx
- stories/how-we-survived/contracts.mdx
- stories/how-we-survived/shadow-testing.mdx
- stories/how-we-survived/migration-verification.mdx
- stories/how-we-survived/hbase-consistency.mdx
- stories/vision/unified-graph.mdx
- GitHub Discussion #32 *"Did We Need a Dedicated Interaction Database — or Did We Overbuild?"*

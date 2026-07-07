# NavigationBar

페이지 상단 고정 바와 그 엔트리를 구성하는 컴포넌트 묶음. 상단 바(NavigationBar) + 엔트리(NavigationBarItem) 두 계층으로 구성되며, 도메인 의도(back / cart / notification …) 는 NavigationBarItem 에 수렴된 **Intent 카탈로그** 에 의해 NavigationBarItem 속성으로 매핑된다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §12 화면 레이어 계층](../../DESIGN_SYSTEM_SPEC.md#12-화면-레이어-계층-screen-layer-hierarchy) — Page Chrome 레이어 위치
> - [SPEC §1.4 Component Token 의 정체성과 두 형태](../../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태) — 본 컴포넌트의 토큰 형태
> - [Components/Navigation/Page.md](./Page.md) — 본 컴포넌트의 호스팅 컨텍스트

---

## 개요

| 컴포넌트 | 역할 | 토큰 네임스페이스 |
|---------|------|-------------------|
| NavigationBar | 슬롯 레이아웃, 고정 높이, 배경, 타이틀 기본 스타일 | `NavigationBar.*` |
| NavigationBarItem | 단일 탭 엔트리(IconButton + 선택적 NumberBadge) 렌더 + 도메인 의도별 엔트리 카탈로그 수렴점 | 전용 없음 (NumberBadge 미정의) |

**핵심 경계**

- NavigationBar 은 left 에 *Intent 카탈로그 엔트리 0~1개*, right 에 *카탈로그에 등록된 엔트리 시퀀스* 를, title 에는 *단일 custom 슬롯* 을 제공한다.
- NavigationBarItem 는 *Intent 카탈로그의 수렴점*. Content (icon · text) 와 NumberBadge (none · count) 직교 축을 렌더하며, 도메인 의도(back / close / share / cart / notification …) 는 NavigationBarItem 에 수렴된 카탈로그에서 엔트리로 생성된다. 별도 래퍼 계층은 두지 않는다. (NumberBadge 미정의 → 전용 토큰 없이 임시 표현.)
- 카탈로그 엔트리는 토큰을 참조하지 않는다. 고정 아이콘 선택과 도메인 상태 → Badge 매핑만 담당한다.
- 구성품은 Component 토큰을 통해서만 값을 소비한다. Semantic 토큰을 직접 참조하지 않는다.

화면 레이어 관점으로는 L2 Page Chrome 에 속한다 ([SPEC §12](../../DESIGN_SYSTEM_SPEC.md#12-화면-레이어-계층-screen-layer-hierarchy)).

---

## NavigationBar

페이지 상단 고정 바. left / right 시퀀스와 title 슬롯의 배치, 좌우 padding, 고정 높이, 배경을 담당한다.

### 역할과 범위

페이지 상단 UI 의 단일 접근점. 페이지 상단 변형(타이틀, 검색, 진행률, 액션 등) 은 본 컴포넌트의 슬롯·variant 로 흡수한다.

**범위 밖:**

- 스크롤 종속 배경 — Page 통합 이슈로 분리
- safe area 처리 — Page 책임

### 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `left` | Left 카탈로그 엔트리 (0 또는 1) | 없음 | 좌측 액션. Left 카탈로그에 등재된 의도만 허용. 최대 1개. |
| `title` | 단일 슬롯 (문자열 또는 custom 구성) | 없음 | **좌측 정렬** — 좌측 항목(아이콘)에 붙고, left 가 없으면 시작 지점에 붙는다. 문자열 타이틀은 `NavigationBar.Title.*` 토큰으로 렌더하며, **left 항목이 없으면 큰 타이틀(`Title.Typo.lg`), 있으면 기본(`Title.Typo.md`)** 으로 크기가 자동 전환된다. custom 슬롯은 호출 측이 스타일 소유. |
| `right` | Right 카탈로그 엔트리 시퀀스 (0..N) | 빈 시퀀스 | 우측 액션. Right 카탈로그에 등재된 의도만 허용. |

속성 조합 예 (의사코드):

```
NavigationBar
  · title: "상품"
  · left:  [ back ]
  · right: [ cart(count: 3), notification(count: 5) ]
```

- `left` / `right` 은 Intent 카탈로그의 엔트리로만 채우는 시퀀스. 호출 측이 raw NavigationBarItem 를 즉석 생성해 꽂는 경로는 제공하지 않는다. 새 의도(bookmark, filter 등) 는 *카탈로그에 등재* 하는 단일 절차로 추가된다.
- `title` 은 유일한 custom 영역. 타이틀 + 서브타이틀, 타이틀 + 진행률 인디케이터 등 도메인별 합성 요구를 흡수한다. 단 NavigationBar 은 외곽 고정 높이 격자와 수직 중앙 정렬을 강제한다.
- `left` 는 0 또는 1개 (단일 엔트리). `right` 엔트리 개수 상한은 현재 명시하지 않는다 — 향후 경험 누적 후 재검토.

### 토큰

색·치수·타이포 등 시각 표현은 `NavigationBar.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다. 타이틀 타이포는 left 유무로 자동 전환(없으면 lg, 있으면 md, ↓ 불변 규칙). 토큰 이름·바인딩·값은 **토큰 소스(`NavigationBarToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

- **Gap = 0**: 엔트리 간 간격은 토큰이 아니라 구조적 고정값(0). 시각 간격은 각 엔트리 외곽 박스가 자체 확보한다.

### 불변 규칙

- Height · Padding · Gap · Background 는 호출자가 변경할 수 없다 (고정값).
- **Gap = 0**: left / right 엔트리들이 붙어 배치된다. 시각적 간격은 각 엔트리의 외곽 박스(고정 크기)가 자체 확보한다. 구성 API 에 간격 조절 훅이 없다는 점으로 강제된다.
- **타이틀 크기 자동 전환**: 문자열 타이틀은 left 항목 유무로 크기가 갈린다 — 없으면 `Title.Typo.lg`(큰 타이틀), 있으면 `Title.Typo.md`. 호출자가 고르는 variant 가 아니라 left 구성에서 파생되는 자동 규칙이며, custom 슬롯에는 적용되지 않는다.
- Style / variant 는 두지 않는다 (위 타이틀 크기 전환은 자동 규칙으로 variant 가 아니다). 단일 형태로 고정되어 있으며 호출자 선택형 변형 도입은 향후 재검토.

---

## NavigationBarItem

상단 바 엔트리의 원자 단위. 하나의 탭 가능한 엔트리를 렌더링한다.

### 역할과 범위

- 아이콘 글리프 또는 짧은 텍스트 라벨 중 하나를 렌더 (Content 축, 배타).
- 그 위에 배지 (none / count) 를 오버레이 (Badge 축, 직교).
- 히트 타깃 크기·전경색·타이포그래피 토큰 소유.
- 도메인 의도 → NavigationBarItem 속성 매핑을 수행하는 *Intent 카탈로그* 호스팅 (카탈로그 엔트리는 토큰을 참조하지 않는다).

**범위 밖:**

- 컨테이너 레이아웃 — 상위 NavigationBar 의 책임
- 도메인 custom 뷰 — 타이틀 슬롯에서만 허용

### 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `content` | `Content` (명세 정의) | — | 필수. `icon` 또는 `text` 배타 선택. ↓ 값 사전. |
| `badge` | `Badge` (명세 정의) | `.none` | Content 위 오버레이. ↓ 값 사전. |
| `onTap` | 행동 | — | 탭 시 발화. |

#### Content (값 사전)

| 값 | 의미 | 비고 |
|----|------|------|
| `.icon(Semantic 아이콘)` | 아이콘 글리프 렌더 | 정사각(고정 항목 높이) |
| `.text(String)` | 짧은 텍스트 라벨 렌더 | 높이 = 고정 항목 높이, 폭 `max(높이, intrinsic)` |

#### Badge (값 사전)

| 값 | 의미 | 비고 |
|----|------|------|
| `.none` | Badge 미렌더 | 기본값 |
| `.count(Int)` | 숫자 캡슐 표시 | 입력 그대로 렌더. NavigationBarItem 단계는 매핑 안 함 — `count(0) → none` 같은 변환은 카탈로그 책임. |

### 구성과 토큰

NavigationBarItem 은 Content (아이콘 또는 텍스트) 위에 선택적 NumberBadge 를 얹은 합성이다.

- **아이콘 엔트리는 IconButton (ghost 변형) 으로 구성**된다 — 아이콘 글리프·탭·히트 타깃·색·크기를 IconButton 이 자기 Kind 어휘·토큰으로 담당한다. 텍스트 엔트리는 탭 가능한 라벨이다.
- 그래서 **NavigationBarItem 전용 토큰 네임스페이스(`NavigationBarItem.*`) 는 두지 않는다** — 아이콘은 IconButton 토큰을, 그 외는 공통 토큰을 따른다.
- **NumberBadge 는 아직 DS 컴포넌트로 정의되지 않았다.** 정식 정의 전까지 *임시 표현*(내부 인라인) 으로 렌더하며 전용 토큰도 두지 않는다. NumberBadge 가 정의되면 그때 합성·토큰을 정합한다.
- NumberBadge 는 *count 기반 의도(cart · notification)* 에만 부여된다. back · close · share 는 배지를 갖지 않는다 (↓ Intent 카탈로그).

### 크기 원칙

NavigationBarItem 의 외곽 박스 크기는 Content 타입에 따라 둘로 갈린다.

| Content | 높이 | 폭 |
|---------|------|------|
| `.icon` | 고정 | 정사각 (아이콘 버튼 크기) |
| `.text` | 아이콘과 동일 고정 높이 | `max(고정 높이, intrinsic)` |

- icon 엔트리는 항상 정사각 (아이콘 버튼이 크기를 결정).
- text 엔트리는 아이콘과 같은 고정 높이, 폭은 최소 고정 폭에서 시작해 텍스트가 길면 intrinsic 으로 확장된다.
- 대부분의 실제 라벨("닫기", "확인", "완료" 등) 은 고정 격자 내에 수렴하므로 아이콘·텍스트 혼재 시에도 격자가 유지된다. 긴 텍스트만 격자가 깨진다.
- Size 는 단일 형태로 고정. 호출자가 Size 를 선택할 수 없다.
- NavigationBar 의 `Gap = 0` 정책과 결합해, left / right 은 예측 가능한 수평 격자를 형성한다.

### 불변 규칙

- Content (icon · text) 와 Badge (none · count) 는 독립 직교 축. Badge 는 Content 위 오버레이이며 Content 의 한 종류가 아니다.
- NavigationBarItem 렌더 경로는 Badge 입력을 *그대로* 렌더한다 — `Badge.none` 이면 미렌더, `Badge.count(n)` 이면 n 을 캡슐로 렌더. `count(0) → none` 같은 매핑은 NavigationBarItem 가 하지 않는다 (카탈로그 책임).
- 카탈로그 엔트리(cart / notification 등) 는 도메인 입력(count) 을 받아 `count > 0` 이면 `Badge.count(count)`, 그 외이면 `Badge.none` 으로 매핑한 뒤 NavigationBarItem 에 전달한다.
- Size · Foreground · Label · Badge 토큰은 모두 호출자가 변경할 수 없다 (고정).

---

## Intent 카탈로그

도메인 의도를 NavigationBarItem 속성으로 매핑하는 *승인된 의도 엔트리의 목록*. 카탈로그는 **슬롯별로 분리** 되어 있다 — left 와 right 이 서로 다른 의도 집합을 허용한다. 좌/우 어느 쪽에서나 임의 의도를 꽂을 수 없게 해, 페이지 상단 UX 의 좌우 의미 구분을 API 수준에서 강제한다.

NavigationBarItem 자체에 수렴된 구조 (별도 래퍼 / 엔트리 계층 없음) 는 유지되며, *어느 카탈로그 엔트리도 토큰을 참조하지 않는다*.

### Left 카탈로그 (좌측 액션)

주로 현재 화면에서 *나가는 방향* 의 의도 (이전 화면 복귀, 모달 닫기 등).

| 의도 | 고정 아이콘 | 도메인 입력 | Badge 매핑 |
|-----|------------|------------|-----------|
| `back` | `Action.back` | — | `none` |
| `close` | `Action.close` | — | `none` |

### Right 카탈로그 (우측 액션)

주로 현재 화면에서 *부가 행동* 을 호출하는 의도 (공유, 장바구니, 알림 등).

| 의도 | 고정 아이콘 | 도메인 입력 | Badge 매핑 |
|-----|------------|------------|-----------|
| `share` | `Action.share` | — | `none` |
| `cart` | `Action.cart` | `count: Int` | `count > 0` → `count(count)` 표시. `count = 0` 이면 Badge 숨김. |
| `notification` | `Action.noti` | `count: Int` | `count > 0` → `count(count)` 표시. `count = 0` 이면 Badge 숨김. |

**공통 Badge 규칙:** count 기반 의도(cart, notification) 는 `count = 0` 일 때 Badge 를 화면에 표시하지 않는다. "0 이라는 값을 UI 에서 명시적으로 보여주는 것이 시각 노이즈" 라는 판단에서 나온 규칙이다. Badge 만 사라지고 *엔트리 자체(아이콘) 는 계속 표시* 된다.

- 이전 버전의 notification 은 `{ none, unseen(=dot), count(n) }` 3 단계 unread 상태를 받았으나, 현 버전에서는 *cart 와 동일한 count 기반 단일 입력* 으로 단순화되었다. "확인 여부를 dot 으로만 표기" 하는 상태는 제외되었으며, 모든 미확인 알림은 count 로 표현된다.
- count 입력은 정수. 음수는 0 으로 취급한다 (또는 도메인 레이어에서 clamping).

### 설계 원칙

- 새 의도는 *슬롯 성격에 맞는 카탈로그에 등재* 한다. left 와 right 의 의도 집합이 교차로 쓰이는 일은 원칙적으로 없다.
- 새 의도(bookmark, filter, profile …) 가 어느 쪽에 속하는지 불명확하면, "나가는 의도인가 / 부가 행동인가" 기준으로 판단. 애매한 경우는 DS 거버넌스에서 결정.
- 카탈로그 엔트리가 토큰을 참조하는 것은 금지. 토큰 참조는 NavigationBarItem 렌더 경로에서만 일어난다.
- 도메인 상태 → 시각 표현 매핑(예: 미확인 알림 수 → Badge) 은 엔트리가 담당한다. NavigationBarItem 렌더 경로는 `count(n)` 을 그대로 그릴 뿐이다 (`count ≤ 0` 이면 Badge 자체가 렌더되지 않는다).
- 카탈로그가 유일한 엔트리 생성 경로이므로, 호출 측이 raw NavigationBarItem 를 즉석 생성해 꽂지 않는다.

---

## 확장 가이드

### 새 의도를 추가할 때

1. NavigationBarItem 렌더 경로가 이미 표현 가능한 조합인지 확인 (대부분 Yes).
2. 의도의 성격 판단: *나가는 방향* 이면 Left 카탈로그, *부가 행동* 이면 Right 카탈로그.
3. 해당 슬롯 카탈로그에 엔트리를 등재. 내용은 *고정 아이콘 + 도메인 상태 → NavigationBarItem 속성 매핑*. 토큰 참조 금지.
4. 본 문서의 해당 카탈로그 표에 한 줄 등재.

### 새 NavigationBarItem 기능이 필요할 때 (신중)

- Content 축과 Badge 축으로 표현할 수 없는 새로운 *시각 요소* 가 생긴 경우에만 NavigationBarItem 를 확장한다.
- 새 슬롯이 필요하면 NavigationBar 측의 슬롯 추가를 먼저 검토.
- NavigationBarItem 확장은 항상 토큰 확장과 쌍으로 이루어진다 (토큰 없는 값 추가 금지).

---

## 미해결 이슈

- Badge count capsule 의 미세 치수 (16pt 등) 은 현재 NavigationBarItem 내부 상수로 유지. 공식 Semantic sizing row 도입은 [SPEC §16.7](../../DESIGN_SYSTEM_SPEC.md#167-dsnavigationbaritem--배지-미세-치수의-semantic-공백) 에서 장기 보류 상태. ([§1.5](../../DESIGN_SYSTEM_SPEC.md#15-토큰화-정책-어디까지-토큰으로-둘-것인가) 도입 후 *컴포넌트 내부 named constant* 로 정합 — 토큰화 의무 영역이 아니다.)
- 투명·숨김 모드, 스크롤 종속 배경 전환 등 상단 바 시각 변형은 현재 버전에서 제외. 복잡도 관리를 위해 단일 형태로 고정하며 필요가 누적되면 재검토. ([SPEC §16.4](../../DESIGN_SYSTEM_SPEC.md#164-dsnavigationbar--스크롤-종속-배경-미구현))

# Section

화면 수준의 콘텐츠 영역을 정의하는 컨테이너. Content 영역의 수평 Padding 과 헤더 / 푸터 슬롯을 관리한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §4 간격 체계](../../DESIGN_SYSTEM_SPEC.md#4-간격spacing-체계) — Padding · Gap 어휘
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 본문 슬롯 / Section 중첩 규칙
> - [SPEC §12 화면 레이어 계층](../../DESIGN_SYSTEM_SPEC.md#12-화면-레이어-계층-screen-layer-hierarchy) — Section 의 화면 위치
> - [Components/Layout/SectionGroup.md](./SectionGroup.md) — 동종 Section 시퀀스 + gap 정책
> - [Components/Layout/Grid.md](../Layout/Grid.md), [Components/Layout/Carousel.md](../Layout/Carousel.md) — 내부 배치

---

## 역할과 범위

Content 영역의 좌우 Padding, 헤더 / 푸터 슬롯을 정의한다. Section 내부에는 표시·배치·도메인 컴포넌트를 자유 조립한다.

**범위 밖:**

- 아이템 배치 — 내부에서 Grid / Carousel 사용.
- 스크롤 — 외부에서 감싼다.

### 중첩 규칙

- *본문 슬롯 직접 자식 불가.* Page 본문 슬롯의 strict 규칙 ([SPEC §11.1](../../DESIGN_SYSTEM_SPEC.md#111-분류)) 상 Section 을 본문 슬롯 직속으로 둘 수 없다. 단일 Section 만 필요한 경우에도 항상 SectionGroup 으로 감싼다 (`SectionGroup { Section { … } }`, 1-원소 SectionGroup).
- *직접 중첩 금지.* Section 의 직속 자식으로 또 다른 Section 을 두지 않는다 (`Section { Section { … } }` 금지).
- *여러 Section 을 시퀀스로 쌓을 때* 는 [SectionGroup](./SectionGroup.md) 안에 배치한다 — gap 정책이 SectionGroup 에 있다.

## 관심사 분리

```
Page (본문 슬롯 strict: SectionGroup, 본문 수직 스크롤 소유)
  └ SectionGroup (gap 소유)
      └ Section
          ├ Header (L1 격자 고정)
          ├ Content (contentPadding 적용)
          │   └ Grid / Carousel (Content 내부 아이템 배치)
          └ Footer (L1 격자 고정)
```

| 관심사 | 담당 |
|--------|------|
| Content 영역의 수평 Padding | Section |
| 헤더 / 푸터 슬롯 렌더, 페이지 L1 격자 고정 | Section |
| Section 간 수직 간격 (gap) | SectionGroup |
| 아이템 간 배치 | Grid / Carousel |
| 스크롤 | Page (본문 수직 스크롤) |

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `header` | `Header` (Preset 또는 custom) | `nil` | 정형화 Preset 또는 자유 구성. ↓ Header 정형화 타입 참조. |
| `content` | (콘텐츠) | — | 필수. 본문 콘텐츠. |
| `footer` | (custom 구성) | `nil` | 자유 구성 푸터. |
| `contentPadding` | `Padding.Horizontal` 토큰 | `.page` | 수평 단일 축. ↓ 상세. |

### contentPadding (horizontal)

Section 은 Content 영역의 *수평 padding 단일 값* 만 노출한다. `left = right` 을 *컴포넌트 불변식으로 강제* ([Header / Footer 의 L1 격자](#header--footer-의-l1-격자) 때문) — 호출부가 좌·우를 따로 줄 방법이 없음, 컴파일 타임에 좌우 대칭 보장.

Section 은 **수직 content padding 개념을 갖지 않는다** — Section 간 수직 리듬은 [SectionGroup 의 `gap`](../Layout/SectionGroup.md) 이 단일 책임자다.

헤더·푸터는 이 값의 영향을 받지 않고 *페이지 L1 격자에 고정* 된다.

> Section 은 좌우 대칭(left = right) 불변식을 API 모양(`horizontal` 단일 파라미터)으로 강제하고, 내부에서 수평 padding 으로 직접 적용한다 ([SPEC §4.4](../../DESIGN_SYSTEM_SPEC.md#44-시스템-전-영역-위치방향-어휘-정책)).

#### horizontal

| 값 | 의미 | 용도 |
|----|------|------|
| `.page` | Padding XL | 일반 콘텐츠 섹션. 기본값. |
| `.none` | 0 | 화면 가장자리까지 확장되는 콘텐츠 (가장자리 스크롤 캐러셀 · 풀-폭 이미지 · 배너 등) |

#### 호출 예 (의사코드)

```
// 기본 — contentPadding 생략: horizontal=.page
Section(header: title("판매자의 다른 상품"))

// 가장자리 스크롤 캐러셀 패턴:
// Content 가로는 가장자리까지 확장, 헤더는 페이지 L1 격자 유지
Section(header: title("추천", accessory: disclosure(title: "더보기", onTap: ...)))
  Carousel(items)
  contentPadding: horizontal=.none
```

> 문서에서는 Padding 어휘만 사용한다. `inset` / `fullBleed` / `edgeToEdge` 같은 플랫폼·인쇄·웹 관용어는 DS 문서 어휘에 등장하지 않는다 ([§4.4](../../DESIGN_SYSTEM_SPEC.md#44-시스템-전-영역-위치방향-어휘-정책)).

## Header / Footer 의 L1 격자

*Header / Footer 의 수평 padding 은 페이지 L1 앵커 (`Padding.Horizontal.page`) 로 고정된다. 값은 존재하지만 소비자 API 에 노출되지 않는다.* Content padding 과 대칭적으로 읽으면:

| 슬롯 | horizontal padding | 조정 가능 여부 |
|-----|-----|-----|
| Content | `.page` (기본) / `.none` | O — `contentPadding(horizontal:)` 노브 공개 |
| Header / Footer | `.page` 고정 | X — 값은 있지만 API 에 노출 안 함 |

- 이유: 섹션마다 헤더 좌측 정렬이 달라지면 페이지 전반의 좌측 격자가 깨진다. 정렬 앵커는 *페이지 전역 규약* 이지 섹션별 조정 축이 아니다.
- `contentPadding(horizontal: .none)` 은 *Content padding 만* `.none` 으로 바꾼다. Header / Footer padding 은 여전히 `.page` 에 고정되어, "타이틀 좌측은 페이지 격자 · 캐러셀 아이템 엣지까지" 구성이 자동으로 성립한다.
- "헤더가 엣지까지 붙어야 하는 화면" 은 L2 Page 섹션 헤더의 범주가 아니라 다른 구성 요소 (전폭 배너, NavigationBar 타이틀 등) — Section 의 Header 슬롯으로 표현하지 않는다.

## Header 정형화 타입 (Preset)

Header 슬롯은 자유 구성뿐 아니라 *정형화된 Title preset* 을 제공한다. 타이틀 문자열은 필수, 그 옆 / 오른쪽 accessory 는 세 가지 중 하나로 고정된다. 다수 페이지에서 반복되는 "섹션 타이틀", "광고성 섹션", "섹션 전체 진입점" 패턴을 카탈로그 수준에서 표준화한다. 정형화 타입은 고정 높이·타이포그래피·전경색 토큰을 소유하며, 호출 측이 값을 직접 조정하지 않는다.

| accessory | 구성 | 용도 |
|-----------|------|------|
| `.none` | 섹션 타이틀 1 개 | 단순 섹션 구분 |
| `.ad` | 섹션 타이틀 + 타이틀 옆 AD 태그 | 광고·프로모션성 섹션임을 고지 |
| `.disclosure(title, onTap)` | 섹션 타이틀 + 우측 [TextButton](../Buttons/TextButton.md) (액션 라벨 + chevronRight) | 섹션 전체 진입점 제공 (전체 보기, 목록으로 이동 등) |

세 variant 모두 *같은 고정 높이* 로 렌더되어 페이지 전반의 섹션 헤더 리듬이 일관된다. 헤더가 자유 구성 (Section custom Header) 으로 쓰일 때도 높이 격자는 동일하게 유지하는 걸 권장한다.

- `.ad` — AD 태그는 *타이틀 문자열 바로 옆* (inline, 타이틀 우측) 에 배치된다. 태그 자체의 시각 스펙은 [AdBadge](../Display/AdBadge.md) 가 소유한다.
- `.disclosure` — 우측 액션은 [TextButton](../Buttons/TextButton.md) 인스턴스로 렌더된다 (액션 라벨 문구는 호출부 지정 · 기본 "더보기" · chevronRight 는 컴포넌트가 강제 표시). 라벨 typography / 색 / 라벨↔chevron 간격은 `TextButtonToken` 이 결정 — Section preset 토큰 (`Section.Preset.Header.action.*`) 은 더 이상 소비되지 않는다.

### 자유 구성 vs 정형화 — 선택 기준

| 상황 | 권장 |
|------|------|
| 단순 타이틀 / 광고 고지 / 전체 보기 액션 | 정형화 Title preset (`.none` · `.ad` · `.disclosure`) |
| 카운트·칩·토글·검색 필드 등 제 3 요소가 상단에 필요 | 자유 구성 |

정형화 타입으로 표현 가능한 경우는 우선 정형화 타입을 사용해, 페이지 간 섹션 헤더 표현을 통일한다. 자유 구성은 정형화로 표현할 수 없는 합성 요구를 흡수하는 예외 경로.

## 토큰

색·치수·타이포 등 시각 표현은 `Section.*` (헤더 preset 포함) Component Token 이 결정하며, 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`SectionToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

토큰 네임스페이스:

- **Horizontal (`Padding.Horizontal.*`)** — Section 내부가 수평 padding 을 그릴 때 참조하는 토큰 네임스페이스. Section 이 3 개 슬롯을 구현상 공통으로 이 네임스페이스에서 소비하므로 `Content.` 로 한정하지 않는다. left = right 불변식이 있어 *축 단위* (`Horizontal`) 로 묶는다. 소비자 API 와는 별개 층:
  - **Content** — 소비자가 `contentPadding(horizontal:)` 로 `.none` / `.page` 중 택 1, Section 이 그 선택을 해당 토큰으로 매핑.
  - **Header / Footer** — 소비자 API 없음. Section 내부가 `Padding.Horizontal.page` 상수를 직접 참조해 고정 적용.

`contentPadding(horizontal:)` API 는 이름 그대로 *Content 수평 전용 노브*. Header / Footer 는 이 API 의 영향권 밖이며, 내부적으로 별도 경로로 `Padding.Horizontal.page` 상수에 고정된다 — 페이지 정렬 앵커 불변식 유지를 위해.

### Header Preset

- 타이틀은 *1 줄 · 꼬리 잘림 (truncate tail)* 을 강제한다. 라벨이 길면 ellipsis.
- `.disclosure` 의 액션 영역 (라벨 typography / 색 / 라벨↔chevron 간격 / chevron size · color) 은 [TextButtonToken](../Buttons/TextButton.md#토큰) 이 소유한다 — Section Header preset 에는 더 이상 액션용 토큰이 없다.
- 호출부에서 액션 라벨 문구를 지정할 수 있다 (기본값 "더보기"). 도메인별 표현 (예: "전체 보기", "관리") 을 허용.

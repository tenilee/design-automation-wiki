# TwoColumnProductCard

2열 그리드용 상품 카드. 썸네일(찜·배지 오버레이) + 정보(할인·가격·이름·날짜·카운트)를 세로로 합성한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [Components/Domain/ProductThumbnail.md](./ProductThumbnail.md) — 썸네일 base
> - [Components/Layout/Grid.md](../Layout/Grid.md) — 반복 배치 컨테이너

---

## 역할과 범위

2열 그리드에 반복 배치되는 상품 카드. 상품 썸네일 영역과 정보 영역을 세로 합성하며, 정보는 `Info` 로 받는다.

**범위 밖:**

- 그리드 배치 — Grid 가 반복 배치.
- 찜 상태 보유 — 호출 측 `Binding`.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `imageURL` | URL | — | 필수. 상품 사진. |
| `favorite` | `Binding<Bool>` | — | 필수. 찜 상태(양방향). |
| `brand` | `ProductCardBrandBadge?` (edition1 · care · mercari) | 없음 | 브랜드 배지(좌상) 종류. nil = 미표시. |
| `video` | Bool | `false` | 영상 배지(좌하). |
| `adBadge` | Bool | `false` | 광고 배지(우하). |
| `info` | `Info` | — | 필수. 하단 정보. ↓ Info. |

### Info

| 항목 | 타입 | 비고 |
|------|------|------|
| `name` | 문자열 | 상품명(1줄). |
| `price` | 정수(원) | 가격. 전체 원화 표기(예: 1,234,000원). |
| `discount` | 정수? | 할인율(%). nil = 미표시. |
| `todayDeal` | Bool | 오늘특가 배지. |
| `date` | 문자열 | 등록 시점(예: 1시간 전). 필수 — 항상 표시. |
| `favoriteCount` | 정수 | 찜 수. 0 이면 미표시. |
| `talkCount` | 정수 | 톡 수. 0 이면 미표시. |

## 토큰

색·치수·타이포 등 시각 표현은 `ProductCard.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값의 단일 출처는 `ProductCardToken` / Token Studio.

## 불변 규칙

- 썸네일 종횡비 4:5 고정. 정보 영역은 썸네일 아래 세로 배치.
- 정보 배치·타이포·색·간격은 `ProductCard.*` 토큰 고정 — 호출자 변경 불가.
- `date` 는 필수 — 항상 표시. `favoriteCount` · `talkCount` 는 0, `discount` 는 nil 이면 미표시.

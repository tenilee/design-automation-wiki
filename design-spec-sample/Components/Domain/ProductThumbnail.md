# ProductThumbnail

상품 썸네일 **base**. `Image`(product placeholder · `fill`) 위에 `Shade`(topToBottom) 를 얹고 `ratio`(4:5 / 1:1)로 종횡비를 정한다. 찜·배지 등 오버레이는 갖지 않는다 — 상위 상품카드가 합성한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [Components/Display/Image.md](../Display/Image.md) — 기반 이미지
> - [Components/Display/Shade.md](../Display/Shade.md) — 오버레이 셰이드

---

## 역할과 범위

상품 사진을 종횡비에 맞춰 표시하는 도메인 썸네일 base. `Image`(product placeholder) 위에 `Shade` 를 얹은 순수 합성이며, **오버레이(찜·배지 등)는 갖지 않는다**.

**범위 밖:**

- 찜·광고·브랜드·비디오 배지 — 상위 ProductCardThumbnail(상품카드 내부)이 합성.
- 판매 상태·상품 정보 — 상품카드의 정보 영역.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `imageURL` | URL | — | 필수. 상품 사진(로딩 중 product placeholder). |
| `ratio` | `Ratio` (명세 정의) | `.fourToFive` | 4:5 / 1:1. ↓ 값 사전. |

### Ratio

| 값 | 종횡비 |
|----|------|
| `.fourToFive` (기본값) | 4:5 |
| `.oneToOne` | 1:1 |

## 토큰

종횡비는 `ProductThumbnail.Container.ratio*` Component Token 이 결정하며 호출 측은 주입하지 않는다. 이미지·셰이드의 시각 표현은 각각 `Image`·`Shade` 컴포넌트의 토큰을 따른다. 토큰 이름·바인딩·값의 단일 출처는 `ProductThumbnailToken` / Token Studio.

## 불변 규칙

- 구성은 `Image`(product placeholder · fill) + `Shade`(topToBottom) 로 고정.
- 오버레이(찜·광고·브랜드·비디오 배지)는 base 가 아니라 상위 상품카드(내부 ProductCardThumbnail)의 책임.

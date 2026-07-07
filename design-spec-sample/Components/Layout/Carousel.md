# Carousel

수평 연속 스크롤 배치 컨테이너. 동종 반복 아이템을 수평으로 배치하고 자유롭게 스크롤한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [Components/Layout/Section.md](../Layout/Section.md) — 호스팅 컨테이너 (가장자리 확장 패턴)
> - [Components/Layout/Grid.md](./Grid.md) — 자매 Layout

---

## 역할과 범위

Layer Y 축의 Arrangement 레이어이므로 항상 [Section](../Layout/Section.md) 의 content 안에 배치한다. Page 본문 슬롯의 직접 자식으로 둘 수 없다 ([SPEC §11.1](../../DESIGN_SYSTEM_SPEC.md#111-분류) strict 규칙). 본문이 단일 캐러셀인 페이지도 `SectionGroup { Section { Carousel { … } } }` 골격을 유지한다.

**자식 규칙:** 자식은 반복 단위 (동종 아이템). 이종 의미 단위 (Section 등) 를 자식으로 두지 않는다.

**범위 밖:**

- 아이템 UI.
- 섹션 수준 여백 — [Section](../Layout/Section.md) 사용.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `gap` | `Gap` (명세 정의) | `.md` | Carousel 의 축은 항상 수평이므로 `gap` = Inline `GapX`. ↓ 값 사전. |

### Gap

| 값 | 의미 |
|----|------|
| `.none` | 0 |
| `.md` | `GapX.md`. 기본값. |

## Item 폭 정책

Carousel 은 *Item 의 폭에 관여하지 않는다.* 각 아이템은 자신의 *intrinsic 폭* (자체 디자인이 결정한 폭) 으로 렌더된다.

- *균일 폭* 이 필요한 경우 → Item 타입이 자체적으로 고정 폭을 보유 (예: 상품 카드가 내부 spec 으로 160pt 폭 보유).
- *Peek 동작* 도 같은 원리 — Item 이 컨테이너보다 좁은 균일 폭으로 렌더되면 자연스럽게 다음 아이템 일부가 화면 너머로 보임.

Container (Carousel) 와 Item 의 책임 분리: *Carousel = 레이아웃, Item = 자체 크기*.

## 좌·우 여백 정책

Carousel 의 좌·우 여백은 *내부 고정값* (`Padding.xl`) 으로 항상 적용된다. 호출자 API 로 노출되지 않으며 변경 불가.

호스팅 Section 은 반드시 `contentPadding(horizontal: .none)` 으로 둘 것 — Section 의 horizontal padding 과 *이중 적용* 되는 것을 방지. 이 결합이 *풀 블리드 + 페이지 여백 + peek* 패턴을 자동으로 표현한다.

스크롤은 연속적이며 스냅하지 않는다.

## 토큰

색·치수·타이포 등 시각 표현은 `Carousel.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`CarouselToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

## 배치 패턴

*풀 블리드 + 페이지 여백 + peek* 단일 패턴으로 동작한다. 호스팅 Section 의 `contentPadding(horizontal: .none)` 와 Carousel 의 *내부 고정 padding (.page)* 가 결합되어 다음을 표현:

- 첫 아이템 좌측 = 페이지 여백 (`Padding.page`)
- 스크롤 영역 = 화면 우측 끝까지 닿음
- 우측 peek = 다음 아이템 일부가 화면 너머로 살짝 보임

```
Section(header: ..., contentPadding: horizontal=.none, vertical=.md)
  Carousel(gap: .md)
    ...
```

- Section 의 `contentPadding(horizontal: .none)` 으로 Section content 영역을 화면 가장자리까지 확장.
- Carousel 의 좌·우 inset 은 *내부에서 자동 적용* — 호출자 코드에 등장하지 않음.
- Section Header / Footer 는 *여전히 L1 격자 (Padding.xl)* 에 정렬 — 타이틀 좌측과 첫 카드 좌측이 같은 선에 맞춰진다.

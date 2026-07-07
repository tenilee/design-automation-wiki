# SectionGroup

동종 Section 시퀀스를 수직으로 배치하고 *gap* 정책을 보유하는 컴포넌트.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 본문 슬롯 모델 / Section 중첩 규칙
> - [SPEC §16.3 수직 간격 토큰의 Semantic 카테고리 불일치](../../DESIGN_SYSTEM_SPEC.md#163-수직-간격-토큰의-semantic-카테고리-불일치-해결-방향-확정--반영-대기) — gap 토큰의 GapY 이관 대기
> - [Components/Layout/Section.md](./Section.md) — 자식 컴포넌트
> - [Components/Navigation/Page.md](../Navigation/Page.md) — 본문 슬롯 호스트

---

## 역할과 범위

여러 Section 을 수직으로 쌓고, Section 사이에 일정한 gap 토큰을 적용한다.

**자식 규칙:** *Section 만 자식으로 받는다.* 이종 의미 단위 (다른 컨테이너, 자유 콘텐츠) 를 자식으로 두지 않는다.

**범위 밖:**

- 좌우 여백 — Section 의 contentPadding 또는 외부 슬롯 소유자가 결정.
- 스크롤 — 외부에서 감싼다.
- 본문 슬롯 자체 보유 — 본문 슬롯 안에 *호출부가 명시적으로 배치* 하는 컨테이너이지, 자체 슬롯 소유자가 아님.

SectionGroup 은 Page / Sheet / Drawer 어디에 놓여도 동일한 gap 정책으로 작동한다. Page 본문 슬롯 strict 규칙 ([SPEC §11.1](../../DESIGN_SYSTEM_SPEC.md#111-분류)) 상 본문 슬롯의 자식은 `SectionGroup` 만이다. 단일 Section 만 필요한 페이지도 항상 `SectionGroup { Section { … } }` (1-원소 SectionGroup) 으로 감싸 골격 통일성을 유지한다.

## 다른 컴포넌트와의 구분 — 왜 별도 컴포넌트인가

Page 가 직접 gap 을 보유하면 책임 경계가 흐려진다 — Section 시퀀스 정책은 "Section 들이 쌓일 때만" 의미가 있어, 정책이 적용될 자리가 없는 다른 본문 슬롯 owner (Sheet / Drawer 등) 에까지 같은 추론이 되풀이된다. 그래서:

- **Page** — 본문 슬롯은 strict (`SectionGroup` 만). 자식의 내부 정책엔 무관심.
- **SectionGroup** — Section 시퀀스 정책 (gap) 보유. 자식 = Section 만.

이 분리로 정책이 한 컴포넌트에만 존재해 변경 시 한 곳만 수정하면 되고, Page / Sheet / Drawer 어디에 놓이든 동일하게 작동한다.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `gap` | `Gap` (명세 정의) | `.md` | none / sm / md / lg. ↓ 값 사전. |

### Gap

값: `.none` · `.sm` · `.md`(기본값) · `.lg` — 섹션 간 수직 간격 단계 (작음→큼).

> Section 시퀀스 축은 수직 stack 고정이라 API 이름은 `gap` (축 suffix 생략, [§4.3](../../DESIGN_SYSTEM_SPEC.md#43-슬롯-네이밍-규칙)).

## 사용 예 (의사코드)

### 페이지 본체가 Section 시퀀스인 경우

```
Page(
  navigationBar: ...,
  content:
    SectionGroup
      Section(header: title("추천"))
        Carousel ...
      Section(header: title("판매자의 다른 상품"))
        Grid(columns: two) ...
      Section ...
)
```

### gap 변경

```
SectionGroup
  Section
  Section
  gap: .none
```

## 토큰

색·치수·타이포 등 시각 표현은 `SectionGroup.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`SectionGroupToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

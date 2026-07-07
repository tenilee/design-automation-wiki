# Page

페이지 레벨 컴포넌트(L2). NavigationBar, *strict 본문 슬롯*, 선택적 bottom 슬롯을 조립하고, 페이지 수준 정책 (배경, 하단 safe-area 경계) 을 소유한다. 본문은 항상 수직 스크롤 가능.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 본문 슬롯 모델
> - [SPEC §12 화면 레이어 계층](../../DESIGN_SYSTEM_SPEC.md#12-화면-레이어-계층-screen-layer-hierarchy) — L2 Page Chrome 위치
> - [Components/Navigation/NavigationBar.md](./NavigationBar.md) — 상단 슬롯 자식
> - [Components/Layout/SectionGroup.md](../Layout/SectionGroup.md) — Section 시퀀스 + gap 정책
> - [Components/Layout/Section.md](../Layout/Section.md) — 콘텐츠 영역 정의

---

## 역할과 범위

L2 페이지 구조 정의 (상단 바 + strict 본문 슬롯 + 옵션 하단 슬롯), 스크롤 컨테이너 제공, 하단 safe-area 규약 적용.

**범위 밖:**

- *Section 시퀀스 리듬 (gap)* — SectionGroup 이 보유.
- 섹션 영역 정의 — Section.
- 아이템 배치 — Grid / Carousel.
- 상단 바 내부 UI — NavigationBar.
- *bottom 슬롯 내부 콘텐츠 구성* — feature 화면이 자유롭게 조합. Page 는 슬롯 공간만 제공.
- *Splash · 풀스크린 이미지 뷰어 · 부팅 화면 등 본문 규칙에 맞지 않는 화면* — L2 Page 가 아니라 다른 Shell 카테고리 (L1 BootShell / L3 Sheet / L4 Overlay) 의 영역. [SPEC §12](../../DESIGN_SYSTEM_SPEC.md#12-화면-레이어-계층-screen-layer-hierarchy) 참조.

## 구성

| 슬롯 | 필수 | 수용 타입 |
|------|------|----------|
| `navigationBar` | O | NavigationBar 타입 고정 |
| `content` | O | strict 본문 슬롯 — `SectionGroup` 만 |
| `bottom` | X | 자유 View 슬롯 (feature 소유) |

### navigationBar — 타입 고정

- NavigationBar 만 받는다. 임의 구성 요소를 직접 꽂을 수 없다.
- L2 Page Chrome 의 일관된 계약 (상단 여백·안전 영역·스크롤 기준점) 이 페이지마다 달라지지 않아야 하므로 필수.
- "상단 바가 전혀 없는 화면" 이 필요한 경우는 L2 Page 가 아니라 L3 Sheet 또는 L4 Overlay 의 범주.

### content — strict 본문 슬롯

Page 의 본문 슬롯은 *`SectionGroup` 만* 받는다 — 타입 (`PageBodySlot` marker) 으로 컴파일 타임 강제.

| 자식 | 판단 | 의도 |
|------|------|------|
| `SectionGroup` | 수용 | Section 시퀀스 + gap 정책. 1-원소 SectionGroup 도 동일 골격 유지 |
| 단일 `Section` | 거부 | 항상 `SectionGroup { Section { … } }` 로 감싼다 (1-원소 SectionGroup) |
| `Grid` / `Carousel` 직접 | 거부 | Arrangement 는 항상 Section 안. 본문 슬롯 직접 = 레이어 스킵 |
| 단일 Atom / Item | 거부 | 의미 영역 (Section) 없이 콘텐츠를 직접 띄우지 않는다 |
| 자유 View | 거부 | Splash 류는 다른 Shell 카테고리로 |

> **빈 상태 / 오류 상태 / 로딩 상태 분기는 본문 골격이 아니라 `Section` 의 content 안에서 처리한다.** Page → SectionGroup → Section 골격은 상태와 무관하게 동일하게 유지하고, loaded / empty / error / loading 분기는 Section.content 안에서 일어난다 — 이때 EmptyState · ErrorState 등 Item 레이어 컴포넌트가 일반 Item 처럼 들어간다. 자유 View 예외 경로는 만들지 않는다.

#### 대표 시나리오 (의사코드)

**(1) Section 시퀀스 — 가장 흔한 케이스**

```
Page(
  navigationBar: ...,
  content:
    SectionGroup
      Section(header: title("추천"))
      Section
      Section
)
```

**(2) 본문이 단일 반복 배치인 페이지 — 1-원소 SectionGroup 으로 감싼다**

검색 결과처럼 본문이 하나의 반복 배치인 화면도 동일 골격을 유지한다.

```
Page(
  navigationBar: ...,
  content:
    SectionGroup
      Section
        Grid(columns: two)
)
```

**(3) 상태 기반 콘텐츠 분기 — loaded / empty / error 모두 동일 골격**

```
Page(
  navigationBar: ...,
  content:
    SectionGroup
      Section
        switch viewState:
          loaded(items): Grid(items) { ProductTwoColumn }
          empty:         EmptyState(icon: search, title: "검색 결과가 없어요")
          error:         ErrorState(title: "잠시 후 다시 시도해 주세요", retry: ...)
)
```

> **왜 gap 정책이 Page 가 아니라 SectionGroup 에 있는가?** Section 시퀀스 리듬은 "Section 들이 쌓일 때만" 의미가 있다. 정책을 Section 시퀀스 컴포넌트(SectionGroup)에만 두면 Page / Sheet / Drawer 어디에 놓이든 동일하게 작동한다.

### bottom — 자유 슬롯

- *타입 제약 없이 임의 View 를 수용* 하는 슬롯. 주된 용례는 CTA 버튼, 탭 바, 키보드 액세서리, 페이지 수준 인디케이터 등.
- Page 는 공간·안전 영역 경계만 관리하고, 슬롯 내부의 레이아웃·토큰 사용은 *feature 화면이 소유*.
- CTA 가 없는 읽기 중심 페이지에서는 bottom 을 아예 생략한다.
- 이 비대칭 (상단 타입 고정 / 본문 strict / 하단 자유) 은 의도된 것이다. 상단·본문은 페이지 골격의 일관성을 보장해야 하지만, 하단은 기능별 요구가 매우 다양해 DS 가 미리 규정하기 어렵다.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `backgroundColor` | `BackgroundColor` (명세 정의) | `.basement` | 선택 제한. ↓ 값 사전. |
| `bottomPadding` | `BottomPadding` (명세 정의) | `.none` | 본문(SectionGroup) 하단 콘텐츠 여백. ↓ 값 사전. |

### BackgroundColor

| 값 | 용도 |
|----|------|
| `.basement` | 기본 페이지 배경. 기본값. |
| `.neutralWeak` | 콘텐츠 중심 페이지 |

### BottomPadding

본문 콘텐츠(SectionGroup) 하단에 적용되는 여백 — `bottom` 슬롯 자체의 여백이 아니다. 스크롤 콘텐츠 바닥과 화면 하단(또는 bottom 슬롯) 사이 간격을 확보한다.

| 값 | 용도 |
|----|------|
| `.none` | 하단 여백 없음. 기본값. |
| `.md` | 중간 하단 여백 |
| `.lg` | 큰 하단 여백 |

## 토큰

색·치수·타이포 등 시각 표현은 `Page.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`PageToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

## 페이지 배경 원칙

- 임의 컬러 주입은 허용하지 않는다 (freeform 값 주입 금지).
- 새 배경이 필요하면 `Page.BackgroundColor` 에 프리셋을 *공식 추가* 하고 근거를 남긴다.

## Bottom 슬롯 사용 가이드

bottom 은 자유 슬롯이지만, 일관성을 위해 다음 원칙을 *권고* 한다 (강제는 아님):

- *토큰 범위 준수* — 슬롯 내부에서 사용하는 색·타이포그래피·간격은 DS 토큰 체계 안에서 선택한다.
- *CTA 단일성* — 의사 결정 CTA 는 한 번에 하나를 기본으로 한다. 보조 액션이 있다면 시각 위계를 명확히 둔다.
- *고정 높이 지양* — bottom 내용 높이는 콘텐츠에 따라 자연스럽게 결정되도록 한다.
- *safe-area 고려* — Page 가 하단 safe-area 를 반영해 슬롯을 배치한다. feature 측은 슬롯 내부의 상대 배치만 고려하면 된다.

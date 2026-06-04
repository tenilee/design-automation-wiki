# Component Contracts

> Platform Design System의 화면 조합용 컴포넌트 Contract 목록입니다.
> Screen Brief의 `component` 값은 이 문서의 컴포넌트 이름과 일치해야 합니다.

---

## 기준

- Figma Design System: https://www.figma.com/design/wJaqzyxIazITPQ7ULFJY4o/Platform-Design-System?node-id=37-22154
- Token output: `colorMode/light.css`, `colorMode/dark.css`
- Token naming: dot path convention (예: `adBadge.overlay.container.bg.default`)
- Contract convention: `Component_Contract_Convention.md`

---

## 목차

- [Page](#page)
- [Section Group](#section-group)
- [Section](#section)
- [navigationBar](#navigationbar)
- [sectionHeader](#sectionheader)
- [productCardGrid](#productcardgrid)
- [productCardCarousel](#productcardcarousel)
- [productCard](#productcard)
- [Grid](#grid)
- [Carousel](#carousel)
- [textBlock](#textblock)
- [image](#image)
- [button](#button)
- [Text Button](#text-button)
- [Badge](#badge)
- [Ad Badge](#ad-badge)
- [Icon Count](#icon-count)
- [Toggle - textIconToggle](#toggle---texticontoggle)
- [Toggle - iconToggle](#toggle---icontoggle)
- [Toggle - textToggle](#toggle---texttoggle)
- [DSIcon](#dsicon)
- [SlotPlaceholder](#slotplaceholder)

---

## Page

```
Status: Core
Purpose: 모바일 화면의 기본 페이지 컨테이너입니다.
Use when: 화면 전체의 배경, navigationBar, Section Group을 하나의 페이지 구조로 조립할 때.
Avoid when: 독립 카드, 모달, 부분 영역만 구성할 때는 사용하지 않습니다.
Key props:
- backgroundColor [required] basement | neutralWeak
  -> basement: 기본 흰 배경, neutralWeak: 약한 회색 배경
Composition:
- navigationBar [0-1] (항상 최상단 고정)
- Section Group [1]
Order: fixed (navigationBar -> Section Group)
Cannot contain: modal, bottomSheet
Owner: Platform Design System
```

---

## Section Group

```
Status: Core
Purpose: 여러 Section을 수직으로 쌓아 화면의 본문 영역을 구성하는 컨테이너입니다.
Use when: Page 내부에서 Section들을 묶어 배치할 때. Section이 하나여도 Section Group을 사용합니다.
Avoid when: Section 없이 단독으로 사용하지 않습니다.
Key props:
- gap-y [required] none | sm | md | lg -> Section 간 수직 간격
- count [required] 1 | 2 | 3 | 4 | 5 -> 포함할 Section 수
Composition:
- Section [1-5]
Order: fixed
Owner: Platform Design System
```

---

## Section

```
Status: Core
Purpose: 화면을 구성하는 단위 블록으로, header / content / footer 세 개의 슬롯을 가진 컨테이너입니다.
Use when: 화면 내 독립된 콘텐츠 영역을 구분할 때.
Avoid when: 단순 여백 조정 목적으로는 사용하지 않습니다.
Key props:
- contentPaddingX [required] page | none
  -> page: layout.page.paddingHorizontal
  -> none: spacing.padding.none
- contentPaddingTop [optional] none | sm | md | lg | xl
- contentPaddingBottom [optional] none | sm | md | lg | xl
- showHeader [optional] boolean -> 기본값 true, false면 headerArea 숨김
- showFooter [optional] boolean -> 기본값 true, false면 footerArea 숨김
Composition:
- headerSlot [0-1]: sectionHeader 권장, custom 가능
- contentSlot [1]: Grid | Carousel | productCardGrid | productCardCarousel | image | textBlock | button | SlotPlaceholder
- footerSlot [0-1]: Grid | Carousel | productCardGrid | productCardCarousel | button | SlotPlaceholder
Order: fixed (headerSlot -> contentSlot -> footerSlot)
Owner: Platform Design System
```

---

## navigationBar

```
Status: Core
Purpose: 화면 최상단에 위치하는 네비게이션 바로, 타이틀과 좌우 액션 버튼을 포함합니다.
Use when: 모든 일반 모바일 화면의 상단 네비게이션 영역.
Avoid when: 모달, 바텀시트 내부 헤더에는 사용하지 않습니다.
Key props:
- title [optional] string -> 없으면 타이틀 빈 영역
- titleSize [optional] lg | md -> 기본값 md
- leftItem [required] none | 1 -> 좌측 버튼 개수
- rightItem [required] none | 1 | 2 | 3 -> 우측 버튼 개수
- leftItem1 [optional] navigationBarItem-back | navigationBarItem-menu
  -> leftItem=1일 때 작성
- rightItem1 [optional] navigationBarItem-cart | navigationBarItem-noti | navigationBarItem-share
  -> rightItem>=1일 때 작성
- rightItem2 [optional] navigationBarItem-cart | navigationBarItem-noti | navigationBarItem-share
  -> rightItem>=2일 때 작성
- rightItem3 [optional] navigationBarItem-cart | navigationBarItem-noti | navigationBarItem-share
  -> rightItem=3일 때 작성
Composition: none
Owner: Platform Design System
```

**Screen Brief 입력 매핑**
- `leftItem: back` -> `leftItem=1`, `leftItem1=navigationBarItem-back`
- `leftItem: menu` -> `leftItem=1`, `leftItem1=navigationBarItem-menu`
- `rightItems` 배열 길이 -> `rightItem`
- `rightItems` 값 -> 순서대로 `rightItem1`, `rightItem2`, `rightItem3`

---

## sectionHeader

```
Status: Core
Purpose: 섹션 제목과 선택적 우측 액션을 표시하는 헤더입니다.
Use when: Section의 headerSlot에서 섹션 제목이 필요한 경우.
Avoid when: 페이지 최상단 타이틀에는 navigationBar를 사용합니다.
Key props:
- variant [required] base | disclosure | ad
  -> base: 제목만 표시
  -> disclosure: 제목 + 우측 더보기 버튼(Text Button)
  -> ad: 제목 + 우측 광고 배지(Ad Badge)
- text [required] string -> 섹션 제목
- actionText [optional] string -> variant=disclosure일 때 Text Button에 입력
Composition: none
Token prefix: sectionHeader.*
Owner: Platform Design System
```

---

## productCardGrid

```
Status: Core
Purpose: productCard를 2열 또는 3열 그리드로 배열한 화면 조합용 컴포넌트입니다.
Use when: 상품 목록 화면, 큐레이션 화면의 상품 그리드 섹션.
Avoid when: 가로 스크롤 상품 목록에는 productCardCarousel을 사용합니다.
Key props:
- grid [required] two-column | three-column
  -> two-column: 상품을 크게 보여줄 때, 4-6개 권장
  -> three-column: 상품을 많이 보여줄 때, 6-9개 권장
- products [required] ProductData[] -> ProductCard 인스턴스에 바인딩할 상품 데이터
Composition:
- productCard [2+]
Order: flexible
Owner: Platform Design System
```

**ProductData**

```
- image [required] url
- title [required] string
- price [required] number
- originalPrice [optional] number
- discountRate [optional] number
- condition [optional] 새상품 | 중고
- brandBadge [optional] edition1 | mercari | care
```

---

## productCardCarousel

```
Status: Core
Purpose: productCard를 가로 스크롤로 나열한 화면 조합용 컴포넌트입니다.
Use when: 관련 상품, 추천 상품처럼 가로 스크롤로 탐색하는 상품 목록.
Avoid when: 세로 목록 형태의 상품 나열에는 productCardGrid를 사용합니다.
Key props:
- products [required] ProductData[] -> ProductCard 인스턴스에 바인딩할 상품 데이터, 3개 이상 권장
Composition:
- productCard [3+]
Order: flexible
Owner: Platform Design System
```

---

## productCard

```
Status: Core
Purpose: 상품 썸네일, 가격, 할인율, 브랜드 배지를 조합해 상품 하나를 표현하는 카드입니다.
Use when: productCardGrid 또는 productCardCarousel 내부에서 개별 상품을 표시할 때.
Avoid when: 상품 상세 페이지 메인 영역에는 사용하지 않습니다.
Key props:
- column [required] two-column | three-column
  -> productCardGrid의 grid prop과 반드시 일치시킵니다.
- image [required] url -> thumbnail image fill
- title [required] string -> title label
- price [required] number -> price label
- originalPrice [optional] number
- discountRate [optional] number -> originalPrice가 있으면 함께 작성
- condition [optional] 새상품 | 중고
- brandBadge [optional] edition1 | mercari | care
Composition: none (thumbnail + product info 내부 구조 고정)
Owner: Platform Design System
```

---

## Grid

```
Status: Core
Purpose: 동일한 컴포넌트를 격자 형태로 배열하는 범용 레이아웃 컨테이너입니다.
Use when: ProductCard 외 다른 컴포넌트를 그리드로 배열해야 할 때.
Avoid when: ProductCard 전용 그리드는 productCardGrid를 사용합니다.
Key props:
- columns [required] two | three
- rows [required] 2 | 3 | 4 | 5 | 6
- content [required] -> SlotPlaceholder 자리에 실제 컴포넌트 INSTANCE_SWAP
Composition:
- content [(columns x rows)개]
Order: flexible
Owner: Platform Design System
```

---

## Carousel

```
Status: Core
Purpose: 동일한 컴포넌트를 가로 스크롤로 나열하는 범용 레이아웃 컨테이너입니다.
Use when: ProductCard 외 다른 컴포넌트를 캐러셀로 배열해야 할 때.
Avoid when: ProductCard 전용 캐러셀은 productCardCarousel을 사용합니다.
Key props:
- gap [required] md | none
  -> none: 아이템 간 여백 없음, 풀블리드 이미지 캐러셀 등에 사용
- content [required] -> SlotPlaceholder 자리에 실제 컴포넌트 INSTANCE_SWAP
Composition:
- content [1+]
Order: flexible
Owner: Platform Design System
```

---

## textBlock

```
Status: Core
Purpose: 카테고리, 제목, 본문 텍스트를 조합한 에디토리얼 텍스트 블록입니다.
Use when: 큐레이션 화면의 에디터 노트, 스토리텔링 텍스트 섹션.
Avoid when: 단순 안내 문구나 상품 설명에는 사용하지 않습니다.
Key props:
- variant [required] editorial
- category [required] string -> 상단 카테고리 레이블
- title [required] string -> 2줄 이내 권장
- body [required] string -> 3-5줄 권장
Composition: none
Owner: Platform Design System
```

---

## image

```
Status: Core
Purpose: 콘텐츠 이미지를 fill 또는 fit 모드로 표시합니다.
Use when: 히어로 이미지, 에디토리얼 이미지 등 콘텐츠 이미지가 필요한 모든 영역.
Avoid when: 아이콘 이미지에는 DSIcon을 사용합니다.
Key props:
- contentMode [required] fill | fit
  -> fill: 영역을 꽉 채움, 일부가 잘릴 수 있음
  -> fit: 원본 비율 유지, 여백이 생길 수 있음
- placeholder [required] default | product | store
  -> 이미지 로딩 전 표시할 플레이스홀더 유형
- src [optional] url -> 자동화가 image fill을 교체하기 위한 데이터, Figma variant prop이 아님
- aspectRatio [optional] square | wide | tall -> Section 프레임 크기 계산용 힌트
Composition: none
Owner: Platform Design System
```

---

## button

```
Status: Core
Purpose: 사용자의 단일 액션을 트리거하는 버튼입니다.
Use when: 폼 제출, 화면 이동, 기능 실행 등 명확한 액션이 필요할 때.
Avoid when: 텍스트 링크처럼 인라인으로 들어가야 할 때는 Text Button을 사용합니다.
Key props:
- variant [required] brandSolid | neutralSolid | brandWeak | neutralWeak | positiveWeak
  -> brandSolid: 주요 CTA, 구매/신청 등
  -> neutralSolid: 일반 주요 액션
  -> brandWeak | neutralWeak: 보조 액션, 더보기
  -> positiveWeak: 긍정적 보조 액션
- size [required] size-xs | size-sm | size-md | size-lg | size-xl
- text [required] string
- width [optional] full | auto -> 기본값 auto
- enabled [optional] boolean -> 기본값 true, false면 disabled 상태
Composition: none
Owner: Platform Design System
```

---

## Text Button

```
Status: Core
Purpose: 우측 화살표(chevron-right)를 포함한 인라인 텍스트 링크 버튼입니다.
Use when: sectionHeader의 disclosure 영역, 더보기 링크 등 텍스트 형태의 보조 액션.
Avoid when: 주요 CTA나 단독 버튼에는 button 컴포넌트를 사용합니다.
Key props:
- text [required] string
Composition: none
Owner: Platform Design System
```

---

## Badge

```
Status: Core
Purpose: 상태, 카테고리, 프로모션 등을 짧은 텍스트 레이블로 표시하는 배지입니다.
Use when: ProductCardInfo의 프로모션 배지, 상태 표시.
Avoid when: 광고 표시에는 Ad Badge를 사용합니다.
Key props:
- variant [required] brand | positiveWeak | positiveSolid | neutralWeak | neutralOutlined
- size [required] xs | sm | md
- shape [required] box | pill -> positiveSolid만 pill 사용 가능, 나머지는 box
- text [required] string
Composition: none
Owner: Platform Design System
```

---

## Ad Badge

```
Status: Core
Purpose: 광고 콘텐츠임을 표시하는 전용 AD 배지입니다.
Use when: sectionHeader의 ad variant 내부, 광고 상품 영역 표시.
Avoid when: 일반 레이블 표시에는 Badge를 사용합니다.
Key props:
- kind [required] overlay | inline
  -> overlay: 이미지 위 오버레이 배치
  -> inline: 텍스트와 인라인 배치
Composition: none
Owner: Platform Design System
```

---

## Icon Count

```
Status: Core
Purpose: 아이콘과 숫자를 조합해 북마크 수, 댓글 수 등 카운트 정보를 표시합니다.
Use when: ProductCardInfo의 subInfo 영역에서 카운트 정보 표시.
Avoid when: ProductCardInfo 외부에서 단독 사용하지 않습니다.
Key props:
- variant [required] bookmark | talk
- text [required] string -> 숫자 문자열 (예: "99+")
Composition: none
Owner: Platform Design System
```

---

## Toggle - textIconToggle

```
Status: Beta
Purpose: 아이콘과 텍스트 레이블을 포함한 선택형 토글 버튼입니다.
Use when: 아이콘과 텍스트를 함께 표시해야 하는 필터, 선택 옵션.
Avoid when: 텍스트만 필요하면 textToggle, 아이콘만 필요하면 iconToggle을 사용합니다.
Key props:
- variant [required] brandWeak
- size [required] md | sm
- shape [required] box
- state [required] on | off
- text [required] string
- icon [required] -> toggleItem INSTANCE_SWAP
Composition: none
Owner: Platform Design System
```

---

## Toggle - iconToggle

```
Status: Beta
Purpose: 아이콘만 포함한 선택형 토글 버튼입니다.
Use when: 텍스트 없이 아이콘으로만 선택 상태를 표현할 때.
Avoid when: 텍스트 레이블이 필요하면 textIconToggle을 사용합니다.
Key props:
- variant [required] neutralOutlined | neutralSolid
- size [required] xl | lg | md | sm
- shape [required] box | none
- state [required] on | off
- icon [required] -> toggleItem INSTANCE_SWAP (bookmark / bookmark-filled / bookmark-outlined / check / noti-added / unnoti)
Composition: none
Owner: Platform Design System
```

---

## Toggle - textToggle

```
Status: Beta
Purpose: 텍스트 레이블만 포함한 선택형 토글 버튼입니다.
Use when: 아이콘 없이 텍스트만으로 선택 옵션을 표현할 때.
Avoid when: 아이콘이 필요하면 textIconToggle 또는 iconToggle을 사용합니다.
Key props:
- variant [required] neutralWeak
- size [required] xs
- shape [required] box
- state [required] on | off
- text [required] string
Composition: none
Owner: Platform Design System
```

---

## DSIcon

```
Status: Experimental
Purpose: 시스템 아이콘을 임시로 표준 크기와 tint 규칙에 맞춰 표시합니다.
Use when: 기존 컴포넌트가 DSIcon 구조를 아직 참조하는 경우에만 사용합니다.
Avoid when: 신규 컴포넌트에서 직접 의존하지 않습니다.
Note: This component may be removed or replaced.
Key props:
- size [required] xxxxs | xxxs | xxs | xs | sm | md | lg | xl | xxl | xxxl
- tint [required] none | neutral | brand | positive
  -> none: 원본 아이콘 색상 유지
  -> neutral | brand | positive: 컬러 토큰 오버라이드 적용
- semantic [required] sic.* -> 아이콘 이름 (sic.heart, sic.chevron-left 등)
Composition: none
Owner: Platform Design System
```

---

## SlotPlaceholder

```
Status: Internal
Purpose: Slot 기반 컴포넌트 제작 시 임시 콘텐츠 위치를 표시하는 내부 placeholder입니다.
Use when: 컴포넌트 제작 중 slot 위치를 정의할 때만 사용합니다.
Avoid when: 실제 화면, 테스트 지면, published pattern 안에 노출하지 않습니다.
Replacement: 실제 slot content로 교체하거나 제거합니다.
Key props: none
Composition: none
Owner: Platform Design System
```

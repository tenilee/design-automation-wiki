# Component Contracts

> Platform Design System의 화면 조합용 컴포넌트 Contract 목록입니다.

---

## 기준

- Figma Design System: https://www.figma.com/design/wJaqzyxIazITPQ7ULFJY4o/Platform-Design-System?node-id=37-22154
- Token naming: dot path convention (예: `adBadge.overlay.container.bg.default`)
- Contract convention: `Component_Contract_Convention.md`

---

## 목차

- [Page](#page)
- [Section Group](#section-group)
- [Section](#section)
- [Section Footer](#section-footer)
- [NavigationBar](#navigationbar)
- [SectionHeader](#sectionheader)
- [productCardGrid](#productcardgrid)
- [productCardCarousel](#productcardcarousel)
- [ProductCard TwoColumn](#productcard-twocolumn)
- [ProductCard ThreeColumn](#productcard-threecolumn)
- [ProductCard Thumbnail (Internal)](#productcard-thumbnail-internal)
- [ProductCard BrandBadge (Internal)](#productcard-brandbadge-internal)
- [ProductCard Info TwoColumn (Internal)](#productcard-info-twocolumn-internal)
- [ProductCard Info ThreeColumn (Internal)](#productcard-info-threecolumn-internal)
- [ProductThumbnail](#productthumbnail)
- [Grid](#grid)
- [Carousel](#carousel)
- [Post](#post)
- [Image](#image)
- [Button](#button)
- [TextButton](#textbutton)
- [IconButton](#iconbutton)
- [Badge](#badge)
- [ImageBadge](#imagebadge)
- [AdBadge](#adbadge)
- [IconCount](#iconcount)
- [FavoriteToggle](#favoritetoggle)
- [MuteToggle](#mutetoggle)
- [AlarmToggle](#alarmtoggle)
- [FollowToggle](#followtoggle)
- [Icon](#icon)
- [SlotPlaceholder](#slotplaceholder)

---

## Page

```
Status: Core
Purpose: 모바일 화면의 기본 페이지 컨테이너입니다.
Use when: 화면 전체의 배경, NavigationBar, Section Group을 하나의 페이지 구조로 조립할 때.
Avoid when: 독립 카드, 모달, 부분 영역만 구성할 때는 사용하지 않습니다.
Key props:
- backgroundColor [required] basement | neutralWeak
  -> basement: 기본 흰 배경, neutralWeak: 약한 회색 배경
Composition:
- NavigationBar [0-1] (항상 최상단 고정)
- Section Group [1]
Order: fixed (NavigationBar -> Section Group)
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
- gap [required] none | sm | md | lg -> Section 간 수직 간격
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
  -> page: layout.page.paddingHorizontal 적용
  -> none: 좌우 패딩 없음 (full-width 이미지, 캐러셀 등)
- showHeader [optional] boolean -> 기본값 true, false면 headerArea 숨김
- showFooter [optional] boolean -> 기본값 true, false면 footerArea 숨김
Composition:
- headerSlot [0-1]: SectionHeader 권장, custom 가능
- contentSlot [1]: Grid | Carousel | productCardGrid | productCardCarousel | Image | Post | Button | SlotPlaceholder
- footerSlot [0-1]: SectionFooter | Button | SlotPlaceholder
Order: fixed (headerSlot -> contentSlot -> footerSlot)
Owner: Platform Design System
```

---

## Section Footer

```
Status: Core
Purpose: Section의 footerSlot에 배치되는 푸터 컨테이너입니다.
Use when: Section 하단에 버튼 등 CTA가 필요할 때.
Avoid when: footerSlot 없이 단독 사용하지 않습니다.
Key props:
- kind [required] button
Composition: none
Owner: Platform Design System
```

---

## NavigationBar

```
Status: Core
Purpose: 화면 최상단에 위치하는 네비게이션 바로, 타이틀과 좌우 액션 버튼을 포함합니다.
Use when: 모든 일반 모바일 화면의 상단 네비게이션 영역.
Avoid when: 모달, 바텀시트 내부 헤더에는 사용하지 않습니다.
Key props:
- title [optional] string -> 없으면 타이틀 빈 영역
- leftItem [required] none | 1 -> 좌측 버튼 개수
- leftIcon [optional] INSTANCE_SWAP -> leftItem=1일 때 아이콘 지정
  -> NavigationIcon-back | NavigationIcon-menu | NavigationIcon-home 등
- rightItem [required] none | 1 | 2 | 3 -> 우측 버튼 개수
  -> 각 rightItem은 NavigationBarRightItem 컴포넌트로 구성
Composition:
- NavigationBarRightItem [0-3]
Owner: Platform Design System
```

**NavigationBarRightItem**

```
Status: Core
Purpose: NavigationBar 우측 슬롯의 단일 액션 아이템입니다.
Key props:
- icon [required] INSTANCE_SWAP -> NavigationIcon-cart | NavigationIcon-setting 등
- badge [optional] false | true -> 뱃지 노출 여부
Composition: none
Owner: Platform Design System
```

---

## SectionHeader

```
Status: Core
Purpose: 섹션 제목과 선택적 우측 액션을 표시하는 헤더입니다.
Use when: Section의 headerSlot에서 섹션 제목이 필요한 경우.
Avoid when: 페이지 최상단 타이틀에는 NavigationBar를 사용합니다.
Key props:
- variant [required] titleOnly | disclosure | ad
  -> titleOnly: 제목만 표시
  -> disclosure: 제목 + 우측 더보기 버튼(TextButton)
  -> ad: 제목 + 우측 광고 배지(AdBadge)
- text [required] string -> 섹션 제목
Composition: none
Token prefix: sectionHeader.*
Owner: Platform Design System
```

---

## productCardGrid

```
Status: Core
Purpose: ProductCard를 정해진 컬럼 규칙에 따라 배치하는 상품 그리드 패턴입니다.
Use when: 상품 목록 화면, 큐레이션 화면의 상품 그리드 섹션.
Avoid when: 가로 스크롤 상품 목록에는 productCardCarousel을 사용합니다.
Key props:
- grid [required] two | three
  -> two: ProductCard TwoColumn을 2열로 배치 (TwoColumnProductCardGrid)
  -> three: ProductCard ThreeColumn을 3열로 배치 (ThreeColumnProductCardGrid)
- row [required] 1 | 2 | 3 | 4 | 6 -> 표시할 행 수
Composition:
- ProductCard TwoColumn | ProductCard ThreeColumn [row개]
Order: flexible
Owner: Platform Design System
```

---

## productCardCarousel

```
Status: Core
Purpose: ProductCard를 가로 스크롤로 나열하는 상품 캐러셀 패턴입니다.
Use when: 관련 상품, 추천 상품처럼 가로 스크롤로 탐색하는 상품 목록.
Avoid when: 세로 목록 형태의 상품 나열에는 productCardGrid를 사용합니다.
Key props: none (내부 구성 고정)
Composition:
- ProductCard ThreeColumn [3+]
Order: flexible
Owner: Platform Design System
```

---

## ProductCard TwoColumn

```
Status: Core
Purpose: 2열 상품 리스트에서 사용하는 ProductCard 마스터 컴포넌트입니다.
Use when: 페이지 본문에서 2column 상품 그리드 또는 2열 상품 리스트를 구성할 때.
Avoid when: 3column 상품 리스트가 필요한 경우에는 ProductCard ThreeColumn을 사용합니다.
Composition:
- ProductCard Thumbnail [1]
- ProductCard Info TwoColumn [1]
Order: fixed (ProductCard Thumbnail -> ProductCard Info TwoColumn)
Owner: Platform Design System
```

---

## ProductCard ThreeColumn

```
Status: Core
Purpose: 3열 상품 리스트에서 사용하는 ProductCard 마스터 컴포넌트입니다.
Use when: 페이지 본문에서 3column 상품 그리드 또는 더 조밀한 상품 리스트를 구성할 때.
Avoid when: 2column 상품 리스트가 필요한 경우에는 ProductCard TwoColumn을 사용합니다.
Composition:
- ProductCard Thumbnail [1]
- ProductCard Info ThreeColumn [1]
Order: fixed (ProductCard Thumbnail -> ProductCard Info ThreeColumn)
Owner: Platform Design System
```

---

## ProductCard Thumbnail (Internal)

```
Status: Internal
Purpose: ProductCard 내부에서 상품 이미지를 표시하는 썸네일 컴포넌트입니다.
Use when: ProductCard TwoColumn / ThreeColumn 내부에서 상품 이미지, AD 뱃지, 브랜드 뱃지, 동영상, 찜 상태를 함께 표시할 때.
Avoid when: 화면에서 썸네일을 단독으로 직접 배치해야 하는 경우에는 ProductThumbnail을 사용합니다.
Replacement: ProductCard TwoColumn 또는 ProductCard ThreeColumn 내부 composition으로 사용합니다.
Key props:
- favorite [required] off | on
- brandBadge [optional] boolean -> 브랜드 뱃지 노출 여부
- adBadge [optional] boolean -> AD 뱃지 노출 여부
- video [optional] boolean -> 동영상 뱃지 노출 여부
Composition:
- Image [1]
- AdBadge [0-1]
- BrandBadge [0-1]
- FavoriteToggle [1]
Order: fixed overlay
Owner: Platform Design System
```

---

## ProductCard BrandBadge (Internal)

```
Status: Internal
Purpose: ProductCard Thumbnail 위에 표시되는 브랜드/서비스 식별 뱃지 컴포넌트입니다.
Use when: 상품 이미지 위에 edition1, care, mercari 등 브랜드/서비스 뱃지를 표시할 때.
Avoid when: 브랜드 뱃지가 필요 없는 경우에는 ProductCard Thumbnail의 brandBadge를 false로 설정합니다.
Replacement: ProductCard Thumbnail의 brandBadge prop과 nested brand variant로 제어합니다.
Key props:
- brand [required] edition1 | care | mercari
Composition: none
Owner: Platform Design System
```

---

## ProductCard Info TwoColumn (Internal)

```
Status: Internal
Purpose: ProductCard TwoColumn에 들어가는 2column 전용 상품 정보 컴포넌트입니다.
Use when: 2column 상품 카드에서 상품명, 가격, 할인율, 뱃지, 보조 정보, 등록 시간, 채팅 수, 북마크 수를 표시할 때.
Avoid when: ProductCard ThreeColumn 안에서는 ProductCard Info ThreeColumn을 사용합니다.
Replacement: ProductCard TwoColumn 내부 composition으로 사용합니다.
Key props:
- badgePrimary [optional] boolean
- badgeSecondary [optional] boolean
- discount [optional] boolean
- discountText [optional] string
- priceText [required] string
- itemNameText [required] string
- subInfo [optional] boolean
- date [optional] boolean
- dateText [optional] string
- bookmarkCount [optional] boolean
- talkCount [optional] boolean
Composition:
- Badge [0-2] -> size=xs, shape=box로 고정
- IconCount [0-2]
Order: fixed
Owner: Platform Design System
```

---

## ProductCard Info ThreeColumn (Internal)

```
Status: Internal
Purpose: ProductCard ThreeColumn에 들어가는 3column 전용 상품 정보 컴포넌트입니다.
Use when: 3column 상품 카드에서 상품명, 가격, 할인율, 뱃지, 등록 시간을 표시할 때.
Avoid when: ProductCard TwoColumn 안에서는 ProductCard Info TwoColumn을 사용합니다.
Replacement: ProductCard ThreeColumn 내부 composition으로 사용합니다.
Key props:
- badgePrimary [optional] boolean
- badgeSecondary [optional] boolean
- discount [optional] boolean
- discountText [optional] string
- priceText [required] string
- itemNameText [required] string
- date [optional] boolean
- dateText [optional] string
Composition:
- Badge [0-2] -> size=xs, shape=box로 고정
Order: fixed
Owner: Platform Design System
```

---

## ProductThumbnail

```
Status: Core
Purpose: 상품 이미지를 단독으로 표시하는 썸네일 컴포넌트입니다.
Use when: ProductCard 외부에서 상품 이미지를 독립적으로 배치해야 할 때.
Avoid when: ProductCard 내부에서는 ProductCard Thumbnail(Internal)을 사용합니다.
Key props:
- ratio [required] 4:5 | 1:1 -> 이미지 비율
Composition: none
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
- rows [required] 1 | 2 | 3 | 4 | 5 | 6
- gap-x [required] md -> 현재 md 고정
- gap-y [required] xl -> 현재 xl 고정
- content [required] INSTANCE_SWAP -> SlotPlaceholder 자리에 실제 컴포넌트 교체
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
- content [required] INSTANCE_SWAP -> SlotPlaceholder 자리에 실제 컴포넌트 교체
Composition:
- content [1+]
Order: flexible
Owner: Platform Design System
```

---

## Post

```
Status: Core
Purpose: 카테고리, 제목, 부제목, 본문 텍스트를 조합한 에디토리얼 텍스트 블록입니다.
Use when: 큐레이션 화면의 에디터 노트, 스토리텔링 텍스트 섹션.
Avoid when: 단순 안내 문구나 상품 설명에는 사용하지 않습니다.
Key props:
- style [required] title | subtitle | body
  -> title: 제목 중심 레이아웃
  -> subtitle: 부제목 포함 레이아웃
  -> body: 본문 중심 레이아웃
- size [required] lg | md | sm
- title [required] string -> 2줄 이내 권장
- subtitle [optional] string
- body [required] string -> 3-5줄 권장
Composition: none
Owner: Platform Design System
```

---

## Image

```
Status: Core
Purpose: 콘텐츠 이미지를 fill 또는 fit 모드로 표시합니다.
Use when: 히어로 이미지, 에디토리얼 이미지 등 콘텐츠 이미지가 필요한 모든 영역.
Avoid when: 아이콘 이미지에는 Icon을 사용합니다.
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

## Button

```
Status: Core
Purpose: 사용자의 단일 액션을 트리거하는 버튼입니다.
Use when: 폼 제출, 화면 이동, 기능 실행 등 명확한 액션이 필요할 때.
Avoid when: 텍스트 링크처럼 인라인으로 들어가야 할 때는 TextButton을 사용합니다.
Key props:
- variant [required] brandSolid | neutralSolid | brandWeak | positiveWeak | neutralWeak | neutralOutline
  -> brandSolid: 주요 CTA, 구매/신청 등
  -> neutralSolid: 일반 주요 액션
  -> brandWeak | neutralWeak: 보조 액션, 더보기
  -> positiveWeak: 긍정적 보조 액션
  -> neutralOutline: 외곽선 스타일 보조 액션
- size [required] xl | lg | md | sm | xs
- text [required] string
- disabled [optional] true | false -> 기본값 false
Composition: none
Owner: Platform Design System
```

---

## TextButton

```
Status: Core
Purpose: 텍스트 중심의 인라인 보조 액션 버튼입니다.
Use when: SectionHeader의 disclosure 영역, 더보기 링크 등 텍스트 형태의 보조 액션.
Avoid when: 주요 CTA나 단독 버튼에는 Button 컴포넌트를 사용합니다.
Key props:
- text [required] string
- appearance [required] text | rightIcon | underline
  -> text: 텍스트만
  -> rightIcon: 텍스트 + 우측 chevron 아이콘
  -> underline: 텍스트 + 하단 밑줄
- emphasis [required] strong | neutral | mute | subtle -> 강조 수준
- size [required] lg | md
- weight [required] bold | medium
- disabled [optional] true | false -> 기본값 false
Composition: none
Owner: Platform Design System
```

---

## IconButton

```
Status: Core
Purpose: 아이콘만으로 구성된 액션 버튼입니다.
Use when: 텍스트 없이 아이콘 단독으로 액션을 표현해야 할 때.
Avoid when: 텍스트 레이블이 필요하면 Button을 사용합니다.
Key props:
- icon [required] INSTANCE_SWAP -> 버튼에 표시할 아이콘
- size [required] xl | lg | md
- variant [required] neutralOutline | ghost
- disabled [optional] true | false -> 기본값 false
Composition: none
Owner: Platform Design System
```

---

## Badge

```
Status: Core
Purpose: 상태, 카테고리, 프로모션 등을 짧은 텍스트 레이블로 표시하는 배지입니다.
Use when: ProductCardInfo의 프로모션 배지, 상태 표시.
Avoid when: 광고 표시에는 AdBadge를 사용합니다.
Note: ProductCard 내부에서는 ProductCard Info 컴포넌트가 size=xs, shape=box 조합을 고정해서 사용합니다. 화면 조합 시 ProductCard 내부 Badge를 직접 배치하지 않습니다.
Key props:
- variant [required] brandWeak | positiveWeak | neutralWeak | neutralOutline | positiveSolid
- size [required] xs | sm | md
- shape [required] box | pill -> positiveSolid만 pill 사용 가능, 나머지는 box
- text [required] string
- showIcon [optional] boolean -> 아이콘 노출 여부
- icon [optional] INSTANCE_SWAP -> showIcon=true일 때 표시할 아이콘
Composition: none
Owner: Platform Design System
```

---

## ImageBadge

```
Status: Core
Purpose: 상품 이미지 위에 표시되는 브랜드/서비스 이미지 뱃지입니다.
Use when: edition1, care, mercari 등 브랜드/서비스를 이미지 뱃지 형태로 표시할 때.
Avoid when: 텍스트 기반 뱃지가 필요하면 Badge를 사용합니다.
Key props:
- kind [required] care | edition1 | edition1_fill | mercari | official | pro | today_deal | video
Composition: none
Owner: Platform Design System
```

---

## AdBadge

```
Status: Core
Purpose: 광고 콘텐츠임을 표시하는 전용 AD 배지입니다.
Use when: SectionHeader의 ad variant 내부, 광고 상품 영역 표시.
Avoid when: 일반 레이블 표시에는 Badge를 사용합니다.
Key props:
- kind [required] overlay | inline
  -> overlay: 이미지 위 오버레이 배치
  -> inline: 텍스트와 인라인 배치
Composition: none
Owner: Platform Design System
```

---

## IconCount

```
Status: Core
Purpose: 아이콘과 숫자를 조합해 북마크 수, 댓글 수 등 카운트 정보를 표시합니다.
Use when: ProductCardInfo의 subInfo 영역에서 카운트 정보 표시.
Avoid when: ProductCardInfo 외부에서 단독 사용하지 않습니다.
Key props:
- kind [required] favorite | talk
- count [required] string -> 숫자 문자열 (예: "99+")
Composition: none
Owner: Platform Design System
```

---

## FavoriteToggle

```
Status: Core
Purpose: 상품 찜하기 상태를 토글하는 컴포넌트입니다.
Use when: 상품 카드, 상품 상세 등 찜 상태를 on/off할 때.
Avoid when: 찜 외 다른 토글에는 해당 도메인 Toggle을 사용합니다.
Key props:
- kind [required] default | overlay
  -> default: 기본 배치
  -> overlay: 이미지 위 오버레이 배치
- state [required] off | on
Composition: none
Owner: Platform Design System
```

---

## MuteToggle

```
Status: Core
Purpose: 동영상/알림 음소거 상태를 토글하는 컴포넌트입니다.
Use when: 동영상 또는 알림의 음소거 상태를 on/off할 때.
Key props:
- state [required] off | on
Composition: none
Owner: Platform Design System
```

---

## AlarmToggle

```
Status: Core
Purpose: 알림 구독 상태를 토글하는 컴포넌트입니다.
Use when: 특정 채널, 브랜드, 상품의 알림 구독 상태를 on/off할 때.
Key props:
- kind [required] icon | text
  -> icon: 아이콘만 표시
  -> text: 텍스트 레이블 포함
- state [required] on | off
Composition: none
Owner: Platform Design System
```

---

## FollowToggle

```
Status: Core
Purpose: 팔로우 상태를 토글하는 컴포넌트입니다.
Use when: 사용자, 브랜드, 채널 등의 팔로우 상태를 on/off할 때.
Key props:
- state [required] off | on
Composition: none
Owner: Platform Design System
```

---

## Icon

```
Status: Core
Purpose: 컴포넌트화 대상이 아닌 1회성 커스텀 슬롯에서 DS 아이콘 소스를 표준 size와 tint 규칙에 맞춰 표시하는 아이콘 래퍼입니다.
Use when: 1회성 커스텀 슬롯 안에서 시스템 아이콘 또는 원본 컬러 유지 아이콘을 단독으로 배치해야 할 때.
Avoid when: Button, NavigationBar, Toggle, Badge 등 기존 컴포넌트 내부에 포함되는 아이콘에는 사용하지 않습니다. 반복 사용되거나 2개 이상 화면에서 재사용될 가능성이 있는 아이콘 UI는 별도 컴포넌트화를 검토합니다.
Key props:
- size [required] xxxxs | xxxs | xxs | xs | sm | md | lg | xl | xxl | xxxl
- tint [required] none | neutral | brand | positive | inverse
  -> none: 아이콘 원본 컬러 유지
  -> neutral: color.fg.neutral.contrast 바인딩
  -> brand: color.fg.brand.contrast 바인딩
  -> positive: color.fg.positive.contrast 바인딩
  -> inverse: color.fg.neutral.on-solid 바인딩
- icon [required] INSTANCE_SWAP -> DS 아이콘 소스 (sic.* 또는 iic.*)
Composition:
- icon slot [1]: sic.* 또는 iic.* 아이콘 인스턴스
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

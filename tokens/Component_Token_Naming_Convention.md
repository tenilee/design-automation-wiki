# Component Token Naming Convention v2

## Purpose

이 문서는 BGZT Platform Design System의 component token 네이밍 규칙을 정의한다.

기존 규칙은 Figma variant 구조와 1:1로 대응하기 쉬웠지만, variant명이 토큰 경로에 반복되면서 같은 값의 중복이 많아지는 문제가 있었다.

v2 규칙은 **컴포넌트 anatomy와 실제로 달라지는 스타일 값**을 기준으로 token을 정의한다.

Component token은 큰 범위에서 작은 범위로 읽히도록 `component → element → property → detail` 순서로 정의한다. `variant`는 디자인 단계에서 선택되는 정적 조건이고, `state`는 런타임에서 바뀌는 상태이므로 가장 뒤에 둔다.

## Format

```text
{comp}.{element}.{property}.{variant*}.{state?}
```

`variant`와 `state`는 선택값이다. `variant*`는 variant segment를 0개 이상 사용할 수 있다는 의미다.

크기처럼 style variant와 별도의 축이 필요한 경우에는 `size-*`를 `variant` 위치에 둔다.

Badge처럼 선택 축이 여러 개인 경우에는 의미를 합쳐 새 이름을 만들지 않고, 각 선택 축을 순서대로 둔다.

```text
badge.container.bg.box.brandWeak
badge.container.bg.pill.positiveSolid
```

## Axes

### comp

토큰이 속한 컴포넌트 이름이다.

```text
button
sectionHeader
productCard
navigationBar
image
toggle
```

예:

```text
button.container.bg.brandSolid.enabled
sectionHeader.title.typography
```

### element

컴포넌트 내부 구성요소다.

```text
container
label
title
icon
thumbnail
track
thumb
image
content
backButton
```

`element`는 실제 컴포넌트 anatomy를 기준으로 정한다. Figma variant명이나 사용 맥락을 element로 넣지 않는다.

예:

```text
button.container.bg.brandSolid.enabled
button.label.color.brandSolid.enabled
button.icon.size.size-lg
sectionHeader.title.color
navigationBar.backButton.icon.asset
```

### property

해당 element에서 제어하는 스타일 속성이다.

```text
bg
color
border
typography
height
width
size
radius
padding-x
padding-y
gap-x
gap-y
asset
opacity
```

예:

```text
button.container.bg.brandSolid.enabled
button.container.height.size-lg
toggle.icon.asset.favorite.on
```

#### bg, color, border

색상 속성은 적용 대상에 따라 구분한다.

```text
bg      = background-color
color   = foreground color, text/icon color
border  = border-color
```

예:

```text
button.container.bg.brandSolid.enabled
button.container.border.neutralOutline.enabled
button.label.color.brandSolid.enabled
button.icon.color.brandSolid.enabled
```

`container.color`처럼 배경/테두리/전경이 모호한 이름은 사용하지 않는다.

## Hyphen

하이픈(`-`)은 token path의 depth를 늘리지 않고 하나의 축 안에서 복합 의미를 표현할 때만 사용한다.

사용하는 경우:

```text
padding-x
padding-y
gap-x
gap-y
size-sm
size-md
size-lg
```

사용하지 않는 경우:

```text
brand-solid
neutral-weak
product-card
section-header
```

컴포넌트명, element명, 일반 variant명은 camelCase를 기본으로 한다.

### variant

컴포넌트의 스타일 타입, 용도, 크기, 강조 수준처럼 실제 값 차이를 만드는 선택 축이다.

```text
brandSolid
neutralWeak
neutralOutline
box
pill
strong
muted
subtle
size-sm
size-md
twoColumn
threeColumn
placeholder
```

`variant`는 Figma variant명을 무조건 넣는 자리가 아니다. 사용자가 선택하는 핵심 타입이거나 실제 token 값이 달라질 때만 사용한다.

사용하는 경우:

```text
Button의 brandSolid, neutralWeak처럼 버튼 타입 자체를 구분해야 함
size-md, size-lg처럼 크기별 height, typography, icon size가 다름
ProductCard의 twoColumn, threeColumn처럼 column별 typography가 다름
Image의 placeholder처럼 특정 용도에서 bg, icon color, asset이 달라짐
Badge의 box, pill처럼 shape 축과 brandWeak, positiveSolid 같은 tone 축이 분리됨
```

사용하지 않는 경우:

```text
variant는 다르지만 값이 같음
단순히 Figma 구조상 variant가 나뉘었을 뿐임
컴포넌트 전체가 공통으로 쓰는 anatomy 값임
```

예:

```text
button.container.bg.brandSolid.enabled
button.container.bg.neutralWeak.enabled
button.container.height.size-lg
productCard.price.label.typography.twoColumn
image.container.bg.placeholder
badge.container.bg.box.brandWeak
badge.container.bg.pill.positiveSolid
```

### state

인터랙션 또는 상태에 따라 값이 달라질 때 사용하는 선택 축이다.

```text
enabled
pressed
disabled
loading
selected
on
off
focus
error
```

예:

```text
button.container.bg.brandSolid.enabled
button.container.bg.brandSolid.pressed
button.container.bg.brandSolid.disabled
toggle.icon.color.favorite.on
toggle.icon.color.favorite.off
```

상태에 따라 값이 바뀌지 않는 고정값에는 state를 붙이지 않는다.

```text
sectionHeader.title.typography
productCard.thumbnail.radius
icon.size.md
```

## Examples

### sectionHeader

기존처럼 variant별로 같은 값을 반복하지 않는다.

```text
sectionHeader.title.color
sectionHeader.title.typography
sectionHeader.container.padding-y
```

variant별로 실제 값이 다른 경우에만 variant를 사용한다.

```text
sectionHeader.ad.container.gap-x
sectionHeader.disclosure.container.gap-x
```

### button

Button은 style variant별 container, label, icon 값이 실제로 달라지고, variant 자체가 사용자가 선택하는 핵심 타입이므로 variant를 사용한다.

```text
button.container.bg.brandSolid.enabled
button.container.bg.brandSolid.pressed
button.container.bg.neutralWeak.enabled
button.container.border.neutralOutline.enabled
button.label.color.brandSolid.enabled
button.icon.color.brandSolid.enabled
```

size별 값도 variant로 관리한다.

```text
button.container.height.size-lg
button.container.padding-x.size-lg
button.container.radius.size-lg
button.label.typography.size-lg
button.icon.size.size-lg
```

### badge

Badge는 `shape`와 `tone` 선택 축이 분리되므로 variant를 여러 segment로 둔다. `boxBrandWeak`, `pillPositiveSolid`처럼 합성 이름을 만들지 않는다.

```text
badge.container.bg.box.brandWeak
badge.container.bg.box.neutralWeak
badge.container.bg.box.neutralOutlined
badge.container.bg.pill.positiveSolid
badge.label.color.box.brandWeak
badge.icon.color.box.brandWeak
badge.container.radius.box
badge.container.radius.pill
```

### productCard

같은 값은 column variant별로 반복하지 않는다.

```text
productCard.price.label.color
productCard.title.label.color
productCard.thumbnail.container.radius
productCard.favoriteToggle.container.size
```

column별로 실제 값이 다른 경우에만 variant를 사용한다.

```text
productCard.price.label.typography.twoColumn
productCard.price.label.typography.threeColumn
productCard.itemName.label.typography.twoColumn
productCard.itemName.label.typography.threeColumn
```

### image

`placeholder`는 image의 element가 아니라, image가 placeholder 상태 또는 용도로 사용될 때의 variant다.

권장 형태:

```text
image.container.bg.placeholder
image.icon.color.placeholder
image.icon.asset.placeholder
```

### toggle

아이콘 asset, color, size를 분리한다.

```text
toggle.icon.asset.favorite.off
toggle.icon.asset.favorite.on
toggle.icon.color.favorite.off
toggle.icon.color.favorite.on
toggle.icon.asset.notification.off
toggle.icon.asset.notification.on
toggle.icon.color.notification.off
toggle.icon.color.notification.on
toggle.icon.size.size-sm
toggle.icon.size.size-md
```

## Migration Guidance

### 1. Variant name removal

Figma variant명이 token path에 들어가 있더라도, 값이 같으면 anatomy token으로 통합한다.

```text
sectionHeader.ad.title.color
sectionHeader.disclosure.title.color
sectionHeader.titleOnly.title.color
```

수정:

```text
sectionHeader.title.color
```

### 2. Variant retention

variant별로 실제 값이 다르거나, variant 자체가 사용자가 선택하는 핵심 타입이면 variant로 남긴다.

```text
button.container.bg.brandSolid.enabled
button.container.bg.neutralWeak.enabled
```

### 3. Size

size별 값은 `size-*` variant로 관리한다.

```text
button.container.height.size-lg
iconToggle.icon.size.size-xl
```

### 4. State

상태별 값만 state 축에 둔다.

```text
button.container.bg.brandSolid.enabled
button.container.bg.brandSolid.pressed
toggle.icon.color.favorite.on
toggle.icon.color.favorite.off
```

상태와 무관한 값에는 state를 붙이지 않는다.

## Checklist

```text
[ ] token이 컴포넌트 anatomy 기준으로 읽히는가?
[ ] variant가 사용자가 선택하는 핵심 타입이거나 실제 값 차이를 만드는가?
[ ] Figma variant명이 불필요하게 들어가 있지 않은가?
[ ] 같은 값이 variant별로 반복되지 않는가?
[ ] state는 인터랙션/상태 차이를 설명하는가?
[ ] bg/color/border가 적용 속성에 맞게 구분되어 있는가?
[ ] component token이 semantic token을 참조하는가?
```

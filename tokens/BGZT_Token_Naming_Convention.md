# BGZT 디자인 토큰 네이밍 컨벤션

> **목적**: 3-레이어 토큰 구조의 원리와 네이밍 규칙을 정의하고,
> 앞으로 모든 컴포넌트에 일관되게 적용하기 위한 단일 기준 문서

---

## 목차

1. [3-레이어 구조 개요](#1-3-레이어-구조-개요)
2. [레이어 1: Primitive 토큰 규칙](#2-레이어-1-primitive-토큰-규칙)
3. [레이어 2: Semantic 토큰 규칙](#3-레이어-2-semantic-토큰-규칙)
4. [레이어 3: Component 토큰 규칙](#4-레이어-3-component-토큰-규칙)
5. [Component 토큰 5개 축 상세 정의](#5-component-토큰-5개-축-상세-정의)
6. [신규 컴포넌트 적용 워크플로우](#6-신규-컴포넌트-적용-워크플로우)
7. [컴포넌트 적용 예시 모음](#7-컴포넌트-적용-예시-모음)
8. [체크리스트](#8-체크리스트)

---

## 1. 3-레이어 구조 개요

BGZT 토큰 시스템은 세 개의 레이어로 구성됩니다.
각 레이어는 서로 단방향으로 참조하며, 아래 레이어를 건너뛰고 참조하지 않습니다.

```
┌─────────────────────────────────────────────────────┐
│  Layer 3: Component Token                           │
│  button.brandSolid.container.bg.default             │
│  → {color.bg.brand.solid}                          │  ← Semantic만 참조
├─────────────────────────────────────────────────────┤
│  Layer 2: Semantic Token                            │
│  color.bg.brand.solid                               │
│  → {color.red.500}                                 │  ← Primitive만 참조
├─────────────────────────────────────────────────────┤
│  Layer 1: Primitive Token                           │
│  color.red.500 = #D80C18                            │  ← 절대값
└─────────────────────────────────────────────────────┘
```

**왜 3레이어인가?**

- **Primitive만 있을 때**: 컴포넌트가 `color.red.500`을 직접 참조하면, 브랜드 색이 바뀔 때 모든 컴포넌트를 수동으로 수정해야 함
- **Semantic을 더하면**: 컴포넌트는 `color.bg.brand.solid`를 참조, primitive 값만 바꾸면 전체 반영
- **Component 레이어를 더하면**: Figma의 variant/state 구조와 토큰이 1:1 대응되어 자동화 가능, 디자이너와 개발자가 동일한 언어로 소통 가능

**참조 방향 규칙**

```
Component → Semantic → Primitive   ✅ 올바른 참조 방향
Component → Primitive              ❌ 금지 (레이어 건너뜀)
Semantic  → Component              ❌ 금지 (역방향)
```

---

## 2. 레이어 1: Primitive 토큰 규칙

### 형식

```
{category}.{scale 또는 name}
```

### 예시

```
color.red.500
color.gray.100
sizing.height.md
spacing.padding.xl
radius.sm
typography.label.md
```

### 규칙

| 규칙 | 설명 |
|------|------|
| 절대값만 사용 | 다른 토큰을 참조하지 않음. 항상 실제 값(#hex, px, 숫자) |
| 의미 없는 스케일 | `red.500`은 빨간색 중 500번째일 뿐, "브랜드"나 "위험" 같은 의미를 담지 않음 |
| 숫자 스케일 원칙 | 색상은 50–900 (밝을수록 낮은 숫자), 사이즈는 xs/sm/md/lg/xl |
| 소문자 + dot 구분 | 카멜케이스 금지, 모든 세그먼트는 소문자 |

### Primitive 카테고리 정의

```
color.{palette}.{scale}        색상 팔레트 (red, gray, blue, lime, green...)
sizing.{type}.{tier}           크기값 (height, icon, ...)
spacing.{type}.{tier}          간격값 (padding, gapX, gapY, ...)
radius.{tier}                  모서리 반경
typography.{style}.{tier}      타이포그래피 합성값
```

---

## 3. 레이어 2: Semantic 토큰 규칙

### 형식

```
{category}.{type}.{role}.{descriptor}[-{state}]
```

각 세그먼트의 정의:

| 세그먼트 | 의미 | 예시 |
|---|---|---|
| `category` | 토큰이 다루는 CSS 속성 영역 | `color`, `sizing`, `spacing` |
| `type` | 해당 속성의 세부 종류 | `bg` (배경색), `fg` (전경/텍스트색), `border` |
| `role` | 어떤 역할/의미의 색인지 | `brand`, `neutral`, `danger`, `positive` |
| `descriptor` | 시각적 강도/사용 맥락 | `solid`, `weak`, `contrast`, `on-solid`, `surface` |
| `-{state}` (선택) | 인터랙션 상태 | `-pressed`, `-disabled` |

### 예시 해석

```
color  . bg    . brand   . solid         = 브랜드 역할의 solid 강도 배경색
color  . fg    . brand   . on-solid      = solid 배경 위에 올라가는 텍스트색
color  . bg    . neutral . weak          = neutral 역할의 weak 강도 배경색
color  . bg    . neutral . weak-pressed  = weak 배경의 pressed 상태
color  . border. neutral . default       = neutral 테두리의 기본 상태
```

### Semantic Role 정의

```
brand     → 브랜드 색(빨강). CTA, 강조 요소에 사용
neutral   → 무채색. 일반 UI, 텍스트, 배경에 사용
positive  → 긍정/성공 상태 (초록 계열)
danger    → 위험/에러 상태 (빨강 계열, brand와 구분)
warning   → 경고 상태 (노랑/주황 계열) — 추후 추가
info      → 정보 상태 (파랑 계열) — 추후 추가
```

### Semantic Descriptor 정의

| descriptor | 의미 | 사용 맥락 |
|---|---|---|
| `solid` | 가장 진한 강도. 배경에 쓸 때 | CTA 버튼 배경 |
| `weak` | 연한 강도. 배경에 쓸 때 | Secondary 버튼 배경, 태그 배경 |
| `on-solid` | solid 배경 위의 텍스트/아이콘 색 | brandSolid 버튼의 흰 텍스트 |
| `contrast` | 컬러 배경 위에서 브랜드색 그대로 쓰는 텍스트 | brandWeak 버튼의 빨간 텍스트 |
| `surface` | 화면 표면(카드, 인풋) 배경색 | InputField 배경 |
| `default` | 기본 테두리/요소 색 | 일반 상태 테두리 |
| `primary` | 가장 강한 텍스트 | 본문, 인풋 텍스트 |
| `secondary` | 중간 강도 텍스트 | 레이블, 부제목 |
| `tertiary` | 가장 연한 텍스트 | placeholder, 힌트 |

### Semantic State 접미사

state는 `-`(하이픈)으로 descriptor에 붙입니다. 별도 세그먼트가 아닙니다.

```
color.bg.brand.solid             default 상태 (state 접미사 없음)
color.bg.brand.solid-pressed     pressed 상태
color.bg.brand.solid-disabled    disabled 상태
```

**왜 state를 별도 세그먼트로 분리하지 않는가?**
Semantic은 "이 토큰이 어떤 색인가"를 정의하는 레이어입니다. state를 경로 안에 넣으면 토큰 수가 과도하게 증가합니다. 대신 `-{state}` 접미사로 하나의 semantic 그룹 내에서 관리합니다.

---

## 4. 레이어 3: Component 토큰 규칙

### 형식

```
{comp}.{condition}.{element}.{property}.{state}
```

각 세그먼트의 정의:

| 세그먼트 | 의미 | 예시 |
|---|---|---|
| `comp` | 컴포넌트 이름 | `button`, `input-field`, `badge`, `chip` |
| `condition` | 스타일 조건 (variant) 또는 사이즈 조건 | `brandSolid`, `size-md`, `base` |
| `element` | 컴포넌트 내부 구성 요소 | `container`, `label`, `icon`, `input` |
| `property` | CSS 속성 | `bg`, `color`, `border`, `height`, `radius` |
| `state` | 인터랙션 상태 | `default`, `pressed`, `focus`, `disabled` |

### 예시 해석

```
button  . brandSolid  . container . bg     . default
└comp   └condition    └element   └property └state
→ brandSolid 스타일 버튼의, container 배경색, 기본 상태

button  . size-xl     . container . height
└comp   └condition    └element   └property
→ xl 사이즈 버튼의, container 높이 (size 토큰은 state 없음)

input-field . base . container . border . focus
└comp       └cond  └element   └property └state
→ 단일 variant 인풋의, container 테두리색, focus 상태
```

---

## 5. Component 토큰 5개 축 상세 정의

### 축 1: comp (컴포넌트)

소문자 케밥케이스. 공식 컴포넌트 이름을 그대로 사용합니다.

```
button          chip            tooltip
input-field     toggle          modal
badge           checkbox        bottom-sheet
tag             radio           snackbar
avatar          select          tab
```

### 축 2: condition (조건)

컴포넌트의 **스타일 변형**과 **사이즈 변형**을 구분하는 축입니다.

**스타일 variant**: `{role}{Emphasis}` 형태의 camelCase

```
brandSolid      brand 역할 + solid 강도
brandWeak       brand 역할 + weak 강도
neutralSolid    neutral 역할 + solid 강도
neutralWeak     neutral 역할 + weak 강도
positiveSolid   positive 역할 + solid 강도
positiveWeak    positive 역할 + weak 강도
dangerSolid     danger 역할 + solid 강도
```

> 💡 `role`과 `emphasis`를 합친 이유: Semantic 토큰명(`color.bg.brand.solid`)의 role+descriptor 조합이
> 그대로 condition 축의 이름이 됩니다. 두 레이어 간 언어가 일치하여 직관적으로 참조 관계를 추론할 수 있습니다.

**사이즈 variant**: `size-{tier}` 형태

```
size-xs / size-sm / size-md / size-lg / size-xl
```

> ❗ `size-` 접두사를 반드시 붙이는 이유: `button.xl.*`과 `button.size-xl.*`은 다른 의미입니다.
> 접두사가 없으면 스타일 variant(`brandSolid`)와 사이즈 variant(`xl`)가 같은 축에서 구분이 어렵습니다.

**단일 variant**: `base`

variant가 하나뿐인 컴포넌트(예: InputField, Badge)는 `base`를 사용합니다.

```
input-field.base.container.border.default
badge.base.container.bg.default
```

> ❗ `default` 대신 `base`를 쓰는 이유: state 축에도 `default`가 있기 때문에
> `input-field.default.container.border.default`처럼 중복이 발생합니다.
> `base`는 "단일 variant"를 의미하며 state의 `default`와 혼동되지 않습니다.

### 축 3: element (구성 요소)

컴포넌트를 이루는 내부 요소의 이름입니다.

| element | 의미 | 사용 컴포넌트 예시 |
|---|---|---|
| `container` | 컴포넌트의 루트/래퍼 요소 | 모든 컴포넌트 |
| `label` | 텍스트 레이블 | button, chip, badge, tab |
| `icon` | 아이콘 | button, input-field, chip |
| `input` | 텍스트 입력 영역 | input-field, textarea |
| `placeholder` | placeholder 텍스트 | input-field |
| `helper` | 도움말/에러 메시지 텍스트 | input-field |
| `prefix` | 앞쪽 장식 요소 | input-field |
| `suffix` | 뒤쪽 장식 요소 | input-field |
| `indicator` | 상태 표시 점/뱃지 | avatar, notification |
| `overlay` | hover/ripple 오버레이 레이어 | 인터랙션 피드백용 |
| `track` | 트랙/배경 슬라이더 | toggle, slider |
| `thumb` | 이동하는 핸들 | toggle, slider |

**element 축이 필요한 이유 — Part Disambiguation**

```
button.brandSolid.color.default  ← ❌ 어떤 요소의 color인가? label? icon?
button.brandSolid.label.color.default  ← ✅ label의 color
button.brandSolid.icon.color.default   ← ✅ icon의 color (다른 값)
```

같은 property(`color`)가 여러 element에 존재할 때, element 없이는 토큰을 구분할 수 없습니다.
반면 element가 유일한 경우(예: `container.height`)도 일관성을 위해 element를 항상 명시합니다.

### 축 4: property (CSS 속성)

토큰이 제어하는 CSS 속성입니다.

| property | CSS 속성 | 적용 element 예시 |
|---|---|---|
| `bg` | background-color | container |
| `color` | color | label, icon, placeholder |
| `border` | border-color | container |
| `border-width` | border-width | container |
| `height` | height | container |
| `width` | width | container |
| `min-width` | min-width | container |
| `padding-x` | padding-left + right | container |
| `padding-y` | padding-top + bottom | container |
| `radius` | border-radius | container |
| `gap` | gap | container (flex) |
| `size` | width + height (정사각형) | icon |
| `typography` | font-size + weight + line-height (합성) | label, input, helper |
| `shadow` | box-shadow | container |
| `opacity` | opacity | container |

### 축 5: state (인터랙션 상태)

컴포넌트의 현재 인터랙션 상태입니다.

| state | 의미 |
|---|---|
| `default` | 기본 상태 |
| `hover` | 마우스 오버 (웹 전용) |
| `pressed` | 눌린 상태 (터치/클릭) |
| `focus` | 포커스 상태 |
| `focus-visible` | 키보드 포커스 상태 |
| `selected` | 선택된 상태 (tab, chip 등) |
| `active` | 활성화된 상태 |
| `disabled` | 비활성화 상태 |
| `loading` | 로딩 중 상태 |
| `error` | 에러 상태 |
| `warning` | 경고 상태 |
| `success` | 성공 상태 |

**Size 토큰에는 state가 없습니다.** 높이, 패딩, 타이포그래피 같은 사이즈 속성은 상태에 따라 변하지 않습니다.

```
button.size-xl.container.height    ✅ state 없음 (사이즈는 상태 불변)
button.brandSolid.container.bg.default    ✅ state 있음 (색상은 상태에 따라 변함)
```

---

## 6. 신규 컴포넌트 적용 워크플로우

새로운 컴포넌트에 토큰을 정의할 때 아래 순서를 따릅니다.

### Step 1: 컴포넌트 분석

Figma에서 컴포넌트를 열고 아래 항목을 파악합니다.

```
[ ] Variant 목록: 어떤 스타일 변형이 있는가?
      예: brandSolid / brandWeak / neutralSolid / neutralWeak
[ ] Size 목록: 몇 가지 사이즈가 있는가?
      예: xs / sm / md / lg / xl
[ ] Element 목록: 컴포넌트 내부에 어떤 요소가 있는가?
      예: container / label / icon / badge
[ ] Property 목록: 각 element에서 어떤 속성이 variant/state에 따라 변하는가?
      예: container.bg, label.color, icon.color
[ ] State 목록: 어떤 인터랙션 상태를 지원하는가?
      예: default / pressed / disabled / loading
```

### Step 2: Condition 분류

**스타일 변형이 있는가?**

- 있다면: `{role}{Emphasis}` 형태로 condition 정의
- 없다면: `base` 사용

**사이즈 변형이 있는가?**

- 있다면: `size-{tier}` 형태로 사이즈 condition 정의
- 없다면: size 토큰 불필요 (literal 값 직접 사용 또는 semantic 직접 참조)

### Step 3: 토큰 네임 초안

아래 공식에 대입합니다.

```
{comp}.{condition}.{element}.{property}.{state}
```

**스타일 토큰 초안 작성 예시 (chip)**

```
chip.brandSolid.container.bg.default
chip.brandSolid.container.bg.pressed
chip.brandSolid.container.bg.disabled
chip.brandSolid.label.color.default
chip.brandSolid.label.color.disabled
chip.brandSolid.icon.color.default
```

**사이즈 토큰 초안 작성 예시 (chip)**

```
chip.size-md.container.height
chip.size-md.container.padding-x
chip.size-md.container.radius
chip.size-md.container.gap
chip.size-md.label.typography
chip.size-md.icon.size
```

### Step 4: Semantic 참조 매핑

각 component token이 참조할 semantic token을 결정합니다.

**매핑 원칙:**

- `container.bg` → `color.bg.{role}.{emphasis}`
- `label.color` (normal state) → `color.fg.{role}.contrast` 또는 `color.fg.{role}.on-solid`
- `label.color` (on-solid 배경) → `color.fg.{role}.on-solid`
- `container.border` → `color.border.{role}.default`
- `container.height` → `sizing.height.{tier}`

**매핑 예시 (chip.brandSolid)**

```
chip.brandSolid.container.bg.default   → {color.bg.brand.solid}
chip.brandSolid.container.bg.pressed   → {color.bg.brand.solid-pressed}
chip.brandSolid.label.color.default    → {color.fg.brand.on-solid}
chip.brandSolid.label.color.disabled   → {color.fg.brand.contrast}
```

### Step 5: Semantic 갭 확인

매핑 과정에서 아직 없는 semantic token이 있다면 먼저 추가합니다.

```
[ ] 필요한 semantic 토큰이 이미 있는가?
[ ] 없다면 semantic 레이어에 추가 → primitive 참조값 결정
[ ] 추가한 semantic 토큰 이름이 기존 컨벤션과 일치하는가?
```

### Step 6: Token Studio 입력

레이어 순서대로 작업합니다.

```
1. Primitive에 빠진 값 추가 (sizing.icon.sm 등)
2. Semantic에 갭 채우기 (새 토큰 추가)
3. Component set에 새 컴포넌트 토큰 추가
```

### Step 7: 검증

```
[ ] 모든 component 토큰이 semantic을 통해 참조하는가? (primitive 직접 참조 없음)
[ ] state별로 resolved value가 의도한 색인가?
[ ] disabled 상태에서 대비율이 충분한가?
[ ] Figma component variant와 1:1 대응되는가?
```

---

## 7. 컴포넌트 적용 예시 모음

### 예시 A: Chip

Chip은 Button과 유사하게 role+emphasis 기반 스타일 variant와 사이즈를 가집니다.

**Variant 분석**: brandSolid / brandWeak / neutralSolid / neutralWeak (Button과 동일 condition 재사용 가능)

```
[스타일 토큰]
chip.brandSolid.container.bg.default      → {color.bg.brand.solid}
chip.brandSolid.container.bg.pressed      → {color.bg.brand.solid-pressed}
chip.brandSolid.container.bg.disabled     → {color.bg.brand.solid-disabled}
chip.brandSolid.label.color.default       → {color.fg.brand.on-solid}
chip.brandSolid.label.color.disabled      → {color.fg.brand.contrast}
chip.brandSolid.icon.color.default        → {color.fg.brand.on-solid}

chip.brandWeak.container.bg.default       → {color.bg.brand.weak}
chip.brandWeak.container.bg.pressed       → {color.bg.brand.weak-pressed}
chip.brandWeak.label.color.default        → {color.fg.brand.contrast}
chip.brandWeak.icon.color.default         → {color.fg.brand.contrast}

[사이즈 토큰]
chip.size-md.container.height             → {sizing.height.sm}     (chip은 button보다 작게 설정 가능)
chip.size-md.container.padding-x          → {spacing.padding.md}
chip.size-md.container.radius             → {radius.full}          (pill 형태)
chip.size-md.container.gap                → {spacing.gapX.xs}
chip.size-md.label.typography             → {typography.label.xs}
chip.size-md.icon.size                    → {sizing.icon.sm}
```

**재사용 포인트**: Chip은 Button과 같은 semantic token을 그대로 참조합니다. semantic 토큰을 추가할 필요가 없습니다. component token만 새로 정의하면 됩니다.

---

### 예시 B: Badge / Tag

Badge는 variant 없이 단일 스타일이거나, role 기반 색상 변형만 있습니다.

```
[variant가 없는 경우 — base 사용]
badge.base.container.bg.default           → {color.bg.brand.solid}
badge.base.label.color.default            → {color.fg.brand.on-solid}

[role 기반 variant가 있는 경우]
badge.brand.container.bg.default          → {color.bg.brand.solid}
badge.neutral.container.bg.default        → {color.bg.neutral.solid}
badge.positive.container.bg.default       → {color.bg.positive.weak}
badge.danger.container.bg.default         → {color.bg.danger.weak}

[사이즈 — 단일 사이즈인 경우 base 사용]
badge.base.container.height               → {sizing.height.xs}
badge.base.container.padding-x            → {spacing.padding.xs}
badge.base.label.typography               → {typography.label.xs}
```

---

### 예시 C: Toggle (Switch)

Toggle은 on/off 두 상태를 condition으로 표현합니다. 기존 role+emphasis와는 다른 패턴입니다.

```
[스타일 토큰 — on/off를 condition으로]
toggle.on.track.bg.default                → {color.bg.brand.solid}
toggle.on.track.bg.disabled               → {color.bg.brand.solid-disabled}
toggle.on.thumb.bg.default                → {color.gray.0} 또는 literal #FFFFFF
toggle.off.track.bg.default               → {color.bg.neutral.weak}
toggle.off.track.bg.disabled              → {color.bg.neutral.weak-disabled}
toggle.off.thumb.bg.default               → {color.gray.0}

[사이즈 토큰]
toggle.size-md.track.width                → (literal 또는 sizing primitive)
toggle.size-md.track.height               → {sizing.height.xs}
toggle.size-md.thumb.size                 → (literal 또는 sizing primitive)
```

---

### 예시 D: Checkbox / Radio

선택형 컴포넌트는 selected/unselected를 condition으로 표현합니다.

```
[스타일 토큰]
checkbox.selected.container.bg.default    → {color.bg.brand.solid}
checkbox.selected.container.border.default → {color.border.brand.default}
checkbox.selected.icon.color.default      → {color.fg.brand.on-solid}

checkbox.unselected.container.bg.default  → transparent 또는 {color.bg.neutral.surface}
checkbox.unselected.container.border.default → {color.border.neutral.default}
checkbox.unselected.container.border.hover   → {color.border.brand.default}

[공통 사이즈 — base 사용]
checkbox.base.container.size              → (literal 20px 등)
checkbox.base.container.radius            → {radius.xs}
checkbox.base.icon.size                   → {sizing.icon.sm}
```

---

### 예시 E: Avatar

Avatar는 color variant 없이 사이즈만 변합니다.

```
[스타일 토큰 — base]
avatar.base.container.bg.default          → {color.bg.neutral.weak}
avatar.base.label.color.default           → {color.fg.neutral.secondary}
avatar.base.indicator.bg.default          → {color.bg.positive.solid}    (온라인 표시)

[사이즈 토큰]
avatar.size-xl.container.size             → 56
avatar.size-lg.container.size             → 48
avatar.size-md.container.size             → 40
avatar.size-sm.container.size             → 32
avatar.size-xs.container.size             → 24
```

---

### 예시 F: Tooltip

Tooltip은 color theme(dark/light)를 condition으로 씁니다.

```
[스타일 토큰]
tooltip.dark.container.bg.default         → {color.bg.neutral.solid}   #191919
tooltip.dark.label.color.default          → {color.fg.neutral.on-solid} #FFFFFF
tooltip.light.container.bg.default        → {color.bg.neutral.surface}
tooltip.light.container.border.default    → {color.border.neutral.default}
tooltip.light.label.color.default         → {color.fg.neutral.primary}

[사이즈 토큰]
tooltip.base.container.padding-x          → {spacing.padding.sm}
tooltip.base.container.padding-y          → {spacing.padding.xs}
tooltip.base.container.radius             → {radius.xs}
tooltip.base.label.typography             → {typography.label.xs}
```

---

## 8. 체크리스트

### 신규 컴포넌트 토큰 정의 시 확인 항목

**Primitive 레이어**
```
[ ] 필요한 sizing / spacing / radius / typography 값이 이미 있는가?
[ ] 없다면 기존 scale에 맞춰 추가했는가?
[ ] 절대값 사용 (다른 토큰 미참조)?
```

**Semantic 레이어**
```
[ ] 필요한 color.bg / color.fg / color.border 토큰이 있는가?
[ ] 없다면 기존 role/descriptor 컨벤션에 맞춰 추가했는가?
[ ] -pressed / -disabled 상태 토큰이 모두 있는가?
[ ] primitive만 직접 참조하고 있는가?
```

**Component 레이어**
```
[ ] comp 이름이 Figma 컴포넌트 이름과 일치하는가?
[ ] condition이 스타일 variant와 사이즈를 올바르게 분리하고 있는가?
[ ] 단일 variant 컴포넌트에 base를 사용했는가?
[ ] element가 모든 구성 요소를 커버하는가?
[ ] 모든 state(default / pressed / disabled 최소 3개)가 정의되어 있는가?
[ ] semantic만 참조하고 있는가? (primitive 직접 참조 없음)
[ ] icon 토큰이 label 토큰과 별도로 정의되어 있는가?
[ ] loading state가 필요한 컴포넌트에 추가되었는가?
```

**Figma 연동**
```
[ ] Token Studio에서 올바르게 resolved value가 표시되는가?
[ ] Figma component의 각 variant 상태가 올바른 토큰을 참조하는가?
[ ] 디자이너와 개발자가 같은 토큰명을 사용하고 있는가?
```

---

*BGZT Design System — Token Naming Convention v1.0*
*Primitive → Semantic → Component 3-Layer Architecture*

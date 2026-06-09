# Component Contract Convention

> 디자인 시스템 컴포넌트의 사용 명세를 정의하는 문서 포맷.
> AI가 화면을 자동으로 조합할 때, 어떤 컴포넌트를 어떻게 쓸지 판단하는 근거가 됩니다.

---

## 목적

Component Contract는 두 가지 역할을 합니다.

1. **디자이너·개발자 간 소통** — 컴포넌트의 의도와 제약을 명확히 전달
2. **AI 화면 조합** — Screen Brief를 받은 AI가 올바른 컴포넌트를 선택하고 올바른 값을 채울 수 있도록 구조화된 정보 제공

---

## 포맷 구조

```
Status       → 컴포넌트 성숙도
Purpose      → 한 줄 용도 설명
Use when     → 사용해야 하는 상황
Avoid when   → 사용하면 안 되는 상황
Replacement  → 대체 방법 (Deprecated / Internal일 때만 작성)
Note         → 추가 경고 또는 특이사항 (선택)
Key props    → 주요 속성 명세
Composition  → 자식 컴포넌트 규칙
Owner        → 컴포넌트가 속한 Figma 파일명
```

---

## 각 필드 작성 기준

### Status

컴포넌트의 현재 성숙도를 나타냅니다.

| 값 | 의미 |
|---|---|
| `Core` | 안정화 완료, 프로덕션 사용 가능 |
| `Beta` | 사용 가능하나 스펙 변경 가능성 있음 |
| `Experimental` | 실험적 도입 단계, 제거 또는 교체 가능성 있음 |
| `Deprecated` | 사용 중단 예정, `Replacement` 필드에 대체 방법 명시 |
| `Internal` | 디자인 시스템 제작 전용 하위 부품, 실제 화면·테스트 지면에서 직접 사용 불가 |

**AI 동작 기준**
- `Core` / `Beta` — 화면 조합에 사용 가능
- `Experimental` — 기존 컴포넌트가 참조하는 경우에만 허용, 신규 화면에서 직접 의존 금지
- `Deprecated` — 신규 화면에 사용 불가, `Replacement`의 컴포넌트로 대체
- `Internal` — 화면 조합에 직접 사용 불가. 단, `Core` 컴포넌트의 내부 composition으로 포함되는 것은 허용

---

### Replacement

`Deprecated` 또는 `Internal` 상태일 때만 작성합니다.

```
Replacement: 실제 slot content로 교체하거나 제거합니다.
Replacement: NewButton 컴포넌트로 대체합니다.
```

**작성 기준**
- `Deprecated` — 어떤 컴포넌트로 마이그레이션해야 하는지 명시합니다. 미정이면 `TBD`
- `Internal` — 실제 사용 시 어떻게 처리해야 하는지 명시합니다 (교체 / 제거)
- `Core` / `Beta` / `Experimental`은 이 필드를 작성하지 않습니다

**`Experimental`에 Replacement를 쓰지 않는 이유**
`Experimental`은 제거될지, 유지될지, 교체될지 자체가 미결정 상태입니다.
이 불확실성은 `Note` 필드로 표현하고, `Replacement`는 대체가 확정된 상황에서만 작성합니다.

---

### Purpose

컴포넌트가 **무엇을 하는지** 한 문장으로 설명합니다.

**작성 기준**
- 컴포넌트의 역할 중심으로 작성합니다
- "~을 위한 컴포넌트" 형식으로 끝내지 않습니다
- 읽는 사람이 컴포넌트를 본 적 없어도 이해할 수 있어야 합니다

```
좋은 예: 모바일 화면의 기본 페이지 컨테이너입니다.
나쁜 예: 페이지를 위한 컴포넌트입니다.
```

---

### Use when

이 컴포넌트를 **써야 하는 상황**을 작성합니다.

**작성 기준**
- 구체적인 맥락을 포함합니다 (어떤 화면에서, 어떤 목적으로)
- 여러 케이스가 있으면 줄바꿈으로 나열합니다
- AI가 이 텍스트를 보고 화면 맥락과 매칭하므로 맥락 단어를 명확히 씁니다

```
좋은 예: 화면 전체의 배경, navigation, section group을 하나의 페이지 구조로 조립할 때.
나쁜 예: 페이지가 필요할 때.
```

---

### Avoid when

이 컴포넌트를 **쓰면 안 되는 상황**을 작성합니다.

**작성 기준**
- 혼동하기 쉬운 상황을 명시합니다
- 대안 컴포넌트가 있으면 함께 안내합니다
- 당연한 내용은 생략합니다

```
좋은 예: 독립 카드, 모달, 부분 영역만 구성할 때는 사용하지 않습니다.
나쁜 예: 잘못된 상황에서는 사용하지 않습니다.
```

---

### Key props

컴포넌트의 주요 속성을 명세합니다.

**포맷**
```
- {prop명} [required | optional] {타입 또는 유효값} → 부연 설명 (필요한 경우만)
```

**타입 표기 규칙**

| 상황 | 표기 방법 | 예시 |
|---|---|---|
| 정해진 값 중 선택 | `값A \| 값B \| 값C` | `two-column \| three-column` |
| 디자인 토큰 참조 | dot path | `color.bg.*` |
| 문자열 | `string` | `string` |
| 숫자 | `number` | `number` |
| 참/거짓 | `boolean` | `boolean` |
| URL | `url` | `url` |

**작성 기준**
- 모든 prop을 나열하지 않고 **주요 prop만** 작성합니다
- `required`와 `optional`을 반드시 표기합니다
- 토큰으로 값을 받는 prop은 어떤 토큰 그룹인지 명시합니다
- 서로 의존하는 prop은 `→` 이후에 명시합니다

---

### Composition

이 컴포넌트 안에 들어올 수 있는 자식 컴포넌트 규칙을 작성합니다.

**포맷**
```
- {컴포넌트명} [{min}–{max}]   → 허용 개수
- A | B [1]                   → 둘 중 하나만
Order: fixed | flexible       → 순서 강제 여부
Cannot contain: ...           → 명시적 금지 (혼동 여지가 있을 때만)
```

**작성 기준**
- 자식이 없는 컴포넌트(leaf)는 `Composition: none`으로 작성합니다
- `Order: fixed` — 자식의 순서가 강제될 때 (예: header가 항상 위)
- `Order: flexible` — 자식의 순서가 자유로울 때
- `Cannot contain`은 실수가 예상되는 케이스에만 작성합니다

---

### Note

추가 경고, 제약, 특이사항이 있을 때만 작성합니다.

```
Note: This component may be removed or replaced.
Note: 이 컴포넌트는 iOS 전용입니다.
```

**작성 기준**
- 위의 다른 필드로 전달할 수 없는 정보일 때만 작성합니다
- 경고성 내용은 영문으로 써도 무방합니다
- 없으면 생략합니다

---

### Owner

컴포넌트가 속한 **Figma 파일명**을 작성합니다.

```
Owner: Platform Design System
```

**작성 기준**
- Figma 파일명 그대로 작성합니다
- 팀명이나 개인명이 아닌 파일명 기준입니다
- 여러 파일에 걸쳐 있을 경우 주 파일명을 기준으로 합니다

---

## 전체 예시

### 단순 컴포넌트 — button

```
Status: Core
Purpose: 사용자의 단일 액션을 트리거하는 버튼입니다.
Use when: 폼 제출, 화면 이동, 기능 실행 등 명확한 액션이 필요할 때.
Avoid when: 텍스트 링크처럼 인라인으로 들어가야 할 때는 Text Button을 사용합니다.
Key props:
- variant [required] brandSolid | neutralSolid | brandWeak | neutralWeak | positiveWeak
- size [required] size-xs | size-sm | size-md | size-lg | size-xl
- text [required] string
- width [optional] full | auto → 기본값 auto, 섹션 하단 CTA는 full 사용
- enabled [optional] boolean → 기본값 true
Composition: none
Owner: Platform Design System
```

---

### 중간 복잡도 — sectionHeader

```
Status: Core
Purpose: 섹션 제목과 선택적 우측 액션을 표시하는 헤더입니다.
Use when: productCardGrid, carousel, textBlock 등 콘텐츠 섹션의 상단에 제목이 필요할 때.
Avoid when: 페이지 최상단 타이틀에는 navigationBar를 사용합니다.
Key props:
- variant [required] base | disclosure | ad
- text [required] string
- actionText [optional] string → variant=disclosure일 때 Text Button에 입력
Composition: none
Owner: Platform Design System
```

---

### 복잡한 컴포넌트 — ProductCard

```
Status: Core
Purpose: 2열 상품 리스트에서 사용하는 ProductCard 마스터 컴포넌트입니다.
Use when:
  페이지 본문에서 2column 상품 그리드 또는 2열 상품 리스트를 구성할 때.
Avoid when:
  3column 상품 리스트가 필요한 경우에는 ProductCard ThreeColumn을 사용합니다.
Key props:
- image [required] url → thumbnail image fill
- title [required] string → title label
- price [required] number → price label
- discountRate [optional] number
- brandBadge [optional] edition1 | mercari | care
- favorite [optional] boolean → 기본값 false
Composition:
- ProductCard Thumbnail [1]
- ProductCard Info TwoColumn [1]
Order: fixed (ProductCard Thumbnail → ProductCard Info TwoColumn)
Owner: Platform Design System
```

---

### Internal 하위 부품 — ProductCard Thumbnail

```
Status: Internal
Purpose: ProductCard 내부에서 상품 이미지를 표시하는 썸네일 컴포넌트입니다.
Use when:
  ProductCard TwoColumn / ThreeColumn 내부에서 상품 이미지, AD 뱃지, 브랜드 뱃지, 찜 상태를 함께 표시할 때.
Avoid when:
  화면에서 썸네일을 단독으로 직접 배치해야 하는 경우에는 DSImage 또는 별도 이미지 패턴을 사용합니다.
Replacement: ProductCard TwoColumn 또는 ProductCard ThreeColumn 내부 composition으로 사용합니다.
Key props:
- favorite [required] off | on
- brandBadge [optional] boolean → 브랜드 뱃지 노출 여부
- adBadge [optional] boolean → AD 뱃지 노출 여부
Composition:
- DSImage [1]
- Ad Badge [0–1]
- ProductCard Brand Badge [0–1]
- ProductCard Favorite Toggle [1]
Order: fixed overlay
Owner: Platform Design System
```

---

### 컨테이너 컴포넌트 — Section

```
Status: Core
Purpose: 화면을 구성하는 단위 블록으로, 헤더 슬롯과 컨텐츠 슬롯을 가진 컨테이너입니다.
Use when: 화면 내 독립된 컨텐츠 영역을 구분할 때.
Avoid when: 단순 여백 조정 목적으로는 사용하지 않습니다.
Key props:
- contentPaddingX [required] page | none → 좌우 패딩 여부, 기본값 page
- contentPaddingTop [optional] none | sm | md | lg | xl
- contentPaddingBottom [optional] none | sm | md | lg | xl
Composition:
- sectionHeader [0–1]
- image | textBlock | productCardGrid | productCardCarousel | button [1] (택 1)
Order: fixed (sectionHeader → content)
Owner: Platform Design System
```

---

### Beta 컴포넌트 — DSIcon

```
Status: Beta
Purpose: 컴포넌트화 대상이 아닌 1회성 커스텀 슬롯에서 DS 아이콘 소스를 표준 size와 tint 규칙에 맞춰 표시하는 아이콘 래퍼입니다.
Use when: 1회성 커스텀 슬롯 안에서 시스템 아이콘 또는 원본 컬러 유지 아이콘을 단독으로 배치해야 할 때 사용합니다.
Avoid when: Button, Navigation Bar, Toggle, Badge 등 기존 컴포넌트 내부에 포함되는 아이콘에는 사용하지 않습니다. 반복 사용되거나 2개 이상 화면에서 재사용될 가능성이 있는 아이콘 UI는 별도 컴포넌트화를 검토합니다.
Key props:
- size [required] xxxxs | xxxs | xxs | xs | sm | md | lg | xl | xxl | xxxl
- tint [required] none | neutral | brand | positive | inverse
- icon [required] INSTANCE_SWAP → sic.* 또는 iic.* DS 아이콘 소스
Composition:
- icon slot [1]
Owner: Platform Design System
```

---

### Internal 컴포넌트 — SlotPlaceholder

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

---

### 최상위 컨테이너 — Page

```
Status: Core
Purpose: 모바일 화면의 기본 페이지 컨테이너입니다.
Use when: 화면 전체의 배경, navigationBar, section group을 하나의 페이지 구조로 조립할 때.
Avoid when: 독립 카드, 모달, 부분 영역만 구성할 때는 사용하지 않습니다.
Key props:
- backgroundColor [optional] color.bg.* → 기본값 color.bg.neutral.surface
Composition:
- navigationBar [0–1] (항상 최상단 고정)
- section [1+]
Order: fixed (navigationBar → section)
Cannot contain: modal, bottomSheet
Owner: Platform Design System
```

---

## 작성 체크리스트

Contract를 작성하고 나서 아래 항목을 확인합니다.

## 자주 하는 실수

| 실수 | 올바른 방법 |
|---|---|
| `Experimental`에 `Replacement: TBD` 작성 | `Replacement` 생략, 불확실성은 `Note`로 표현 |
| `Deprecated`에 `Replacement` 없이 작성 | 대체 컴포넌트 명시 필수, 미정이면 `TBD` |
| Purpose를 "~을 위한 컴포넌트입니다"로 작성 | 컴포넌트가 하는 역할 중심으로 한 문장 작성 |
| Key props에 required / optional 표기 생략 | 모든 prop에 `[required]` 또는 `[optional]` 명시 |
| 토큰을 받는 prop에 타입을 `string`으로만 표기 | `color.bg.*` 같은 dot-path 토큰 그룹 명시 |
| 서로 의존하는 prop 관계 미명시 | `→` 이후에 의존 관계 설명 (예: `originalPrice → discountRate 함께 작성`) |
| 모든 prop을 빠짐없이 나열 | 주요 prop만 작성, 자명한 prop은 생략 |
| leaf 컴포넌트에 `Composition` 필드 생략 | `Composition: none` 명시 |
| 컨테이너 컴포넌트에 `Order` 미작성 | `Order: fixed` 또는 `Order: flexible` 명시 |
| `Cannot contain`을 모든 컨테이너에 작성 | 실수가 예상되는 케이스에만 작성 |
| `Owner`에 팀명이나 개인명 작성 | Figma 파일명 그대로 작성 |

---

## 작성 체크리스트

- [ ] `Status`가 Core / Beta / Experimental / Deprecated / Internal 중 하나로 작성되어 있는가
- [ ] `Deprecated` / `Internal`이면 `Replacement` 필드가 작성되어 있는가 (미정이면 `TBD`)
- [ ] `Experimental`이면 `Replacement` 대신 `Note`로 불확실성을 표현했는가
- [ ] `Purpose`가 한 문장으로 작성되어 있는가
- [ ] `Use when`에 구체적인 화면 맥락이 포함되어 있는가
- [ ] `Key props`에 required / optional이 모두 표기되어 있는가
- [ ] 토큰을 값으로 받는 prop에 토큰 그룹이 명시되어 있는가
- [ ] 서로 의존하는 prop이 있다면 `→`로 관계가 설명되어 있는가
- [ ] 자식이 없는 컴포넌트는 `Composition: none`으로 명시되어 있는가
- [ ] 컨테이너 컴포넌트는 `Order`가 명시되어 있는가

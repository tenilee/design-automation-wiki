# DesignSystem 규칙 명세

## 0. 왜 디자인 시스템인가

### 0.1 해결하려는 문제

제품이 커지면 여러 종류의 문제가 동시에 발생한다.

**값의 불일치**

같은 "보조 텍스트"인데 한쪽은 12pt에 opacity 0.62, 다른 쪽은 13pt에 opacity 0.72가 들어간다. 이 차이가 의도인지 실수인지 아무도 모르게 되고, 수정하려면 모든 사용처를 추적해야 한다.

**요구사항의 끝없는 변형**

"이 화면만 여백을 좀 좁게", "이 카드만 라운딩을 조금만 더", "이 섹션만 타이틀 크기를 한 단계 크게". 각 요청은 단독으로는 합리적으로 보이지만, 전체를 합치면 일관성이 사라진다.

**예외의 누적**

한 번의 예외는 허용 가능하지만, 예외가 열 번 반복되면 원칙이 먼저 무너진다. 새 기능이 추가될 때마다 "기존 것도 예외였으니 이것도"라는 논리가 작동하고, 시간이 지나면 예외가 표준이 되고 원래의 기준이 예외가 된다.

**반복되는 재결정 비용**

토큰과 컴포넌트가 없으면 모든 화면에서 "이 간격은 몇 pt여야 하는가"를 다시 결정한다. 이 결정은 언제나 그때그때 다른 결론으로 이어지고, 디자이너와 개발자는 매 화면마다 같은 고민을 반복한다.

---

디자인 시스템(이하 DS)은 이 문제들을 **한 곳에서 정의하고, 여러 곳에서 참조하며, 참조 밖의 결정은 원천적으로 차단하는 구조**로 해결한다.

### 0.2 이 시스템의 핵심 원칙

**단일 진실 원천 (Single Source of Truth)**

모든 시각적 결정(색상, 서체, 간격, 크기, 라운딩)은 토큰으로 정의한다. 컴포넌트는 토큰을 참조할 뿐, 값을 직접 품지 않는다. 값을 바꾸고 싶으면 토큰을 바꾸면 된다.

**의미 기반 추상화**

"Red 500"이 아니라 "Brand Solid 배경색"으로 참조한다. 값이 아닌 의미에 의존하므로, 브랜드 색상이 바뀌어도 참조하는 쪽은 영향을 받지 않는다. 다크모드 대응도 이 계층에서 자동으로 해결된다.

**합성 우선 (Composition over Inheritance)**

작은 기본 컴포넌트(Text, Icon, Image, Button, Badge 등)를 먼저 만들고, 이를 조합하여 더 큰 컴포넌트(NavigationBar)나 도메인 컴포넌트(ProductCard)를 만든다. 배치 컨테이너(Grid, Carousel)는 배치만 담당하고 내용물에 관여하지 않는다. 컴포넌트 간 결합을 최소화하여 독립적으로 진화할 수 있게 한다.

**범용과 도메인의 분리**

도메인 무관한 범용 컴포넌트와 도메인 특화 컴포넌트를 명확히 구분한다 (범용 / 도메인 두 갈래). Text, Button은 어떤 제품에도 쓸 수 있지만, ProductCard는 커머스 도메인에 종속된다. 이 경계가 흐려지면 범용 컴포넌트에 도메인 로직이 침투하여 재사용성이 떨어진다.

**적정 토큰화**

모든 값을 토큰으로 만드는 것이 목표가 아니다. 디자인 의도가 담긴 결정만 토큰화한다. 모든 DS 컴포넌트는 Component Token을 통해 속성값을 관리하며, 사용처에서는 토큰 체계 안에서만 값을 선택할 수 있다.

**제약이 곧 기능이다 (Constraints as Feature)**

DS의 힘은 무엇을 허용하는가가 아니라 **무엇을 차단하는가**에서 나온다.

- 사용처에서 임의의 간격 값을 넣을 수 없어야 Section 간 리듬이 유지된다.
- 사용처에서 임의의 color를 넣을 수 없어야 다크모드 전환이 한 곳에서 해결된다.
- 사용처에서 임의의 typography를 조합할 수 없어야 위계 읽기가 일관된다.

즉 제약은 DS의 부작용이 아니라 DS가 제공하는 기능 그 자체다. 제약이 없으면 토큰과 컴포넌트는 단순한 헬퍼 라이브러리이지 시스템이 아니다.

### 0.3 DS가 작동하기 위한 전제

위 원칙들은 다음 전제 위에서만 작동한다. 전제가 흔들리면 토큰과 컴포넌트가 아무리 잘 짜여 있어도 시스템은 유지되지 않는다.

**규칙성은 사용자를 위한 것이다**

Section 간 여백을 균일하게 유지하는 이유는 "보기 좋아서"가 아니라, 사용자가 콘텐츠 경계를 **매번 새로 판단하지 않아도 되게 하기 위해서**다. 여백이 화면마다 다르면 사용자는 "이건 같은 그룹인가 다른 그룹인가"를 다시 계산한다. 규칙성의 수혜자는 디자이너가 아니라 사용자다.

**변형 요청은 대체로 "구조 문제"다**

"이 섹션만 간격을 다르게"라는 요청이 들어오면, 값을 노출하기 전에 **왜 그 섹션이 다른가**를 먼저 묻는다. 답이 "여기부터 다른 종류의 콘텐츠가 시작되기 때문"이라면 그것은 값 문제가 아니라 구조 문제이고, 해답은 Shell 분리 또는 Section Group의 도입이지 간격 override가 아니다. DS는 이 질문을 강제하는 장치로 작동한다.

**예외는 누적되고 시스템을 잠식한다**

한 번의 override는 무해해 보이지만, 각 override는 "왜 이것만 예외인가"의 기록을 남기지 않는다. 6개월 후에는 override가 표준이 되고, 원래의 토큰 값이 예외가 된다. DS는 예외를 금지하는 것이 아니라, **예외를 구조적으로(명명된 variant, 새 Shell, 새 토큰으로)** 표현하게 강제한다.

**탈출구는 명시적이어야 한다**

DS를 벗어나는 것 자체는 문제가 아니다. 문제는 **몰래** 벗어나는 것이다. 한 속성에 토큰 바깥의 임의 값을 직접 꽂아 넣는 방식의 탈출은 다른 사람에게 탈출이 일어났음을 알리지 않는다. DS에서 벗어나려면 Shell 바깥으로 나가야 하고, 그 사실이 **명시적으로(명명된 탈출 경로와 리뷰 절차로)** 드러나야 한다.

### 0.4 설계 방향

**토큰은 3계층으로 구성한다**

Primitive(원시 값) → Semantic(의미 부여) → Component(컴포넌트 바인딩). 이 계층을 건너뛰지 않는다. 컴포넌트가 Primitive를 직접 참조하면 "왜 이 값인가"를 추적할 수 없게 된다.

**컴포넌트는 용도 카테고리로 묶고, 범용/도메인만 구분한다**

컴포넌트는 용도 카테고리(Buttons / Controls / Display / Feedback / Layout / Navigation / Domain)로 묶는다(§11.1). 분류상 유지하는 유일한 구분은 **범용 vs 도메인** — 접두사를 가른다. 무엇을 담고 배치하고 표시하는지(합성 위치)는 §11.1 레이어 모델이 기술하며, Leaf/Composite 같은 세부 역할 분류는 두지 않는다.

**Layout 가족 경계**: Layout은 동종 반복 아이템을 배치하는 컨테이너 가족이다. 이종 의미 단위(Section 등)는 자식으로 두지 않는다. 차원(1D/2D), 방향, 스크롤 모드는 멤버별로 다르지만 "반복 단위를 배치"라는 본질은 공통이다.

**스크롤 같은 동작은 규칙으로 정의한다**

모든 것을 컴포넌트로 감쌀 필요는 없다. Pull-to-refresh, pagination, scroll-to-top처럼 사용처마다 조합이 달라지는 동작은 컴포넌트보다 규칙이 적합하다. 규칙은 "언제, 어떤 조건에서, 어떻게 동작하는가"를 명세하고, 구현은 사용처에 맡긴다.

**기존 컴포넌트를 먼저 합성한다**

새로운 UI가 필요할 때, 기존 컴포넌트를 조합하여 만들 수 있는지 먼저 검토한다. Grid 와 ProductCard 를 조합해 상품 목록을 구성하는 것처럼, 새 컴포넌트를 만들기 전에 합성으로 충분한지 확인한다.

### 0.5 이 시스템의 정체 — 어휘 시스템 vs 스타일 라이브러리

DS 를 만들 때 가장 본질적인 갈림길은 **"DS 가 무엇을 책임지는가"** 다. 두 길이 있다.

|                  | 어휘 시스템 (Vocabulary)                                           | 스타일 라이브러리 (Styling Kit)          |
| ---------------- | ------------------------------------------------------------------ | ---------------------------------------- |
| 본질             | 골격을 강제하는 어휘 (Page / SectionGroup / Section / Layout 가족) | 토큰화된 표현 도구 (Stack + Gap + Token) |
| 제공하는 것      | "이건 Section 이다" 라는 의미 단위                                 | "이 값으로 띄워라" 라는 표현 수단        |
| 자유도           | 정의된 어휘 안                                                     | 무한 — 모든 디자인 표현 가능             |
| 일관성 보장 위치 | 컴파일러 / 골격                                                    | 없음 (코드리뷰가 잡아야 함)              |
| 상태 분기의 거처 | Section.content 한 곳                                              | 페이지마다 결정                          |
| 리뷰 언어        | "이 Section 의 header 가…"                                         | "여기 gap 12, 저기 gap 8"                |

기술적으로는 두 길 모두 가능하다. Stack + Gap + Token 만 있으면 Section / SectionGroup 없이도 모든 레이아웃을 표현할 수 있다. 하지만 그 시스템은 **Design System 이 아니라 styling library** 다. 둘은 같은 게 아니다.

**이 DS 는 어휘 시스템을 선택한다.** Section / SectionGroup 은 단순한 "stack with gap" 이 아니라 header / footer / content 슬롯, 상태 분기의 거처, grid padding 의 단일 결정 자리, 의미 단위의 경계, 페이지 골격 위치라는 **여러 의무를 묶은 어휘** 이고, 이 어휘를 잃으면 페이지 골격 일관성을 보장할 위치가 사라진다.

**유연성은 어휘 안쪽으로 밀어 넣는다**

```
[고정 골격]  Page → SectionGroup → Section
              ↑ 여기까진 어휘로 강제

[자유 영역]  Section.content 안
              ↑ 여기서부턴 native + 토큰으로 자유 조합
```

골격을 어휘로 잠그고, 안은 풀어주는 구조. 이것이 "기존 디자인의 다양성" 과 "DS 의 일관성" 을 동시에 만족시키는 유일한 방법이다.

**"기존 케이스가 다양하다" 는 거의 항상 구조 신호다**

"기존 디자인이 많아서 어휘로 다 못 담는다" 는 추론은 자연스러워 보이지만, 실제로는 두 가지 가능성이 있다.

| 해석                          | 의미                                          | 답                              |
| ----------------------------- | --------------------------------------------- | ------------------------------- |
| A. 도메인이 본질적으로 다양함 | 100가지 다른 골격이 정말로 필요               | 어휘 확장 또는 styling kit 전환 |
| B. 일관성 없이 자라온 결과    | 80% 가 같은 골격으로 환원되는데 표현이 제각각 | 어휘 도입으로 정리              |

번개장터 화면을 검토하면 거의 항상 **B** 다. 표면적 다양성은 Section.content 내부의 다양성이지 골격의 다양성이 아니다. 이 경우 어휘를 풀어버리면 **현재의 비일관성을 DS 에 내재화** 하게 되고, 한 번 그렇게 결정되면 되돌리기 매우 어렵다.

**골격 어휘를 벗어나야 하는 화면은 다른 Shell 카테고리로 간다**

Splash, 풀스크린 이미지 뷰어, 부팅 화면처럼 정말로 Page 골격을 따를 수 없는 화면은 L2 Page 의 어휘를 비트는 게 아니라 **L1 BootShell / L3 Sheet / L4 Overlay** 로 간다 (§11.1, §12). 이게 어휘 시스템이 제공하는 공식 예외 경로다.

**Stack / 보편 Gap 같은 "수단" 은 DS 어휘에 들이지 않는다**

Stack 은 "수직으로 쌓는다" 라는 순수 기하 동작이고, Gap-as-universal 은 "임의의 두 요소 사이 간격" 이라는 보편 표현이다. 둘 다 **수단** 이지 **규칙** 이 아니다. DS 의 본질은 수단의 제공이 아니라 규칙의 강제이고, 수단을 어휘에 들이는 순간 DS 가 styling kit 으로 변한다. 따라서:

- **수직 조합이 반복되는 패턴이면** → Stack 이 아니라 명명된 Item 컴포넌트로 승격 (의미 있는 이름을 받는다).
- **진짜 일회성 자유 조합이면** → DS 어휘 밖. native 컨테이너 + 토큰 참조 (`VStack(spacing: GapY.md.spacingValue)` 같은 형태) 로 처리하고 DS 는 관여하지 않는다.

**플랫폼이 결정하는 것 / DS 가 명세하는 것 / 토큰이 가지는 것**

이 정체성에서 자연스럽게 따라오는 책임 분리:

| 무엇이                                | 누가 결정       | 자유도             |
| ------------------------------------- | --------------- | ------------------ |
| Item 컴포넌트의 존재·입력·시각 구조 | DS 명세         | 0 (계약)           |
| 내부 간격 값 (예: title↔price = 5)    | 토큰 레지스트리 | 0 (값 고정)        |
| 그 토큰을 어떻게 코드로 박을지        | 각 플랫폼       | 자유 (native 수단) |

5 라는 숫자는 **토큰 레지스트리** 한 곳에 산다. 그 5 를 ProductCard 어디에 쓰는지는 **Item 명세** 한 곳에 산다. SwiftUI 가 `VStack(spacing: GapY.xs)` 으로 짜든 Compose 가 `Arrangement.spacedBy(GapY.xs)` 로 짜든 — DS 는 관여하지 않는다. DS 가 보장하는 건 **결과물의 토큰 일관성과 골격 어휘** 이지 **구현 코드의 형태** 가 아니다. Figma 는 동일 토큰을 시각적으로 참조 표기할 뿐 진실의 원천은 아니다.

**약속의 명문화**

이 DS 는 다음을 약속하고, 다음은 약속하지 않는다.

> **약속한다**: 페이지 골격 일관성 (Page → SectionGroup → Section), 상태 분기의 거처 (Section.content), 토큰 일관성, 어휘 기반 리뷰 언어, 예외 경로의 명시성 (다른 Shell 카테고리).
>
> **약속하지 않는다**: 모든 기존 디자인 케이스의 1:1 표현, native 구현 코드 형태의 통일, Figma 산출물의 자동 동기화, Stack / 보편 Gap 같은 표현 수단의 제공.

이 선언은 새 컴포넌트 / 새 어휘 추가 논의가 올라올 때마다 의사결정의 기준선이 된다. "이게 어휘인가 수단인가, 의미 단위인가 표현 도구인가" 를 먼저 묻고, 수단·표현 도구 쪽이면 DS 어휘 바깥으로 둔다.

---

## 1. 토큰 체계

### 1.1 계층 구조

```
Primitive  →  Semantic  →  Component
```

| 계층      | 정의                                            | 네이밍 예시                                                                                    |
| --------- | ----------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Primitive | 의미 없는 원시 값 또는 안정적인 원본 식별자     | `color.red.500`, `spacing.4`, `font.size.14`, `asset.icon.system.close`                            |
| Semantic  | 쓰임새에 의미를 부여한 값                       | `color.bg.brand.solid.default`, `spacing.gap-x.xs`, `typography.label1.bold`, `icon.action.close`  |
| Component | 컴포넌트 variant/size/state/slot 에 바인딩한 값 | `button.container.color.brandSolid.enabled`, `badge.container.padding-x.size-md`, `icon.size.md`   |

- **Component Token 은 값을 갖지 않는다** — 컴포넌트의 분기(variant/size/state)를 Semantic 토큰에 매핑할 뿐이고, 값은 Semantic 이 갖는다(Semantic-bound, default). 예외적으로 Semantic 에 대응 단계가 없는 *컴포넌트 고유 값* 만 Primitive 를 직접 참조한다(Component-specific). 상세는 [§1.4](#14-component-token-의-정체성과-두-형태) · [§1.5](#15-토큰화-정책-어디까지-토큰으로-둘-것인가).

#### 에셋 토큰과 manifest

아이콘·이미지 같은 파일 기반 리소스도 **Primitive → Semantic → Component** 흐름을 따른다. Primitive asset token 의 값은 실제 파일 경로가 아니라 안정적인 원본 식별자 `asset://<assetId>` 다.

```text
Component asset token
       │ {semantic.icon.navigation.chevronRight}
       ▼
Semantic asset token
       │ {asset.icon.system.chevronRight}
       ▼
Primitive asset token
       │ asset://icon/system/sic-chevron-right
       ▼
Assets/manifest.json
       │ assetId -> source path
       ▼
원본 에셋 파일
```

설계 원칙은 다음과 같다.

- `studio-export/primitive/asset.json` 은 primitive asset token 의 단일 token set 이다. `icon`, `image`, `lottie` 는 `asset` 아래 namespace 로 둔다.
- `Assets/manifest.json` 은 의미 계층을 결정하지 않고 `assetId` 와 실제 파일 경로만 연결한다.
- semantic/component asset token 은 의미와 컴포넌트 slot 연결을 담당한다.
- 플랫폼별 산출물은 각 플랫폼 저장소가 `assetId` 계약을 기준으로 변환한다. 이 레포에는 공통 계약과 원본 리소스만 둔다.
- assetId 생성 규칙, manifest 갱신, Figma 반영, Token Studio import 절차는 [Assets/README.md](../Assets/README.md)를 단일 운영 문서로 따른다.

### 1.2 토큰 바인딩 규칙

디자인 시스템 컴포넌트는 **토큰 체계 바깥의 값이 들어올 수 없도록 제한**한다. 이것이 컴포넌트가 DS에 존재하는 근본 이유이다.

제한의 강도는 세 단계로 나뉜다:

| 강도            | 방식                                           | 의미                                         |
| --------------- | ---------------------------------------------- | -------------------------------------------- |
| **고정**        | 값을 토큰이 결정, 사용처에서 변경 불가         | "이 값은 이것이다"                           |
| **선택 제한**   | 토큰 체계 안에서만 선택 가능                   | "이 범위 안에서 골라라"                      |
| **기본값 제공** | 토큰 기본값이 있고, 토큰 범위 내에서 변경 가능 | "기본은 이것이고, 바꾸려면 이 안에서 골라라" |

**컴포넌트별 바인딩:**

각 컴포넌트가 어떤 속성을 어떤 강도로 어느 토큰에 묶는지는 **해당 컴포넌트 문서**(`Components/<Category>/<Name>.md` 의 「속성」·「토큰」)가 단일 출처다. 여기서는 강도 3단계가 실제로 어떻게 갈리는지 예시만 든다:

| 컴포넌트 | 속성 | 강도 | 토큰 |
| --- | --- | --- | --- |
| Text | typography, color | 선택 제한 | Typography, SemanticColor |
| Button | height·padding·radius·background·text color | 고정 | ButtonToken |
| Image | placeholder | 기본값 제공 | ImageToken.Placeholder.* |

> 전체 컴포넌트 목록·분류는 [§11.1 분류](#111-분류), 컴포넌트별 상세 바인딩은 각 컴포넌트 문서를 참조.

공통 원칙: 어떤 강도든 **토큰 체계 바깥의 임의 값은 입력할 수 없다.**

### 1.3 네이밍 규칙

각 계층의 이름 **구성 규칙**만 정의한다. 실제 토큰 이름 목록은 **토큰 소스가 단일 진실원**이므로 여기 나열하지 않는다 (토큰 추가·개명·삭제 시 문서 재정렬 불필요).

| 계층 | 패턴 | 예 (illustrative) |
| --- | --- | --- |
| **Primitive** | `{영역}.{패밀리\|값}` | `color.red.500` · `spacing.16` · `font.size.14` |
| **Semantic** | `{영역}.{카테고리}.{용도\|단계}[.{상태}]` | `color.fg.neutral.contrast.default` · `spacing.gap-x.xs` · `typography.label1.bold` |
| **Component** | `{컴포넌트}.{part}.{속성}[.{variant\|size\|state}]` | `button.container.color.brandSolid.enabled` · `badge.container.padding-x.size-md` |

**컨벤션:**

- 컴포넌트 이름은 **camelCase** (`navigationBar`·`productCard`).
- 다중어 속성·축은 **kebab** (`gap-x`·`padding-x`).
- 분기 qualifier 는 뒤에 붙는다 — variant(`brandSolid`·`box`·`twoColumn`) · size(`size-md` suffix) · state(`default`·`enabled`·`pressed`·`disabled`).
- semantic 아이콘은 `icon.{category}.{name}` (별도 `semantic.` 접두사 없음).

### 1.4 Component Token 의 정체성과 두 형태

Component Token 은 **호출자의 분기(`variant`/`size`/`state`/`shape`)를 값에 매핑**하는 layer 다. **값 자체는 Semantic 토큰이 가진다** — Component Token 은 "이 분기일 때 어느 Semantic 토큰을 쓸지"만 고른다.

```
ProductCardToken.Info.Container.GapY.twoColumn    →  GapY.xs
ProductCardToken.Info.Container.GapY.threeColumn  →  GapY.xxs
```

→ variant 분기가 각각 다른 Semantic 토큰(`GapY` 단계)을 가리킨다.

**두 형태:**

- **Semantic-bound** (default, 거의 전부) — Semantic 토큰을 참조(spacing·sizing·radius·typography·color).
- **Component-specific** (예외) — Semantic 에 대응 단계가 없는 *그 컴포넌트만의 고유 값* 일 때만 Primitive 를 직접 참조한다. 손에 꼽을 정도여야 정상이며, 많아지면 Scale 정의가 불완전하다는 신호(§1.5).

### 1.5 토큰화 정책 (어디까지 토큰으로 둘 것인가)

여기서 **시스템 Scale** = 치수 계열 Semantic 토큰(Spacing·Sizing·Radius·Typography)이 이루는 *절제된 단계 사다리* 를 가리킨다 — 별도 계층이 아니라 Semantic 의 한 부분.

**원칙:**

> **치수 차원** (spacing / sizing / radius / typography / border-width) 에 해당하는 모든 수치는 시스템 Scale 토큰 또는 Component Token 을 참조한다. 코드·문서에서 인라인 매직 넘버 금지.
>
> **추상 단위** (count / ratio / opacity / scale-factor / lineLimit 등) 와 **구조적 0** 은 인라인 허용.

인라인 금지의 목적은 변경 단일점이나 가독성이 아니라 **디자인 자유도 제한** 이다 — 디자이너·개발자가 시스템 Scale 안에서만 값을 선택하도록 강제하는 메커니즘. 그래서 적용 범위는 시스템 Scale 이 정의된 치수 차원에 한정된다.

**판단 절차 (치수 차원에 한해):**

```
1. 이 값이 호출자의 분기 입력 (variant / size / state / shape) 에 따라 다른가?
   Yes → Component Token 등재 (분기 표현, 값은 시스템 Scale 참조)
   No  → 다음 단계

2. 이 값이 시스템 Scale (Spacing / Sizing / GapX / GapY / Radius / Typography) 의 한 단계와 일치하는가?
   Yes → 그 Scale 토큰 직접 참조 (Component Token 등재 불필요)
   No  → 다음 단계

3. 이 값이 그 컴포넌트의 *정체성* 으로서 의식적 디자인 결정인가?
   Yes → Component Token (Component-specific) 등재. 또는 Scale 단계를 추가해 1 단계로 흡수.
   No  → 디자인을 Scale 안으로 끌어들이거나, 컴포넌트 내부 named constant 로 둔다.
```

**핵심 통찰:**

- **Scale 의 절제가 토큰의 가치를 만든다.** 0~100 의 모든 값을 토큰화하면 토큰은 _이름 붙은 매직 넘버_ 가 되어 가치를 잃는다. 시스템 Scale 이 좁고 절제되어 있을 때 토큰화의 의미가 살아난다.
- **Component Token 은 분기 매핑 layer 일 뿐, 값의 정의 layer 가 아니다.** 값의 정의는 항상 시스템 Scale 이 한다.
- **Component-specific 은 예외다.** 한 컴포넌트의 Component-specific 토큰이 많으면 (1) Scale 정의가 불완전하거나 (2) 디자인이 시스템 절제를 따르지 않는 신호로, 시스템 차원의 정정 대상이다.

**Scale 밖 값 처리:**

디자인 작업 중 시스템 Scale 에 없는 값이 시안에 등장할 수 있다. **default 는 _디자인 조정_** — 가장 가까운 Scale 단계로 수렴한다. 새 토큰 정의·Scale 단계 추가는 다음 세 기준을 모두 통과할 때만 정당화된다.

| 기준     | 질문                                                             | Scale 추가 정당화 |
| -------- | ---------------------------------------------------------------- | ----------------- |
| 재사용성 | 다른 컴포넌트에서도 사용되거나 사용될 가능성이 있는가            | Yes 일 때만       |
| 빈도     | 여러 디자인에서 반복 등장하는 패턴인가                           | Yes 일 때만       |
| 거리     | 기존 Scale 의 가장 가까운 단계와 2pt 이상 차이 + 의식적 결정인가 | Yes 일 때만       |

세 기준 중 하나라도 통과 못 하면 디자인 조정으로 처리한다.

홀수 단위 (7pt, 11pt, 13pt) 는 거의 항상 조정 대상이다 — Spacing scale 이 4 또는 8 의 배수 기반이 일반적이라 홀수는 시스템 절제 원칙에 본질적으로 어긋난다. (Typography 의 fontSize 는 예외 — typography scale 에 미리 포함.)

Scale 단계 추가는 _시스템 차원_ 의 결정이지 컴포넌트별 결정이 아니다. PR · 디자인 리뷰 · 시스템 채널을 통해 한 번에 결정한다.

---

## 2. 색상 체계

### 2.1 Primitive Color

14개 컬러 패밀리, 각 패밀리 내 명도 단계(shade)로 구성.

| 패밀리   | 단계        | 비고                |
| -------- | ----------- | ------------------- |
| Red      | v50 ~ v900  | 브랜드 색상 원천    |
| Green    | v50 ~ v900  | 긍정(Positive) 원천 |
| Blue     | v50 ~ v900  |                     |
| Orange   | v50 ~ v900  |                     |
| Yellow   | v50 ~ v900  |                     |
| Purple   | v50 ~ v900  |                     |
| Brown    | v50 ~ v900  |                     |
| Navy     | v50 ~ v900  |                     |
| Lime     | v50 ~ v900  |                     |
| Sand     | v50 ~ v900  |                     |
| Gray     | v0 ~ v1000  | 중립(Neutral) 원천  |
| CoolGray | v100 ~ v900 | 다크모드 중립 원천  |
| Black    | a2 ~ a90    | 투명도 단계         |
| White    | a2 ~ a90    | 투명도 단계         |

- 숫자가 클수록 어둡다 (v50 = 가장 밝음, v900 = 가장 어두움).
- Black/White는 투명도(alpha) 기반이다.

### 2.2 Semantic Color

모든 Semantic Color는 **Light/Dark 쌍**을 가진다. 다크모드는 토큰 수준에서 해결된다.

**분류 체계:**

```
SemanticColor
├── BG (배경)
│   ├── Brand    : solid, solidPressed, solidDisabled, weak, weakPressed, weakDisabled
│   ├── Neutral  : solid, weak
│   └── Positive : weak, weakPressed, weakDisabled
├── FG (전경/텍스트)
│   ├── Brand       : contrast, onSolid, onSolidPressed
│   ├── Brand.Weak  : contrastDisabled, contrastPressed
│   ├── Neutral     : contrast, onSolid
│   └── Positive    : contrast, contrastDisabled, contrastPressed
└── Layer
    └── basement
```

**의미 분류:**

| 의미     | 역할                                       |
| -------- | ------------------------------------------ |
| Brand    | 브랜드 정체성을 전달하는 색상 (Red 계열)   |
| Neutral  | 의미를 부여하지 않는 중립 색상 (Gray 계열) |
| Positive | 긍정적 의미를 전달하는 색상 (Green 계열)   |

**상태 접미사 규칙:**

| 접미사   | 의미         |
| -------- | ------------ |
| (없음)   | Default 상태 |
| Pressed  | 눌림 상태    |
| Disabled | 비활성 상태  |

**배경/전경 관계 규칙:**

| 배경 토큰        | 위에 올라가는 전경 토큰 |
| ---------------- | ----------------------- |
| BG.Brand.solid   | FG.Brand.onSolid        |
| BG.Neutral.solid | FG.Neutral.onSolid      |
| BG.Brand.weak    | FG.Brand.contrast       |
| BG.Neutral.weak  | FG.Neutral.contrast     |
| BG.Positive.weak | FG.Positive.contrast    |
| Layer.basement   | FG.Neutral.contrast     |

- `onSolid`: solid 배경 위 전경색
- `contrast`: weak 배경 또는 일반 배경 위 전경색

---

## 3. 타이포그래피 체계

### 3.1 Primitive Font

| 속성           | 값                                                  |
| -------------- | --------------------------------------------------- |
| **Family**     | Pretendard                                          |
| **Size**       | 10, 11, 12, 13, 14, 15, 16, 18, 20, 22, 24, 28, 34 (pt) |
| **Weight**     | 400(Regular), 500(Medium), 700(Bold)                |
| **LineHeight** | 120%, 130%, 140%, 145%, 150%, 160% |

### 3.2 Typography (Semantic)

Typography = Family + Size + Weight + LineHeight 조합. **구체 값(크기·굵기·행간)은 토큰 정의가 단일 진실원**이므로 문서는 이를 복제하지 않고, 카테고리·스케일·규칙만 정의한다.

| 카테고리 | 스케일 | 용도                   |
| -------- | ------ | ---------------------- |
| Title    | 1–4    | 대형 제목              |
| SubTitle | 1–4    | 소제목                 |
| Body     | 1–4    | 본문                   |
| Label    | 1–4    | 라벨·버튼 등 UI 텍스트 |
| Caption  | 1–2    | 보조 설명              |

- 각 카테고리 내 **숫자가 작을수록 큼**(예: `Title1` > `Title4`).
- Weight 는 토큰별로 `bold` · `medium` 중 정의된 것만 존재.
- 행간은 §3.3 규칙(fontSize 기준)을 따른다.
- 크기·굵기·행간의 실제 값은 **토큰 정의(`Typography.*`)를 참조** — 문서에 값 미복제.

**크기 스케일 명명:**

| 이름 | 순서        |
| ---- | ----------- |
| XS   | 가장 작음   |
| SM   | 작음        |
| MD   | 중간 (기본) |
| LG   | 큼          |
| XL   | 가장 큼     |

이 스케일은 Spacing, Sizing, Radius, 컴포넌트 Size에 공통 적용된다. (Typography 는 카테고리별 숫자 스케일 1~4 를 쓴다 — §3.2.)

### 3.3 행간(LineHeight) 계산

행간은 **폰트 크기(fontSize) 기준**으로 산출한다 — `행간 높이 = fontSize × 행간%`. 폰트 내부 metric(자연 line height)이 아니라 폰트 크기에 배수를 적용하므로, 같은 % 값이 폰트가 바뀌어도 예측 가능하게 동작한다(CSS `line-height` · 디자인 도구의 % 와 동일 기준).

**여백 분배(half-leading):** 행간 높이와 글리프 자연 높이의 차이는 위·아래로 절반씩 나눠 글리프를 line box 세로 중앙에 둔다. 그래서 한 줄 텍스트도 정의된 행간만큼 높이를 차지한다.

---

## 4. 간격(Spacing) 체계

DS 가 다루는 모든 공간은 **정확히 세 카테고리** 중 하나에 속한다. 어떤 간격도 이 세 카테고리 바깥에 존재할 수 없다.

| 카테고리    | 의미                                                                                                                         | Semantic 이름 |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------- | ------------- |
| **Inline**  | 수평으로 나열된 형제 요소 사이 간격                                                                                          | `GapX`        |
| **Stack**   | 수직으로 쌓인 형제 요소 사이 간격                                                                                            | `GapY`        |
| **Padding** | 컨테이너 경계와 내부 콘텐츠 사이의 여백. 외부 기준선(페이지 경계, safe area 등)으로부터의 들여쓰기도 이 카테고리에 수렴한다. | `Padding`     |

### 4.1 소유권 (누가 어떤 간격을 결정하는가)

역할 분류에 따라 소유 가능한 카테고리가 정해진다. 소유 경계가 흐려지면 더블 패딩, 정책 충돌, 디버깅 난도 상승이 바로 뒤따른다.

| 역할                                       | `GapX`        | `GapY`                          | `Padding`                |
| ------------------------------------------ | ------------- | ------------------------------- | ------------------------ |
| Layout (Stack / Grid / Carousel 등) | O — 자식 배치 | O — 자식 배치                   | **X**                    |
| Section                                    | —             | 내부 슬롯 간 (header ↔ body 등) | O — 좌우 경계 Padding    |
| Page / NavigationBar (페이지·바)               | —             | 페이지·바 내부 경계             | O — 화면·바 수준 Padding |

핵심: **Layout 은 Padding 을 소유하지 않는다.** Layout 은 "자식 사이 배치" 만 담당하고, "컨테이너-자식 경계 여백" 은 항상 Section 또는 Page/NavigationBar 가 소유한다. Layout 에 Padding 을 허용하면 그 둘과 이중으로 쌓여 정책이 무너진다.

### 4.2 Gap 슬롯의 의미

컨테이너의 gap 슬롯(`gapX` / `gapY`) 은 **그 컨테이너의 직접 자식들 사이** 만 의미한다. "자신과 형제 사이" 는 절대 의미하지 않는다.

| 예                                               | 누가 소유                                   |
| ------------------------------------------------ | ------------------------------------------- |
| Grid 내 아이템 간 (같은 행)                      | Grid 의 `gapX`                              |
| Grid 내 행 사이                                  | Grid 의 `gapY`                              |
| Section 내부 header ↔ content ↔ footer 수직 호흡 | Section 미보유 — 섹션 *간* 수직만 SectionGroup.gap (섹션 *내* 슬롯 수직 간격은 정의된 소유자 없음)   |
| Section ↔ Section                                | **부모 Layout / Page** (Section 자신 아님) |
| Page 자식 블록 사이                              | Page 가 소유한 content stack 의 `gapY`      |

블록이 자기 외곽 여백(top margin 등)을 스스로 주장하지 않는다는 원칙의 근거도 이것이다. 자기 바깥 간격은 부모만 안다.

### 4.3 슬롯 네이밍 규칙

간격 슬롯의 이름은 **컨테이너가 이미 규정한 맥락을 이름에 반복하지 않는다**. 두 종류의 중복이 있다 — 축 중복 · 스코프/타입 중복.

#### (a) 축 접두어 (`X` / `Y`) 규칙

컨테이너가 **여러 축을 노출할 때만** 축을 이름에 명시한다.

| 컨테이너 유형                     | 예                                                                                      | 명명           |
| --------------------------------- | --------------------------------------------------------------------------------------- | -------------- |
| 단일 축 (배치 방향이 역할로 고정) | Carousel (수평) · Page content stack (수직) · NavigationBar 시퀀스 (수평) | `gap`          |
| 2축 (X·Y 모두 의미 있음)          | Grid                                                                                    | `gapX`, `gapY` |

단일 축 컨테이너에서 `X`/`Y` 를 덧붙이는 건 컨테이너 정의상 자명한 축을 반복 표기하는 것이므로 금지.

#### (b) 스코프·타입 접두어 규칙

| 컨테이너 구조                                                                | 판단                      | 예                                                                 |
| ---------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------ |
| 자식 타입이 **단일** (Grid/Carousel 의 items, SectionGroup 의 sections) | 접두어 중복 → 금지. `gap` | ~~`itemGap`~~ / ~~`rowGapY`~~ / ~~`columnGap`~~ / ~~`sectionGap`~~ |
| **복수 영역/슬롯** 을 가진 컨테이너                                          | 스코프 접두어 허용        | (해당 사례 없음 — 도입 시 결정)                                    |

컨테이너 API 표면에 오직 하나의 gap 개념만 존재하면 접두어 없이 `gap` 만 쓴다. 컨테이너 이름이 자식 타입을 이미 표현하므로(예: `SectionGroup` 은 자식이 Section 임이 자명) 함수명에 자식 타입 접두어를 다시 붙이지 않는다.

#### (c) Padding 축 규칙

Padding 은 컨테이너 4면에 대한 여백으로, **4면이 각각 독립일 수 있다**는 것이 개념적 기본이다. 이 개념은 DS foundation 의 **`Padding` 값 타입** 으로 순수하게 표현된다. 모든 padding 적용 경로는 내부적으로 이 타입으로 환원된다.

**Foundation `Padding` 타입의 표준 생성자** (각각이 API 형태 정책과 1:1 대응):

| 생성자                            | 의미                  | 컴포넌트 사용 예                                          |
| --------------------------------- | --------------------- | --------------------------------------------------------- |
| `Padding(top:bottom:left:right:)` | 4면 모두 독립         | Card / Banner 등 4면 모두 자유                            |
| `Padding(horizontal:vertical:)`   | 2축 모두 대칭         | 양 축 대칭이 구조적으로 요구될 때                         |
| `Padding(horizontal:top:bottom:)` | 수평 대칭 + 수직 분리 | Section — 수평은 페이지 격자 불변식으로 대칭, 수직은 독립 |
| `Padding(vertical:left:right:)`   | 수직 대칭 + 수평 분리 | 수평 비대칭이 필요한 특수 케이스                          |

**컴포넌트는 자기 불변식에 맞춰 축소된 API 를 노출**하고 내부에서 위 생성자 중 하나로 `Padding` 값을 조립해 적용한다. 예를 들어 Section 의 `contentPadding(horizontal:)` 은 수평 단일 값만 받아 좌우 대칭("left=right") 을 컴파일 타임에 강제한다 (수직 padding 은 갖지 않음 — Section 간 수직 리듬은 SectionGroup 이 소유).

API 축소 형태의 추가 예:

- 한 축만 제어: `horizontalPadding` / `verticalPadding` / `topPadding` / `bottomPadding`
- 단일 값: `contentPadding(_ padding: Padding)` — 호출부가 직접 `Padding` 을 생성/재사용

§4.4 가 허용하는 축 어휘는 `top` / `bottom` / `left` / `right` / `horizontal` / `vertical` 이다. 컴포넌트는 이 중 필요한 조합을 선택해 API 를 노출하고, 내부적으로는 foundation `Padding` 으로 통일된다.

### 4.4 문서·Component 토큰 용어 규약 (위치/방향 어휘 시스템 정책)

DS 문서와 Component 토큰 이름에서 쓰는 용어는 Semantic 카탈로그의 이름(`GapX` / `GapY` / `Padding`) 과 **1:1 대응** 한다.

> **시스템 전 영역 위치/방향 어휘 정책**
> 본 절의 어휘 규칙은 Padding 에 한정되지 않는다. **DS 공개 API 의 모든 위치/방향/정렬 어휘** — Padding 축, 컴포넌트 슬롯 식별자(예: NavigationBar 의 좌/우 슬롯), 텍스트 정렬, 오버레이 정렬 등 — 에 동일하게 적용된다. 부분 도입을 금지한다.
> 플랫폼 SDK 의 reading-direction 어휘 (SwiftUI `.leading`/`.trailing`, UIKit `leading`/`trailing`, CSS Logical Properties `inline-start`/`inline-end`) 는 **DS → 플랫폼 매핑 구현 지점에서만** 등장한다 (예: `Padding` 적용부, `Text.Alignment` → SwiftUI `TextAlignment` 변환). DS 공개 API · 토큰 이름 · 문서 어휘 어디에도 노출되지 않는다.

| 용도            | 사용                                                                                                         | 사용 금지                                                                   |
| --------------- | ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| 수평 자식 간격  | `GapX` / "Inline 간격" / `gapX`                                                                              | `spacing`, `hSpacing`, `itemSpacing`, `columnGap`                           |
| 수직 자식 간격  | `GapY` / "Stack 간격" / `gapY`                                                                               | `spacing`, `vSpacing`, `rowSpacing`, `rowGap`                               |
| 컨테이너 여백   | `Padding` / `horizontalPadding` / `verticalPadding` / `topPadding` / `bottomPadding` / `contentPadding(...)` | `inset`, `insets`, `contentInsets`, `edgeInsets`, `fullBleed`, `edgeToEdge` |
| Padding 축 단위 | `top`, `bottom`, `left`, `right`, `horizontal`, `vertical`                                                   | `leading`, `trailing`, `start`, `end`, `all`                                |

금지 용어의 이유:

- `inset` / `insets` / `edgeInsets` — UIKit·SwiftUI 등 플랫폼 API 관용어. DS 문서는 플랫폼 중립.
- `fullBleed` — 인쇄 어원. 디지털 DS 맥락에서 모호.
- `edgeToEdge` — 웹 관용으로 편하지만 토큰 이름(`Padding`) 과 다른 어휘라 1:1 대응을 깬다.
- `spacing` / `itemSpacing` — Inline 과 Stack 두 카테고리를 뭉개므로 3분법이 흐려진다.
- `leading` / `trailing` / `start` / `end` — 읽는 방향 기준의 논리축 어휘. 특정 플랫폼 (SwiftUI · CSS Logical Properties) 관용어라 DS 어휘로는 사용하지 않는다. DS 는 물리 축 어휘 (`left` / `right`) 로 통일한다.
- `all` — 편의형 단축. 명시적인 4면 / 2축 어휘로 통일해 어휘 확산을 막는다.

허용된 축 어휘 (`top` / `bottom` / `left` / `right` / `horizontal` / `vertical`) 는 다음과 같이 쓴다:

- `top` / `bottom` — 수직 방향의 **분리된** 측면. top ≠ bottom 이 필요하면 두 파라미터를 따로 노출.
- `left` / `right` — 수평 방향의 분리된 측면 (물리 축). left ≠ right 이 필요하면 두 파라미터를 따로 노출.
- `horizontal` — left = right 이 **컴포넌트 불변식으로 강제**될 때의 단일 파라미터. `horizontal` 을 노출하는 순간 호출부가 좌우 비대칭을 표현할 방법이 없어짐 = 컴파일 타임에 좌우 대칭 보장.
- `vertical` — top = bottom 이 **컴포넌트 불변식으로 강제**될 때의 단일 파라미터. 동일 논리.

즉 `horizontal` / `vertical` 은 "4면 어휘의 단축형" 이 아니라 **"대칭을 강제하기 위해 선택되는 축약"** 이다. 컴포넌트 API 가 `top` / `bottom` / `left` / `right` 중 어떤 것을 노출하고 어떤 것을 `horizontal` / `vertical` 로 합치는지가 그 컴포넌트의 Padding 불변식을 선언한다.

> **i18n 주의**: DS 는 현재 LTR 로케일만 대상으로 한다. `left` / `right` 는 **물리 축** 어휘로, RTL 로케일 (아랍어 · 히브리어 등) 도입 시 의미가 자동 반전되지 않는다. 플랫폼 구현층 (SwiftUI `.leading`/`.trailing`) 은 자체적으로 읽는 방향을 반영하므로, DS foundation 의 `Padding` 값을 실제로 적용하는 지점에서 매핑이 이뤄진다. 향후 RTL 지원이 필요하면 이 매핑 지점 하나만 재검토하면 되며, DS 소비자 API 는 `left` / `right` 를 계속 쓴다.

Component 토큰의 카테고리 이름도 동일 규약을 따른다: 수평 자식 간격이면 `GapX.*`, 수직이면 `GapY.*`, 경계 여백이면 `Padding.*`. 과거 "spacing" 을 포괄적 접두어로 쓰던 Component 토큰은 해당 카테고리에 맞게 재네이밍 대상이다 (영향 범위: §16.3).

### 4.5 Primitive Spacing

0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 40, 44, 48, 52, 56, 60, 64 (pt)

### 4.6 Semantic Spacing

값은 토큰 정의가 단일 진실원 — 여기서는 카테고리·단계·소유 의미만 정의한다.

**GapX** (Inline — 수평 형제 간 간격) — 단계: `XXS · XS · SM · MD · LG · XL`

**GapY** (Stack — 수직 형제 간 간격) — 단계: `XXS · XS · SM · MD · LG · XL · XXL`

> **상태: 설계 확정 · 소비자 반영 대기.** 본 카테고리는 Token Studio 등재 후 codegen 을 통해 각 소비자에 배포된다. 현 시점의 소비자 구현은 아직 이 카테고리를 참조하지 않으며, 과거 `Padding.XL` 을 수직 간격 용도로 빌려쓰던 경로가 남아 있다 (영향 대상: §16.3).

- GapY 는 GapX 와 동일 스케일을 유지하되, 수직 형제 간격(섹션 간, 카드 내부 수직 요소 간, 그리드 행 간 등)이 자주 요구하는 한 단계를 **XXL 로 확장**한다. 해당 값은 §4.5 primitive 스케일에 이미 존재해 primitive 추가는 불필요.

**Padding** (컨테이너 내부 여백 / 외부 기준선으로부터의 들여쓰기) — 단계: `SM · MD · LG · XL`

- 이 카테고리는 "컨테이너가 내부 콘텐츠를 경계에서 얼마나 떨어뜨릴지" 와 "외부 규약으로부터 요소를 얼마나 들여 놓을지" 둘 다 수용한다. 두 의미는 소유자(컨테이너 자신 vs 외부 규약)가 다르지만, 실제 값 스케일이 겹치기에 같은 Semantic 카테고리를 공유한다.

---

## 5. 높이(Sizing) 체계

### 5.1 Semantic Height

값은 토큰 정의가 단일 진실원. 단계: `XS · SM · MD · LG · XL` (XS = 가장 작음).

---

## 6. 라운딩(Radius) 체계

### 6.1 Primitive Radius

0, 2, 4, 6, 8, 10, 12, 14, 16, 20, 24, full(9999)

### 6.2 Semantic Radius

값은 토큰 정의가 단일 진실원. 단계: `XS · SM` (XS = 더 작음).

---

## 7. 아이콘 체계

모든 아이콘형 에셋 — 기능 글리프, 상품 속성 마크, 브랜드·소셜 아이덴티티, 비틴터블 기능 일러스트 — 은 **단일 SemanticIcon 네임스페이스** 와 **단일 Icon 컴포넌트** 로 통합된다. 이전에 존재하던 **Badge · Logo · SemanticBadge · SemanticLogo 는 모두 제거**되었고 SemanticIcon 의 카테고리(Product · Brand · Social · Functional) 로 흡수됐다.

### 7.1 계층 구조

```
PrimitiveIcon  →  SemanticIcon  →  Icon 에서 사용
(assetId 참조)    (의미 부여)
      │
      ▼
Assets/manifest.json  →  Assets/source/...
(assetId -> file)
```

PrimitiveIcon 은 실제 파일 경로를 직접 갖지 않고 `asset://icon/...` 형태의 `assetId` 를 참조한다. 실제 파일 위치는 `Assets/source` 파일 구조에서 자동 생성되는 `Assets/manifest.json` 이 관리한다.

### 7.2 Semantic Icon 카테고리

| 카테고리         | 용도                                            | 항목 예시                                                                        |
| ---------------- | ----------------------------------------------- | -------------------------------------------------------------------------------- |
| **Action**       | 사용자 동작                                     | search, filter, close, share                                                     |
| **Navigation**   | 방향 · 이동                                     | chevronLeft, chevronRight, chevronDown                                           |
| **Commerce**     | 커머스 도메인                                   | cart, heart, product                                                             |
| **Notification** | 알림                                            | bell                                                                             |
| **Status**       | 상태 표시                                       | info, warning, error, caution                                                    |
| **Product**      | 상품 인터랙션 · 속성                            | wishOff, wishOn, view, comment, care, edition1, edition1WithBackground, timeSale |
| **Brand**        | 자사 브랜드 아이덴티티                          | bunjang, edition1, edition1WithBackground                                        |
| **Social**       | 소셜 · 외부 서비스                              | apple, band, facebook, kakao, line, naver, naverCafe, toss, x, zicgoo            |
| **Functional**   | 비틴터블 기능 글리프 (멀티컬러 · 그라디언트 등) | — (필요 시 등록)                                                                 |

카테고리는 **분류 태그일 뿐**, "Brand" / "Social" 은 이전의 "Logo" 개념 잔재가 아니라 단순히 관련 아이콘을 묶는 라벨이다.

### 7.3 Tint 입력 모델

호출자는 **tint 색의 유무** 만 입력한다. 내부 렌더 모드(자산 원본 색 / 템플릿 틴팅) 는 DS 가 매핑하며 호출자 어휘에 노출하지 않는다. tint 입력은 모든 source 에 동일하게 노출된다.

| 입력                             | 내부 매핑               |
| -------------------------------- | ----------------------- |
| tint 입력 없음                   | 자산 원본 색으로 렌더   |
| tint 입력 있음 (`SemanticColor`) | 해당 색으로 템플릿 틴팅 |

자산이 틴팅 가능한 형태인지의 판단은 호출자 책임이며, DS 는 source 종류로 type 강제를 두지 않는다.

카테고리별 **사용 가이드** — `.semantic` 사용 시 참고. 타입 수준 강제 없음, 호출 측이 선택하며 리뷰·문서로 일관성 확보.

| 카테고리                                    | 권장 사용                                                                           |
| ------------------------------------------- | ----------------------------------------------------------------------------------- |
| Action / Navigation / Notification / Status | tint 지정                                                                           |
| Commerce                                    | tint 지정 (대부분) · edition 류 에셋은 tint 미사용                                  |
| Product                                     | 혼합 (wish / view / comment 는 tint 지정, care / edition / timeSale 은 tint 미사용) |
| Brand                                       | tint 미사용                                                                         |
| Social                                      | tint 미사용                                                                         |
| Functional                                  | tint 미사용                                                                         |

### 7.4 Size 토큰 (10 단계)

Icon 은 **하나의 Size 스케일** 을 가진다 — Size 는 **정사각(size × size)** 박스를 지정한다. 단계: `xxxxs · xxxs · xxs · xs · sm · md · lg · xl · xxl · xxxl` (기본 `md`). pt 값은 `SemanticSizing.Icon` 토큰이 단일 진실원.

### 7.5 역할 구분 (통합 후)

| Semantic 타입    | 컴포넌트   | 역할                                                                      |
| ---------------- | ---------- | ------------------------------------------------------------------------- |
| **SemanticIcon** | **Icon** | **모든 아이콘형 에셋 — 기능 · 상품 속성 · 브랜드 · 소셜 · 기능 일러스트** |

Badge / Logo / SemanticBadge / SemanticLogo 는 제거됨. 단일 Icon 이 모든 아이콘 _유형_ 을 담당.

**사용 경계 — 단발 전용**: Icon 은 호출처가 **단독으로 직접 쓰는** 단발 컴포넌트다. 다른 DS 컴포넌트의 내부 합성 재료로는 쓰지 않는다 (§10.4 — 내부 합성 대상에서 제외). 아이콘이 필요한 결합 주체는 Icon 을 합성하는 대신 자기 slot.Kind 어휘로 아이콘을 정의하고 내부 렌더 수단으로 직접 그린다. 이에 따라 Icon 의 source · tint · size 어휘와 `.featureOwned` · `.remote` escape 는 결합 주체로 전파되지 않는다.

### 7.6 등록 절차

새 아이콘 추가의 운영 절차는 [Assets/README.md](../Assets/README.md)를 따른다. 이 명세에서는 의미 매핑 책임만 정의한다.

1. 원본은 `Assets/source/icon/...` 아래에 추가한다.
2. primitive asset token 은 `asset://icon/...` 형태의 `assetId`를 참조한다.
3. Token Studio에서 PrimitiveIcon 을 적절한 SemanticIcon 카테고리에 매핑한다.
4. 컴포넌트 내부 슬롯에 필요하면 Component asset token 이 SemanticIcon 을 참조하게 한다.

플랫폼별 변환 산출물은 이 저장소에 커밋하지 않는다. 각 플랫폼 저장소가 `assetId` 계약과 원본 파일을 기준으로 필요한 형태로 변환한다.

---

## 8. 이미지 체계

### 8.1 계층 구조

```
PrimitiveImage  →  SemanticImage  →  컴포넌트에서 사용
(assetId 참조)     (의미 부여)
      │
      ▼
Assets/manifest.json  →  Assets/source/...
(assetId -> file)
```

PrimitiveImage 역시 실제 파일 경로가 아니라 `asset://image/...` 형태의 `assetId` 를 참조한다. 파일 위치와 확장자는 `Assets/source` 파일 구조에서 자동 생성되는 manifest 에서만 관리한다.

### 8.2 Semantic Image 카테고리

| 카테고리        | 항목             |
| --------------- | ---------------- |
| **Placeholder** | product, default |

### 8.3 등록 절차

새 이미지 추가의 운영 절차는 [Assets/README.md](../Assets/README.md)를 따른다. 이 명세에서는 의미 매핑 책임만 정의한다.

1. 원본은 `Assets/source/image/...` 아래에 추가한다.
2. primitive asset token 은 `asset://image/...` 형태의 `assetId`를 참조한다.
3. Token Studio에서 PrimitiveImage 를 적절한 SemanticImage 카테고리에 매핑한다.
4. 컴포넌트에서 필요하면 Component asset token 이 SemanticImage 를 참조하게 한다.

---

## 10. 컴포넌트 모델

DS 의 모든 컴포넌트는 동일한 구조 모델을 따른다 — **slot tree + fill strategy**. 컴포넌트의 용도나 합성 깊이와 무관하게, 호출자가 어디서 무엇을 결정하고 DS 가 어디서 무엇을 결정하는지를 한 어휘로 기술한다.

### 10.1 slot 의 정의

**slot** — 컴포넌트 내부의 명명된 자리. 각 slot 은 두 질문에 답한다:

1. 그 자리에 무엇이 올 수 있는가 (content type)
2. 그것을 누가 어떻게 결정하는가 (fill strategy)

호출자가 직접 채우는 자리(외부 노출 slot)와 DS 가 내부적으로 채우는 자리(내부 slot) 가 모두 slot 이다.

### 10.2 fill strategy

slot 이 채워지는 방식은 다섯 가지로 분류된다.

| Strategy          | 결정 주체 | 방식                                                                                                                       | 예                                   |
| ----------------- | --------- | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| 호출자 Kind 선택  | 호출자    | slot 이 노출하는 닫힌 _의미_ 어휘에서 선택 (매체는 DS 결정)                                                                | NavigationBar 의 left / right slot |
| 호출자 raw 데이터 | 호출자    | 텍스트·숫자 같은 자유 데이터 전달                                                                                          | Text 의 본문, Badge 의 count       |
| 외부 Kind 종속    | DS        | 컴포넌트 자체의 Kind 가 slot 내용을 결정                                                                                   | NavigationBarItem 의 Body          |
| 상태 종속         | DS        | State · Enable · Selection 토큰이 표현 결정                                                                                | 모든 컴포넌트의 색상·외형 분기       |
| 조건부 존재       | 다른 결정 | Kind / 데이터에 따라 slot 자체의 존재 여부가 결정됨                                                                        | NavigationBarItem 의 Badge         |
| 결합 대상 공개    | 호출자    | 정해진 타입의 결합 대상 컴포넌트를 호출자가 직접 제공 (속성·제어 모두 호출자 손) — _공개 합성_ ([§10.4](#104-결합-캡슐화)) | (도입 예정)                          |

호출자가 결정하는 것은 위 표의 첫 두 strategy 와 마지막 (결합 대상 공개) 이고, 나머지 셋은 DS 가 캡슐화한다.

### 10.3 의미·표현 분리의 경계

slot 은 **의미와 표현의 분리 경계선** 이다.

- **slot 위 (외부 면)** — 호출자가 strategy 에 따라 의미를 결정
- **slot 아래 (내부 면)** — DS 가 토큰·자산·자식 컴포넌트로 표현을 결정

state 토큰 자동 적용, Component Token 의 분기 매핑([§1.4](#14-component-token-의-정체성과-두-형태)), variant·size·shape 분기 표현은 모두 _slot 아래_ 에서 일어난다. 호출자는 slot 위에서만 컴포넌트와 만난다.

### 10.4 결합 캡슐화

컴포넌트 결합에는 **내부 합성** 과 **공개 합성** 두 모드가 있다.

- **내부 합성** — 결합 대상이 결합 주체의 slot 안에서 _구현체로 사용_ 되며, 호출자에게는 완전히 숨음 (접근·속성 제어 모두 불가). 호출자는 결합 주체의 자기 속성 (예: title, content) 으로만 만난다. 결합 대상의 호출자 측 API 는 결합 주체의 API 표면으로 전파되지 _않는다_. 결합 주체는 자기 slot.Kind 어휘를 정의하고, 그 Kind 가 어떤 결합 대상 컴포넌트를 어떻게 호출할지를 내부적으로 결정한다.
- **공개 합성** — 결합 대상이 결합 주체의 한 속성으로 _그 자체_ 노출되며, 호출자가 결합 대상에 직접 접근·속성 제어 가능 (§10.2 의 _결합 대상 공개_ 채움 방식).

결합은 결합 대상이 결합 주체의 _역할에 기여_ 하는 관계이지, 결합 대상의 역할이 결합 주체의 역할이 되는 게 아니다 — 이 점은 두 모드 공통.

본 절의 비전파 단정은 **내부 합성에 한정** 한다.

#### 내부 합성의 따름

- **예외 경로 비전파** — 결합 대상의 예외 경로 (`.featureOwned`, 자유 raw 데이터 주입 등) 가 결합 주체의 속성으로 노출되지 않음. 예외 경로는 결합 대상의 단독 사용 경로에서만 유효.
- **표현 토큰 비전파** — 결합 대상의 색상·크기·렌더링 토큰이 결합 주체의 속성이 되지 않음. 결합 주체의 variant·state 토큰이 결합 대상의 표현을 자동 결정.
- **케이스 종속 데이터 비전파** — 결합 대상의 케이스별 종속 데이터 형식이 결합 주체의 속성이 되지 않음. 결합 주체의 Kind 케이스가 자기 종속 데이터를 별도 정의.

이 원리는 §10.3 의 직접 따름이며 **내부 합성의 모든 슬롯에** 보편적으로 적용된다 — 특정 컴포넌트의 특수 룰이 아니라 내부 합성의 일반 원칙이다.

단, 비전파 원칙(_어떻게_ 합성하는가)과 **어떤 컴포넌트를 내부 합성 대상으로 삼을 수 있는가**(_무엇을_ 합성하는가) 는 별개 문제다. 일부 기본 컴포넌트는 단발 전용으로 지정되어 내부 합성 대상에서 제외된다 — 대표적으로 **Icon 은 단발 전용으로, 다른 컴포넌트의 내부 합성 재료로 쓰지 않는다** (§7 아이콘 체계). 아이콘이 필요한 결합 주체는 Icon 을 합성하는 대신 자기 slot.Kind 어휘로 아이콘을 정의하고 내부 렌더 수단으로 직접 그린다.

#### 슬롯별 모드 선택 기준

각 컴포넌트 명세는 _각 슬롯이 내부 합성인지 공개 합성인지_ 명시한다. 선택 기준:

| 슬롯 성격                                                                 | 모드           |
| ------------------------------------------------------------------------- | -------------- |
| 표현이 DS 토큰 정책으로 강하게 통제되어야 함 (예: 헤더 타이틀 typography) | 내부 합성      |
| 결합 대상이 자기 표현 속성을 호출자에게 _노출 안 함_ (토큰 내부 봉인)     | 공개 합성 가능 |
| 호출자 의도가 주도하는 콘텐츠 자체 (예: 카드·아이템)                      | 공개 합성 자연 |
| 결합 대상의 모든 속성을 결합 주체가 자기 어휘로 재정의해야 의미 있음      | 내부 합성      |

**중간 지대 없음** — 결합 대상의 _일부 속성만 노출_ 하는 형태는 새 모드가 아니라 내부 합성의 한 형태다. 결합 주체가 자기 어휘로 그 일부 속성을 재정의해서 받아들이는 비용이 따라온다.

**공개 합성의 안전 조건** — 공개 합성은 결합 대상의 _내부 봉인 정도_ 가 보장되어야 안전. 결합 대상이 자기 표현 속성을 호출자에게 노출하면, 공개 합성 시 DS 토큰 정책 우회 위험이 있다 (호출자가 그 표현 속성을 임의 조작). 그래서 공개 합성을 채택할 슬롯은 _결합 대상이 자기 토큰을 내부에서 닫고 있는지_ 를 점검 후 결정.

### 10.5 표기 규칙

"slot" 은 본 §10 안에서만 개념어로 등장한다. 컴포넌트 API · Figma variant property · 컴포넌트 문서 표면에는 구체 명칭으로만 등장한다. 어휘는 [§4.4 위치/방향 어휘 정책](#44-문서component-토큰-용어-규약-위치방향-어휘-시스템-정책) 을 따른다.

**위치 기반** (수평 · 수직 축 위치가 의미 있을 때)

| 명칭                     | 적용                                 |
| ------------------------ | ------------------------------------ |
| `LeftItem` / `RightItem` | 콘텐츠 좌·우 보조 항목 자리 (수평축) |
| `TopItem` / `BottomItem` | 콘텐츠 상·하 보조 항목 자리 (수직축) |

**역할 기반** (위치가 무의미하거나 단일일 때)

| 명칭             | 적용                        |
| ---------------- | --------------------------- |
| `Body`           | 컴포넌트의 주 시각 영역     |
| `Label`          | 텍스트 라벨 자리            |
| `Badge`          | 표식·카운터 자리            |
| `Track` / `Knob` | atomic control 의 내부 구성 |

같은 명칭은 컴포넌트를 가로질러 동일 의미를 가진다 — NavigationBar 의 `left` 슬롯은 "콘텐츠 좌측 보조 항목 자리" 이다. 새 명칭이 필요하면 본 §10 갱신을 통해 시스템 차원에서 결정한다 (컴포넌트별 임의 추가 금지).

### 10.6 Item.Kind 어휘

호출자 Kind 선택 strategy 의 slot 은 자기 어휘 `Item.Kind` 를 가진다. **Item.Kind 는 의미의 닫힌 어휘이지, 표현 매체의 어휘가 아니다.** 매체(아이콘 / 도트 / 카운터 / 텍스트 등) 의 결정은 DS 내부 매핑이며 호출자에게 노출되지 않는다.

```
NavigationBarItem.Kind  = .home | .search | .notifications(count: Int) | …
```

호출자는 _의미_ 만 고른다 — 그 의미가 아이콘으로 표현될지, 도트로 표현될지, 텍스트로 표현될지는 DS 내부 결정이다. 같은 `.dropDown` 이 한 컴포넌트에선 chevron 아이콘, 다른 컴포넌트에선 도트로 표현될 자유가 보존된다.

Item.Kind 는 sum type 으로, 의미가 종속 데이터를 동반할 수 있다 (예: `.notifications(count:)`). 새 의미가 필요하면 Kind 케이스 추가로 처리하며, 호출자 코드 변경 없이 새 API 가 자동 열린다.

**`variant` vs `kind` — 컴포넌트 분기 축 어휘 정책**

컴포넌트의 분기 입력은 두 축으로 구분되며 **이름이 의미를 가른다**. 본 정책은 시스템 전 영역 (docs 어휘 · 코드 enum 이름 · Figma component property 이름) 에 **동일하게 적용된다**.

#### 핵심 구분

| 어휘          | 본질                                                                                                                                     | 호출자 관점 질문                                   |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| **`kind`**    | 컴포넌트가 정의된 위에서, *이 컴포넌트의 종류*를 나열하는 분기. 각 kind 는 서로 다른 외형 (모양·구조·요소 구성) 또는 의미 단위를 가진다. | _"이 컴포넌트의 어떤 **종류**를 쓸 것인가?"_       |
| **`variant`** | 컴포넌트 내부 _속성값의 분류_. 같은 외형 안에서의 시각 스타일 / 색 의미 분기.                                                            | _"이 컴포넌트를 어떤 **스타일**로 표시할 것인가?"_ |

쉽게 말해:

- **`kind` 가 다르면 그 컴포넌트가 _다른 일을 하고 있는 것이다_**. 새 kind 추가 = _새 사용 시나리오 또는 새 의미 단위_ 의 등재.
- **`variant` 가 다르면 _같은 일을 다르게 보이게 하는 것이다_**. 새 variant 추가 = _같은 역할에 새 시각 옵션_ 의 등재.

#### 개발 관점 비유

코드 모델로 옮겨 보면 두 어휘의 본질 차이가 또렷해진다:

| 어휘          | 개발 어휘                                           | 본질                                                                                    |
| ------------- | --------------------------------------------------- | --------------------------------------------------------------------------------------- |
| **`kind`**    | _타입 수준 닫힌 enum_ (static enumeration of types) | 컴포넌트가 _어떤 형태로 존재하는지_ 의 닫힌 어휘. 케이스마다 본질이 다른 _별개의 형태_. |
| **`variant`** | _인스턴스의 속성값 분류_ (property classification)  | 컴포넌트가 가진 _속성에 들어갈 수 있는 값들의 분류_. 모든 값은 _같은 형태_ 의 인스턴스. |

호출 시점의 API 형상도 보통 이 본질을 따라간다:

- **`kind` 는 보통 컴포넌트의 첫 구분 (생성 시점 결정)** — 어느 형태를 만들지를 먼저 고른 뒤 나머지 설정.
- **`variant` 는 보통 속성·모디파이어 (생성 후 설정)** — 인스턴스의 한 속성으로 값을 지정.

예:

| 컴포넌트                        | API 패턴                       | 어휘 정합   |
| ------------------------------- | ------------------------------ | ----------- |
| `AdBadge(.overlay)`           | 생성 시점에 kind 결정          | `Kind` ✓    |
| `Badge("AD").variant(.brand)` | 생성 후 체이닝으로 속성값 지정 | `Variant` ✓ |

위 패턴은 _경향_ 이며 강제 규칙은 아니다. 본질 (외형 차이 / 속성값 분류) 이 어휘 선택의 기준이고, API 형상은 자연스럽게 따라온다.

#### 식별 테스트

새 컴포넌트 또는 기존 컴포넌트의 분기 축 설계 시 다음 질문으로 판별:

1. **두 분기값이 시각적 외형 (모양·구조·요소 구성) 가 다른가?**
   - 다르다 → `kind`
   - 같다 (색·강조도·테두리 처리만 다름) → `variant`
2. **두 분기값이 호출자에게 의미적으로 다른 사용 시나리오를 노출하는가?**
   - 다르다 (예: 배치 맥락이 다름, 슬롯 의미가 다름) → `kind`
   - 같다 (같은 사용 시나리오의 시각 변형) → `variant`
3. **새 분기값이 추가될 때 호출자가 새로운 의미를 학습해야 하는가?**
   - 그렇다 → `kind`
   - 아니다 (이미 있는 색/스타일 옵션의 확장) → `variant`

세 질문 중 하나라도 `kind` 로 답이 나오면 `kind`. 모두 `variant` 로 답이면 `variant`.

#### 예시

| 컴포넌트                                | 어휘      | 값                                                                    | 본질                                                                |
| --------------------------------------- | --------- | --------------------------------------------------------------------- | ------------------------------------------------------------------- |
| Badge                                 | `Variant` | `brand` / `positive` / `neutral` / `neutralOutlined` / `neutralSolid` | 모든 값이 같은 라벨 외형 — 색 의미·테두리 처리만 다름 → 시각 스타일 |
| Button                                | `Variant` | `primary` / `secondary` / ...                                         | 같은 버튼 외형 — 강조도·색 처리만 다름 → 시각 스타일                |
| AdBadge                               | `Kind`    | `overlay` / `inline`                                                  | 배경 있음 vs 없음, 배치 맥락 자체가 다름 → 다른 사용 시나리오       |
| Item.Kind ([§10.6](#106-itemkind-어휘)) | `Kind`    | `.search` / `.notifications(count:)` / ...                            | 각 case 가 서로 다른 의미 단위 → 의미 어휘                          |

#### 두 축의 공존

한 컴포넌트가 `kind` 와 `variant` 를 _동시에_ 가질 수 있다. 예: 알림 표시 컴포넌트가 `Kind` (알림 종류: `info` / `warning` / `error` — 의미 분기) 와 `Variant` (시각 스타일: `filled` / `outlined` — 시각 분기) 를 동시 보유. 분기 축이 본질적으로 다르면 두 축으로 분리 표현하며, 한 축으로 뭉개지 않는다.

#### 안티 패턴

- **시각 분기를 `kind` 로 명명** — `Badge.Kind = .brand / .positive` ✗. Badge 의 모든 값이 같은 라벨 외형이므로 `Variant` 가 정합.
- **시나리오 분기를 `variant` 로 명명** — 이전 `AdBadge.Variant = .overlay / .inline` ✗. 배경 유무·배치 맥락이 본질적으로 다른 외형이므로 `Kind` 가 정합 ([§11.19](#1119-adbadge) 정정 사례).
- **두 축을 한 enum 으로 뭉개기** — `Kind = .infoFilled / .infoOutlined / .warningFilled / .warningOutlined` ✗. 의미 (info/warning) 와 시각 (filled/outlined) 의 곱집합을 한 축에 박으면 케이스가 폭증하고 의미 추출이 어려워진다. 두 축으로 분리 (`Kind` × `Variant`) 한다.

#### 적용 의무

**모든 신규 컴포넌트 설계는 본 정책을 따른다**. 기존 컴포넌트 명명에서 정합 위반이 식별되면 본 정책 기준으로 정정한다.

### 10.7 컴포넌트 설계의 환원

새 컴포넌트 설계는 두 질문으로 환원된다:

1. 어떤 slot 들로 구성되는가 (slot tree)
2. 각 slot 의 fill strategy 는 무엇인가

이 두 질문이 답해지면 API 표면, Figma 매핑, 토큰 바인딩이 일관되게 따라온다 — slot ↔ Figma 레이어, Item.Kind ↔ Figma variant property, state 종속 ↔ component state 토큰. 새 컴포넌트가 들어와도 별도의 모델 분류 결정이 없다.

---

## 11. 컴포넌트

### 11.1 분류

컴포넌트는 **용도 카테고리**(문서·코드 폴더와 동일: Buttons / Controls / Display / Feedback / Layout / Navigation / Domain)로 조직된다. 분류상 유지하는 유일한 구분은 **범용 vs 도메인** — 도메인 결합 여부를 가른다(범용은 도메인 무관하게 재사용, 도메인은 특정 도메인에 종속). Leaf · Composite 같은 세부 역할 분류는 두지 않는다 — 각 컴포넌트가 *무엇을 담고 / 배치하고 / 표시하는지* 는 카테고리·컴포넌트 문서와 아래 합성 규칙(레이어 모델 · 본문 슬롯)이 직접 서술한다.

- **Buttons**: `Button` · `TextButton` · `IconButton`
- **Controls**: (현재 비어 있음 — 토글류는 Domain 으로 이동)
- **Display**: `Text` · `Icon` · `Image` · `Badge` · `AdBadge` · `ImageBadge` · `CountBadge` · `IconCount` · `Shade` · `Post`
- **Layout**: `Section` · `SectionGroup` · `Grid` · `Carousel`
- **Navigation**: `Page` · `NavigationBar` · `NavigationBarItem`
- **Domain** (무접두사): `ProductThumbnail` · `ProductCard` · `FavoriteToggle` · `MuteToggle` · `AlarmToggle` · `FollowToggle`

(`Domain` 외 전부 범용. `Feedback` 은 현재 비어 있음.)

**레이어 구조 원칙 (Y축 — 콘텐츠 수직 구성):**

화면 콘텐츠는 책임 레이어 6 단계로 구성된다. 각 레이어는 자기 책임 / 슬롯 / 경계 규칙을 가진다. **이 레이어 모델은 컴포넌트의 *합성 위치*(콘텐츠 수직 구성)를 기술하며, 용도 카테고리(무엇에 쓰나)와 직교한다** — 한 컴포넌트는 한 레이어에 속한다. (§12의 Z축 레이어와도 직교한다.)

| 레이어                      | 책임                                                                                                          | 멤버                                         |
| --------------------------- | ------------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| **Screen**                  | navigation, modal stack, 전역 상태 (DS 외부)                                                                  | —                                            |
| **본문 슬롯 소유 컴포넌트** | 페이지 본체 슬롯 보유, 스크롤·여백 정책 결정                                                                  | Page, (미래) Sheet/Drawer                  |
| **Region (영역)**           | 의미 영역과 그 시퀀스. Header / Content / Footer 슬롯 (Section), 동종 Section 시퀀스 + gap (SectionGroup) | Section, SectionGroup                    |
| **Arrangement (반복 배치)** | 동종 반복 아이템 배치                                                                                         | Grid, Carousel |
| **Item (콘텐츠 단위)**      | 카드 / 버튼 등                                                                                                | ProductCard · Button · IconCount 등                            |
| **Atom (기본 표현)**        | 텍스트 / 아이콘 / 이미지                                                                                      | Text · Icon · Image · Badge 등                                         |

**본문 슬롯 정의:**

본문 슬롯은 페이지 본체 콘텐츠를 담는 슬롯이다. **자식 타입이 strict 하게 제약**된다.

- **자식 = `SectionGroup` 만.** 그 외(단일 Section · Grid/Carousel 직접 · 자유 View) 는 거부.
- **타입으로 강제.** 플랫폼 구현은 marker (`PageBodySlot`) 로 컴파일 타임에 제약 — 호출부가 일부러 우회해야만 어길 수 있음.
- **gap 정책은 자식(`SectionGroup`)이 보유.** 본문 슬롯 owner(Page 등) 는 자식 정책에 무관심.
- **스크롤 정책**은 슬롯 owner 가 결정 (`owner-scrolled` / `parent-scrolled`).
- **좌우 여백 정책**은 슬롯 owner 가 결정.

본문 슬롯을 가진 컴포넌트:

| 컴포넌트      | 본문 슬롯 개수 | 비고                                           |
| ------------- | -------------- | ---------------------------------------------- |
| Page        | 1개            | 페이지 본체                                    |
| (미래) Sheet  | 1개            | 시트 본체. 본문 규칙은 Sheet 정의 시 별도 결정 |
| (미래) Drawer | 1개            | 드로어 본체. 동상                              |

**본문 슬롯의 자식 규칙:**

| 자식                                    | 판단     | 의도                                                                                                                                                                     |
| --------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `SectionGroup`                        | **수용** | Section 시퀀스 + gap 정책. 1-원소 SectionGroup 도 동일 골격 유지                                                                                                         |
| 단일 `Section`                        | **거부** | 항상 `SectionGroup { Section { … } }` 로 감싼다 — 1-원소 SectionGroup. 골격 통일성                                                                                   |
| `Grid` / `Carousel` 직접 | **거부** | Layout 가족(Arrangement)는 항상 Section 안에 위치. 본문 슬롯 직접 호스팅 = 레이어 스킵                                                                                   |
| 단일 Atom / Item                        | **거부** | 의미 영역(Section) 없이 콘텐츠를 직접 띄우지 않는다                                                                                                                      |
| 자유 View                               | **거부** | Splash 류 자유 콘텐츠는 L2 Page 의 영역이 아님 — 적합한 Shell(L1 BootShell / L3 Sheet / L4 Overlay) 으로 이동. [§12](#12-화면-레이어-계층-screen-layer-hierarchy) 참조 |

> **빈 상태 / 오류 상태 / 로딩 상태 등 데이터 분기는 본문 골격이 아니라 `Section` 의 content 안에서 처리한다.** Page → SectionGroup → Section 골격은 상태와 무관하게 동일하게 유지하고, loaded / empty / error / loading 분기는 Section.content 안에서 일어난다 — 이때 사용되는 [EmptyState · ErrorState 등](#11-컴포넌트) 은 Item 레이어 컴포넌트로서 일반 Item 처럼 들어간다. 자유 View 예외 경로는 만들지 않는다.

**Section 중첩 규칙:**

- Section 의 직속 자식으로 또 다른 Section 을 두지 않는다 — **직접 중첩 금지**.

```
✓ 허용: Page > SectionGroup > [Section A, Section B, Section C]
✓ 허용: Page > SectionGroup > [Section A, Section B(content: Carousel(items))]
✗ 거부: Page > Section { … }                             ← 단일 Section 약식 — 항상 SectionGroup 으로 감싼다
✗ 거부: Page > Grid { … }                                 ← Arrangement 직접 호스팅 (레이어 스킵)
✗ 거부: Page > VStack { … }                                 ← 자유 View — 적합한 Shell 으로 이동
✗ 거부: Page > SectionGroup > Section { Section { … } }  ← Section 직접 중첩
```

**구성 예시:**

가장 흔한 페이지 (Section 시퀀스):

```
Page {
    SectionGroup {
        Section(header: …) { … }
        Section { … }
        Section { … }
    }
}
```

본문이 검색 결과 그리드 한 덩어리인 페이지 (Section 헤더가 따로 없어도 SectionGroup + Section 으로 감싼다):

```
Page {
    SectionGroup {
        Section {
            Grid(columns: .two) { … }
        }
    }
}
```

상태 기반 콘텐츠 분기 (loaded / empty / error 모두 동일 골격):

```
Page {
    SectionGroup {
        Section {
            switch viewState {
            case .loaded(let items): Grid(items) { ProductTwoColumn($0) }
            case .empty:             EmptyState(icon: .search, title: "검색 결과가 없어요")
            case .error:             ErrorState(title: "잠시 후 다시 시도해 주세요", retry: { … })
            }
        }
    }
}
```

> Splash · 풀스크린 이미지 뷰어 · 부팅 화면 등 L2 Page 본문 규칙에 맞지 않는 화면은 L2 Page 의 예외가 아니라 **다른 Shell 카테고리**(L1 BootShell / L3 Sheet / L4 Overlay) 의 영역이다. [§12](#12-화면-레이어-계층-screen-layer-hierarchy) 참조.

**기본 원칙:**

- **Page 본문 슬롯 = `SectionGroup` 만 수용 (strict, 타입 강제).** 단일 Section · Arrangement · Atom · 자유 View 는 거부.
- **SectionGroup 이 Section 시퀀스 정책(gap) 을 책임**, **Section 이 의미 영역을 정의**, **Layout 가족이 동종 반복 아이템을 배치**.
- 페이지 상단 UI 변형(타이틀, 검색, 진행률 등)은 **NavigationBar 의 variant**로 흡수한다.
- 페이지 하단 UI 변형(CTA, 탭바, 키보드 액세서리)은 **Page 의 bottom 자유 슬롯**에서 feature 화면이 직접 구성한다. DS 는 공간과 safe-area 경계만 제공하며, 슬롯 내부 컴포넌트는 강제하지 않는다 — 본문 슬롯의 strict 규칙과 비대칭 (의도된 설계).

**명명 규칙:**

- DS 명세의 컴포넌트 이름에는 플랫폼 구현 접두사를 붙이지 않는다.
- 범용 / 도메인 특화 두 갈래로만 구분한다 — `Domain` 카테고리 = 도메인 특화(예: ProductCard), 그 외 전부 범용. (세부 역할 분류 Leaf/Composite 는 두지 않는다.)

**Shell 카테고리 (X축 — 화면 카테고리):**

Shell 가족은 한 종류가 아니다. 화면 카테고리에 따라 서로 다른 본문 규칙을 갖는 **여러 Shell 카테고리**로 갈라진다. L2 Page 가 가장 흔한 경우이지만 전부는 아니다.

| 카테고리         | 책임                                                                           | 본문 규칙                               | 현재 상태              |
| ---------------- | ------------------------------------------------------------------------------ | --------------------------------------- | ---------------------- |
| **L1 BootShell** | 부팅·스플래시·풀스크린 미디어. 상단/하단 chrome 없이 자유 콘텐츠를 띄우는 화면 | 자유 (Page 의 strict 규칙 적용 안 됨) | ✗ 미구현 (placeholder) |
| **L2 Page**    | 일반 페이지. 상단 NavigationBar + strict 본문 슬롯 + 옵션 bottom               | `SectionGroup` (strict, 타입 강제)    | ✓ 구현                 |
| **L3 Sheet**     | BottomSheet · 모달 시트                                                        | Sheet 정의 시 별도 결정                 | ✗ 미구현 (placeholder) |
| **L4 Overlay**   | 풀스크린 오버레이 (이미지 뷰어 등)                                             | Overlay 정의 시 별도 결정               | ✗ 미구현 (placeholder) |

> **카테고리 X축은 §12의 Z축 레이어 모델과 다른 개념이다.** §12는 "한 화면 안에서 깊이 방향으로 무엇이 쌓이는가"를 다루고, 여기서는 "이 화면이 어떤 종류의 화면인가"를 다룬다. 두 축은 직교한다 — 같은 화면이 §12 Z축에선 L1 Page Content 레이어를 차지하면서 동시에 X축에선 L2 Page 카테고리 인스턴스일 수 있다.
>
> 현재는 L2 Page 만 구현되어 있다. Splash · 풀스크린 이미지 뷰어 · 부팅 등 L2 본문 규칙에 맞지 않는 화면은 L2 Page 의 예외 경로가 아니라 L1 BootShell · L3 Sheet · L4 Overlay 의 영역으로 분류한다 — 구현 전이지만 분류는 미리 결정해 두어 L2 본문 규칙이 약화되는 것을 방지한다.

---

### 11.2 Text

텍스트 표시 기본 단위. Typography 토큰과 SemanticColor.FG 참조로만 스타일링.

→ **상세**: [Components/Display/Text.md](./Components/Display/Text.md)

---

### 11.3 Icon

단일 아이콘 렌더링. 단독 사용(SemanticIcon 카탈로그 노출)과 다른 컴포넌트 내부 렌더 인프라의 이중 역할.

→ **상세**: [Components/Display/Icon.md](./Components/Display/Icon.md)

---

### 11.4 Image

이미지 표시 기본 단위. fill/fit contentMode, 단색 placeholder.

→ **상세**: [Components/Display/Image.md](./Components/Display/Image.md)

---

### 11.5 Button

사용자 액션 유도. 텍스트 전용 — 라벨 위에 chrome(배경·radius·padding)을 얹은 형태.

→ **상세**: [Components/Buttons/Button.md](./Components/Buttons/Button.md)

---

### 11.7 Section

화면 콘텐츠 영역 컨테이너. 헤더/푸터 슬롯과 Content 양축 padding 을 소유.

→ **상세**: [Components/Layout/Section.md](./Components/Layout/Section.md)

---

### 11.8 ProductThumbnail

상품 썸네일 base. Image(product placeholder) + Shade + 종횡비(4:5 / 1:1). 오버레이·정보는 갖지 않는다(상위 상품카드가 합성).

→ **상세**: [Components/Domain/ProductThumbnail.md](./Components/Domain/ProductThumbnail.md)

---

### 11.10 Grid

2차원 반복 배치 컨테이너. 아이템 간 간격과 열 수만 담당. 항상 lazy 렌더링.

→ **상세**: [Components/Layout/Grid.md](./Components/Layout/Grid.md)

---

### 11.11 Carousel

동종 반복 아이템의 수평 연속 스크롤 컨테이너. 아이템 간격과 뷰포트 Padding 을 토큰으로 관리.

→ **상세**: [Components/Layout/Carousel.md](./Components/Layout/Carousel.md)

---

### 11.13 ProductCard

상품 정보를 표시하는 도메인 컴포넌트. 열 수에 따라 `TwoColumnProductCard` · `ThreeColumnProductCard` 별개 컴포넌트로 분리된다(통합 variant 아님).

→ **상세**: [Components/Domain/TwoColumnProductCard.md](./Components/Domain/TwoColumnProductCard.md) · [Components/Domain/ThreeColumnProductCard.md](./Components/Domain/ThreeColumnProductCard.md)

---

### 11.14 Page

페이지 레벨 컴포넌트(L2). NavigationBar·본문 슬롯(SectionGroup 강제)·옵션 bottom 슬롯을 조립하고 페이지 수준 정책을 소유.

→ **상세**: [Components/Navigation/Page.md](./Components/Navigation/Page.md)

---

### 11.15 NavigationBar

페이지 상단 고정 바. `left / title / right` 슬롯 배치, 좌우 Padding, 고정 높이, 배경 정책을 담당한다.

→ **상세**: [Components/Navigation/NavigationBar.md](./Components/Navigation/NavigationBar.md)

---

### 11.16 NavigationBarItem

상단 바 슬롯의 원자 컴포넌트. Kind-bound — 노출 의미는 §11.17 Intent 카탈로그의 닫힌 어휘가 결정.

→ **상세**: [Components/Navigation/NavigationBar.md#navigationbaritem](./Components/Navigation/NavigationBar.md#navigationbaritem)

---

### 11.17 NavigationBarItem Intent 카탈로그

NavigationBarItem 에 수렴된 승인된 도메인 의도(back/close/share/cart/notification …) 목록. 토큰 비보유.

→ **상세**: [Components/Navigation/NavigationBar.md#intent-카탈로그](./Components/Navigation/NavigationBar.md#intent-카탈로그)

---

### 11.18 Badge

텍스트(+ 선택 아이콘) 기반 읽기 전용 라벨. variant(색상 의미)·size·shape 로 시각 결정, 아이콘은 옵셔널.

→ **상세**: [Components/Display/Badge.md](./Components/Display/Badge.md)

---

### 11.19 AdBadge

광고·유료 콘텐츠 표시 의무 전용. 'AD'/접근성 '광고' 고정, Badge 와 별개(분기 축·법적 요건 상이).

→ **상세**: [Components/Display/AdBadge.md](./Components/Display/AdBadge.md)

---

### 11.20 SectionGroup

동종 Section 시퀀스를 수직 배치하고 gap 정책을 보유하는 컴포넌트.

→ **상세**: [Components/Layout/SectionGroup.md](./Components/Layout/SectionGroup.md)

---

### 11.23 TextButton

본문 흐름의 탭 가능한 텍스트 액션. appearance(text / rightIcon / underline) × emphasis(색 강도) × size, 컨테이너 chrome 없음. 우측 아이콘은 닫힌 세트(기본 chevronRight).

→ **상세**: [Components/Buttons/TextButton.md](./Components/Buttons/TextButton.md)

---

### 11.24 IconCount

아이콘과 카운트 숫자를 한 줄로 표시하는 읽기 전용 메타 컴포넌트.

→ **상세**: [Components/Display/IconCount.md](./Components/Display/IconCount.md)

---

### 11.25 ImageBadge

커머스 신뢰/상태/혜택을 고정 이미지로 표시하는 배지 (kind-bound, 8 종). 도메인 kind 를 갖지만 전체 공용이라 범용으로 둔다.

→ **상세**: [Components/Display/ImageBadge.md](./Components/Display/ImageBadge.md)

---

### 11.27 IconButton

아이콘 하나로 액션을 유도하는 정사각 아이콘 전용 버튼. variant(neutralOutline / ghost) × size(md / lg / xl), 라벨 없음. 비활성은 표준 disabled.

→ **상세**: [Components/Buttons/IconButton.md](./Components/Buttons/IconButton.md)

---

## 12. 화면 레이어 계층 (Screen Layer Hierarchy)

화면은 Z축(깊이) 방향으로 여러 레이어가 쌓인다. 이 섹션은 각 레이어의 **정의**, **소유자**, **책임 경계**, **레이어 간 상호작용 규칙**을 정의한다.

> **Y축(콘텐츠 수직 배치)와의 구분**
>
> - 11.1의 "레이어 구조 원칙"은 **Y축**(페이지 > 섹션 > 레이아웃 > 아이템의 수직 중첩)을 다룬다.
> - 본 섹션은 **Z축**(깊이 순서: 배경 < 콘텐츠 < 크롬 < 플로팅 < 오버레이 < 모달)을 다룬다.
> - 두 축은 직교한다. 하나의 컴포넌트는 Y축에서의 위치(예: 섹션 내부 아이템)와 Z축에서의 위치(예: L1 콘텐츠 레이어)를 모두 가진다.

### 12.1 레이어 모델

```
위(최상단)
┌─────────────────────────────────────────┐
│ L6  System Overlay                      │  OS 소유 (alert, action sheet)
├─────────────────────────────────────────┤
│ L5  Modal Surface                       │  차단형 오버레이 (Modal, Sheet, BottomSheet)
├─────────────────────────────────────────┤
│ L4  Transient Overlay                   │  비차단형 알림 (Toast, Snackbar, Tooltip)
├─────────────────────────────────────────┤
│ L3  Floating                            │  FAB, 플로팅 액션
├─────────────────────────────────────────┤
│ L2  Page Chrome                         │  NavigationBar (상단) / bottom 자유 슬롯 (하단)
├─────────────────────────────────────────┤
│ L1  Page Content                        │  Page 의 content 슬롯 (섹션 스택 + 스크롤)
├─────────────────────────────────────────┤
│ L0  Page Background                     │  Page 의 background 속성
└─────────────────────────────────────────┘
아래(최하단)
```

### 12.2 각 레이어의 소유자와 책임

| 레이어               | 소유 컴포넌트                                                                  | 책임                        | 범위                              | 현재 상태 |
| -------------------- | ------------------------------------------------------------------------------ | --------------------------- | --------------------------------- | --------- |
| L0 Page Background   | Page                                                                         | 배경색, safe area까지 확장  | 전체 화면                         | ✓ 구현    |
| L1 Page Content      | Page 의 content 슬롯                                                         | 섹션 수직 스택, 스크롤 동작 | NavigationBar 와 bottom 슬롯 사이 | ✓ 구현    |
| L2 Page Chrome       | 상단: NavigationBar (고정) / 하단: Page 의 bottom 자유 슬롯 (feature 소유) | 페이지 상/하단 고정 UI      | 페이지 경계                       | ✓ 구현    |
| L3 Floating          | —                                                                              | 콘텐츠 위에 떠 있는 액션    | 페이지 내부                       | ✗ 미구현  |
| L4 Transient Overlay | —                                                                              | 일시 알림 / 보조 정보       | 페이지 내부                       | ✗ 미구현  |
| L5 Modal Surface     | —                                                                              | 차단형 모달 / Sheet         | 페이지 경계 초과 가능             | ✗ 미구현  |
| L6 System Overlay    | OS                                                                             | alert, action sheet, 키보드 | 시스템 제어                       | N/A       |

### 12.3 레이어 내 구성 요소 (예시)

| 레이어 | 예시 UI                                                                                                               |
| ------ | --------------------------------------------------------------------------------------------------------------------- |
| L0     | 페이지 basement / neutralWeak 배경                                                                                    |
| L1     | Section, Grid, Carousel, ProductCard 등                                               |
| L2     | back / cart 등 Intent 카탈로그에서 선택된 엔트리로 채워진 NavigationBar / 주요 CTA·탭 바 등이 놓이는 bottom 자유 슬롯 |
| L3     | "위로 가기" FAB, 플로팅 CTA, 드래그 핸들                                                                              |
| L4     | "장바구니에 담김" Snackbar, 입력 도움 Tooltip, 상태 Toast                                                             |
| L5     | 필터 BottomSheet, 이미지 뷰어 Modal, 약관 동의 Sheet                                                                  |
| L6     | 시스템 alert · 공유 시트 · 시스템 키보드                                                                                 |

### 12.4 레이어 간 상호작용 규칙

**(1) 상호작용 차단**

| 레이어 | 아래 레이어 차단             | Dimmer/배경                      |
| ------ | ---------------------------- | -------------------------------- |
| L1~L2  | 차단하지 않음                | —                                |
| L3     | 차단하지 않음                | —                                |
| L4     | 차단하지 않음 (Tooltip 제외) | 투명                             |
| L5     | **차단**                     | dimmer 필수 (배경 상호작용 불가) |
| L6     | OS 규칙을 따름               | —                                |

- **L5(Modal) 진입 시:** 아래 레이어의 모든 터치/제스처는 차단된다. dimmer 색상은 별도 `Layer.scrim`(가칭) 토큰으로 관리해야 한다 (현재 미정).
- **L4(Transient) 진입 시:** 아래 레이어의 터치는 그대로 통과한다. 다만 Toast/Snackbar가 L2(bottom 슬롯) 영역을 일시적으로 가리는 것은 허용한다.

**(2) 동일 레이어 내 공존**

| 레이어       | 동시 허용            | 정책                                             |
| ------------ | -------------------- | ------------------------------------------------ |
| L3 Floating  | 일반적으로 1개       | 화면당 FAB은 단일 권장                           |
| L4 Transient | Toast/Snackbar는 1개 | 새 것이 들어오면 이전 것을 **교체** (stack 아님) |
| L4 Transient | Tooltip은 복수 가능  | 앵커별로 최대 1개                                |
| L5 Modal     | 중첩 가능            | 최대 깊이 2 권장 (Modal 위 Modal)                |

**(3) 상위 레이어의 아래 레이어 참조 금지**

- L5(Modal)는 L1(Content)의 상태/토큰을 직접 참조하지 않는다. 데이터는 presenting 측이 주입한다.
- L4(Transient)는 어느 페이지에서 띄우든 동일하게 동작해야 한다 (페이지 독립적).
- L3(Floating)은 스크롤 상태와 협력할 수 있지만(예: 스크롤 시 숨김), 이 협력은 "신호 전달"일 뿐이며 L3이 L1 내부 구조를 안다는 뜻은 아니다.

### 12.5 페이지 전환과 레이어의 생명주기

| 전환          | 교체 단위            | 살아남는 레이어                       |
| ------------- | -------------------- | ------------------------------------- |
| Push / Pop    | L0 ~ L2 묶음         | L5(앱 글로벌 모달), L6                |
| Present Modal | — (L5가 위에 추가됨) | 기존 L0~L4 모두                       |
| Dismiss Modal | L5만 제거            | L0~L4 유지                            |
| Tab 전환      | L0 ~ L2 묶음         | L4(글로벌 Toast), L5(글로벌 모달), L6 |

- **원칙:** 페이지 네비게이션은 **L0~L2만** 교체한다. L3 이상은 페이지 수명과 독립적으로 선언하거나 명시적으로 함께 소멸시킨다.

### 12.6 레이어와 토큰

레이어 개념은 시각 토큰에도 반영된다.

| 레이어 | 현재 토큰                                                                                                     | 장기 필요 토큰                                                                    |
| ------ | ------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| L0     | Layer.basement                                                                                                | Layer.neutralWeak (이미 있음)                                                     |
| L1     | Layer.basement (페이지 배경 위임)                                                                             | 섹션 내부 카드 배경 (확장 여지)                                                   |
| L2     | NavigationBar.Background.basement (상단 고정 바) / bottom 자유 슬롯은 feature 가 소유하므로 DS 토큰 강제 없음 | scroll-dependent 배경 전환 (16.4 참조)                                            |
| L3~L5  | —                                                                                                             | Layer.elevated (카드/시트 표면), Layer.scrim (모달 dimmer), Shadow.\* (고도 토큰) |

- Shadow는 현재 SPEC에 정의되어 있지 않다. L3 이상 컴포넌트를 도입하기 전에 Shadow 토큰 체계를 먼저 정의해야 한다.

### 12.7 컴포넌트 내부 오버레이와 레이어의 관계

일부 컴포넌트는 내부에 자체 z-stack을 가진다. 예:

- `ProductThumbnail` = Image 배경 + 뱃지/찜 버튼 오버레이
- `NavigationBarItem` = Icon + 배지 오버레이

이런 **컴포넌트 내부 오버레이**는 본 섹션의 L0~L6과는 다른 개념이다. 컴포넌트는 자기가 속한 화면 레이어(대부분 L1 또는 L2) 내부에 자체적인 오버레이 스택을 가질 수 있으나, 그 오버레이가 **화면 레이어를 건너뛰어 다른 페이지나 모달 위에 떠오르지는 않는다.**

정리:

- **화면 레이어(L0~L6):** 페이지/화면 간 z-order
- **컴포넌트 내부 오버레이:** 하나의 컴포넌트 내부 z-stack
- 둘은 독립적이며 **컴포넌트 내부 오버레이가 화면 레이어를 승격시키는 것은 금지**된다.

### 12.8 현재 SPEC 연계

- L3~L5 구현 필요성 및 Shadow / `Layer.scrim` 토큰 부재는 16.8("화면 레이어 L3~L5 — 공식 컴포넌트 및 토큰 부재")에 장기 과제로 기록되어 있다.
- L2(Chrome)의 스크롤 종속 배경 전환은 16.4("NavigationBar — 스크롤 종속 배경 미구현")에서 다룬다.
- L0/L2 safe area 경계 정책은 16.6("Top/Bottom Bar — Safe Area 경계 정책 부재")에서 다룬다.

---

## 13. 스크롤 동작 규칙

스크롤은 별도 컴포넌트로 제공하지 않는다. Grid 는 배치만 담당하고 스크롤은 외부에서 조합한다. Carousel 은 정의상 자체 스크롤을 소유한다.

이 섹션은 조합 시 지켜야 할 **동작 규칙**을 정의한다.

### 13.1 Pull-to-Refresh

수직 스크롤 목록에서 콘텐츠를 최신 상태로 갱신할 때 사용한다.

**적용 대상:**

- 서버 데이터를 표시하는 수직 스크롤 목록 (상품 목록, 피드 등)
- 로컬 데이터만 표시하는 화면에는 적용하지 않는다

**적용 제외:**

- 수평 스크롤 (Carousel)
- 페이징 카루셀 내부 그리드

**동작 규칙:**

- 스크롤이 최상단에 있을 때 아래로 당기면 트리거된다
- 갱신 중에는 인디케이터를 표시한다
- 갱신 완료 또는 실패 시 인디케이터를 제거한다
- 갱신 중 추가 pull-to-refresh 요청은 무시한다

### 13.2 Pagination (다음 페이지 요청)

스크롤 끝에 도달하기 전에 다음 페이지를 미리 요청하여 끊김 없는 경험을 제공한다.

**트리거 조건:**

- 마지막에서 N번째 아이템이 화면에 노출될 때 다음 페이지를 요청한다
- N의 기본값은 사용처에서 결정하되, 한 화면에 보이는 아이템 수 이상을 권장한다 (예: 2열 그리드에서 한 화면에 6개 보이면 N >= 6)

**동작 규칙:**

- 이미 요청 중이면 중복 요청하지 않는다
- 다음 페이지가 없으면 (마지막 페이지) 트리거하지 않는다
- 요청 중에는 목록 하단에 로딩 인디케이터를 표시한다
- 실패 시 재시도 수단을 제공한다 (자동 재시도 또는 명시적 버튼)

**적용 방향:**

| 컨테이너 유형                             | Pagination 방향       |
| ----------------------------------------- | --------------------- |
| 수직 스크롤 (Grid) | 하단 도달 시          |
| 수평 스크롤 (Carousel)                  | 우측 끝 도달 시       |

### 13.3 Scroll-to-Top

긴 목록에서 최상단으로 빠르게 복귀하는 수단.

**트리거 조건:**

- 일정 거리 이상 스크롤된 상태에서 활성화된다
- 상단 탭바 영역 탭, 또는 플로팅 버튼으로 트리거한다

**동작 규칙:**

- 애니메이션과 함께 최상단으로 이동한다
- 최상단에 이미 있으면 동작하지 않는다
- 수평 스크롤에는 적용하지 않는다

### 13.4 조합 매트릭스

| 기능            | 수직 목록 (서버 데이터) | 수직 목록 (로컬 데이터) | 수평 연속 | 수평 페이징 |
| --------------- | ----------------------- | ----------------------- | --------- | ----------- |
| Pull-to-Refresh | O                       | X                       | X         | X           |
| Pagination      | O                       | X                       | O (선택)  | O (선택)    |
| Scroll-to-Top   | O                       | O (선택)                | X         | X           |

---

## 14. 컴포넌트 합성 관계

```
Text ── 텍스트
Icon ── 모든 아이콘형 에셋 · 기능 · 상품 속성 · 브랜드 · 소셜 · Functional
Image ── 이미지
AdBadge ── 광고 표시 전용 (텍스트 "AD" 고정)
NavigationBarItem ── Icon | Text + badge (상단 바 슬롯 원자)

Button ── Text + chrome (액션, 텍스트 전용)
TextButton ── Text + chevronRight (인라인 액션, chrome 없음)
Badge ── Text + chrome (텍스트 라벨, 범용, 읽기 전용)


Page ── NavigationBar + Content + bottom 자유 슬롯 (페이지 쉘, L2)
NavigationBar ── 상단 바
NavigationBarItem Intent 카탈로그 (Left: back / close · Right: share / cart / notification …)
    └ NavigationBarItem 에 수렴된 의도 엔트리 목록 (슬롯별 분리)

Section ── 콘텐츠 영역

Grid ── 2차원 배치
Carousel ── 수평 연속

ProductThumbnail ── Image + 커머스 오버레이 (도메인, 상품 썸네일)
ProductCard (Two/Three) ── ProductThumbnail + Text (도메인)
```

---

## 15. 확장 규칙

### 15.1 새로운 토큰 추가 시

1. Primitive 값이 기존에 없으면 Primitive 토큰을 먼저 추가한다.
2. Semantic 토큰을 추가하고, 기존 카테고리 네이밍 체계를 따른다.
3. 컴포넌트에서 사용할 경우 Component Token으로 바인딩한다.

### 15.2 새로운 Semantic Color 추가 시

- BG/FG 쌍을 함께 고려한다 (배경을 추가하면 그 위의 전경도 정의).
- 상태가 필요하면 Default/Pressed/Disabled 세트를 모두 정의한다.
- Light/Dark 쌍을 반드시 함께 지정한다.

### 15.3 새로운 컴포넌트 추가 시

- 용도 카테고리(Buttons / Controls / Display / Feedback / Layout / Navigation / Domain)와 범용/도메인을 정한다. (세부 역할 분류 Leaf/Composite 는 두지 않는다.)
- 명세 이름에는 플랫폼 접두사를 쓰지 않는다 (플랫폼 구현의 접두사 규칙은 해당 플랫폼 구현 문서에서 다룬다).
- Variant가 있으면: 각 Variant x State에 대한 Component Token을 정의한다.
- Size가 있으면: XS~XL 스케일 중 필요한 범위를 선택하고, 기존 Semantic 토큰으로 매핑한다.
- 기존 기본 컴포넌트(Text, Icon, Image)를 최대한 재사용한다.
- 레이아웃이 필요하면 기존 Layout 컴포넌트(Grid, Carousel)를 합성한다.

---

## 16. 현재 설계에서 발견된 문제점

### 16.1 ProductCardToken — Primitive 직접 참조 (Semantic 계층 건너뜀)

다수의 Component Token이 Semantic을 거치지 않고 Primitive를 직접 참조하거나, Primitive 연산으로 값을 도출하고 있다.

| 토큰                                   | 현재 참조 방식                            |
| -------------------------------------- | ----------------------------------------- |
| `thumbnail.wish-tap-width`             | PrimitiveSizing.v40 + v2                  |
| `thumbnail.wish-tap-height`            | PrimitiveSizing.v40                       |
| `thumbnail.video-badge-size`           | PrimitiveSizing.v16                       |
| `thumbnail.ad-badge-min-width`         | PrimitiveSizing.v24                       |
| `thumbnail.ad-badge-height`            | PrimitiveSizing.v16                       |
| `thumbnail.status-circle-size`         | PrimitiveSizing.v60                       |
| `one-column.thumbnail-width`           | PrimitiveSizing.v60 + v50                 |
| `one-column.benefit-height`            | PrimitiveSizing.v16                       |
| `three-column.price-block-spacing`     | PrimitiveSpacing.v2                       |
| `three-column.title-top-spacing`       | PrimitiveSpacing/Sizing 연산              |
| `tag.size.*.height`                    | 19pt · 21pt 직접 수치 또는 Primitive 참조 |
| `tag.size.*.padding.horizontal.*`      | Primitive 또는 직접 수치                  |
| `tag.size.*.icon.size`                 | 9 / 10 / 11pt 직접 수치                   |
| `tag.size.*.icon.spacing`              | 2 / 2 / 3pt 직접 수치                     |
| `ad-tag.overlay.min-width` / `.height` | PrimitiveSizing.v24 · v16                 |
| opacity/aspectRatio 전체               | PrimitiveSizing 나눗셈으로 도출           |

[§1.4](#14-component-token-의-정체성과-두-형태) · [§1.5](#15-토큰화-정책-어디까지-토큰으로-둘-것인가) 도입 (2026-05-04) 후 본 항목은 다음 두 트랙으로 정리된다.

**트랙 A — Semantic-bound 정정**: 위 토큰 중 시스템 Scale 의 한 단계로 흡수 가능한 케이스를 식별해 Scale 토큰 참조로 정정한다. 예시 후보:

- `tag.size.*.height` (19 / 21pt) → `Sizing` scale 단계 도입 또는 흡수
- `tag.size.*.icon.spacing` (2 / 2 / 3pt) → `GapX` scale 흡수
- `thumbnail.video-badge-size` (16pt) · `thumbnail.ad-badge-height` (16pt) · `one-column.benefit-height` (16pt) → `Sizing` scale 흡수

**트랙 B — Component-specific 인정**: 시스템 Scale 에 흡수되지 않고 _컴포넌트 정체성 값_ 으로서 의식적 디자인 결정인 케이스는 Component-specific 형태로 명시한다 (§1.4 의 정통 분류이며 위반이 아님). 토큰 레지스트리 비고에 `Component-specific` 표기.

각 토큰별 트랙 분류는 결정 완료. 트랙 B (Component-specific) 로 확정된 항목은 Component Token 표에 `Component-specific` 으로 표기되어 있다. 향후 Sizing scale 또는 토큰화 정책 변경 시 재분류 여지 있음.

### 16.2 하드코딩된 Opacity 잔존

ProductCardToken에서 대부분의 opacity가 토큰화되었으나, 일부 컴포넌트에 하드코딩된 opacity가 남아 있다.

| 위치                                  | 현재 값                                 | 상태     |
| ------------------------------------- | --------------------------------------- | -------- |
| Toggle disabled                     | 0.38                                    | 하드코딩 |
| AdBadge overlay/inline text opacity | ≈ 0.5 (PrimitiveSizing 나눗셈으로 도출) | 하드코딩 |
| AdBadge overlay background opacity  | ≈ 0.5 (동일)                            | 하드코딩 |

이 값들이 디자인 의도를 담고 있다면 토큰화가 필요하다. 특히 disabled opacity(0.38)는 Toggle 외에 다른 컴포넌트에서도 재사용될 수 있으므로 Semantic 수준 토큰화를 검토해야 한다.

### 16.3 수직 간격 토큰의 Semantic 카테고리 불일치 (해결 방향 확정 · 반영 대기)

아이템 간 **수직** 간격을 정의하는 Component Token 여러 개가 Semantic `Padding` 을 참조하고 있다. 이는 원래 Padding(컨테이너 내부 여백)의 의미와 맞지 않으며, Semantic 층에 수직 형제 간격을 위한 카테고리가 없었기 때문에 발생한 차선책이다.

**영향 대상 (마이그레이션 필요):**

| 토큰                    | 의미                    | 현재 참조         | 이관 참조 |
| ----------------------- | ----------------------- | ----------------- | --------- |
| grid.spacing.row.xl     | 그리드 행 간격          | Padding.XL (20pt) | GapY.XXL  |
| list.spacing.item.xl    | 수직 리스트 아이템 간격 | Padding.XL (20pt) | GapY.XXL  |
| page.spacing.section.md | 섹션 간 수직 간격       | Padding.XL (20pt) | GapY.XXL  |

> Section 은 자체 vertical content padding 을 갖지 않으며, header ↔ body / body ↔ footer 수직 간격을 위한 정의된 토큰이 없다 (슬롯은 수직 간격 없이 적층). 따라서 본 GapY 이관 대상이 아니다.

**해결 방향:** §4.6 에 `GapY` Semantic 카테고리를 신설 (Stack 역할, Inline 대응). 기존 `Padding.XL` 을 빌려쓰던 수직 간격 참조는 전부 `GapY.XXL` 으로 이관한다. `Padding` 은 컨테이너 내부 여백 / 외부 들여쓰기 용도로 의미를 좁힌다.

**적용 순서:**

1. Token Studio 에 `GapY` 카테고리 신설 (XXS=4, XS=6, SM=8, MD=10, LG=12, XL=16, XXL=20)
2. Codegen 으로 소비자 Semantic 토큰 재생성
3. 위 Component Token 들의 바인딩을 `GapY.XXL` 로 이관
4. 이관 검증 후 본 항목을 해결로 표시

### 16.4 NavigationBar — 스크롤 종속 배경 미구현

스크롤 위치에 따라 NavigationBar 배경이 변하는(top에서 투명 → 스크롤 시 불투명 + divider) 패턴은 일반적으로 사용된다. 현 NavigationBar 는 단일 고정 배경(basement) 만 제공하며, Page 와의 상태 전달 통로가 없다. 투명·숨김·스크롤 종속 variant 는 현 버전에서 제외된 상태.

**필요한 작업:**

- Page 가 스크롤 offset 을 관찰하여 NavigationBar 에 전달하는 경로
- NavigationBarToken 에 "scroll-activated" 상태 색상 / divider 토큰 추가
- Style enum 또는 Page 통합 레벨에서 `scrollResponsive` variant 도입 검토

### 16.5 NavigationBar — Variant 확장 여지

현재 NavigationBar는 left / title / right 슬롯만 제공한다. 아래 UI 변형을 흡수하기 위한 variant 혹은 slot 확장이 필요할 수 있다.

| 변형                           | 현재 대응 가능 여부                                                 |
| ------------------------------ | ------------------------------------------------------------------- |
| 타이틀 + 부제                  | title slot으로 조립 가능                                            |
| 검색 바 고정                   | title slot에 검색 입력 꽂기 (정상 동작하지만 variant로 공식화 검토) |
| 진행률 표시 (온보딩)           | 미지원 — Variant 또는 별도 slot 필요                                |
| 스크롤 종속 컬러/레이아웃 전환 | 16.4 참조                                                           |

"페이지 상단의 모든 UI 변형은 NavigationBar에서 흡수한다" 원칙을 유지하려면 위 케이스들을 점진적으로 variant로 추가한다.

### 16.6 Top/Bottom Bar — Safe Area 경계 정책 부재

Page가 safe area를 "책임"진다고 명세했으나, 실제 safe area와 배경 확장의 경계 처리 규칙이 구체적이지 않다. 확정이 필요한 포인트:

- NavigationBar 배경이 상단 safe area(상태바)까지 확장되는가
- bottom 자유 슬롯의 내용이 하단 safe area(home indicator)까지 확장되는가 (Page 의 safe-area 경계 정책)
- `ignoresSafeArea` 범위를 배경으로 제한할지 content까지 허용할지

### 16.7 NavigationBarItem — 배지 미세 치수의 Semantic 공백

NavigationBarItem 의 배지(dot / count capsule)는 6pt · 16pt 같은 미세 치수를 필요로 하지만, 현재 Sizing 계열에는 해당 스케일 행이 없다(최소값 Height.xs = 30pt). 이에 따라 해당 치수는 토큰화되지 않은 컴포넌트 내부 상수로 잔존한다.

정리 방향:

- `SemanticSizing`에 미세 스케일(예: v4/v6/v16)을 추가할지, 혹은 배지 전용 토큰(`NavigationBarItemToken.Badge.Dot.size` / `.Count.minSize`)을 만들지 결정
- 선정된 방식으로 로컬 상수를 토큰 참조로 치환

### 16.8 화면 레이어 L3~L5 — 공식 컴포넌트 및 토큰 부재

12장(화면 레이어 계층)에 정의된 L3~L5에 해당하는 컴포넌트와 관련 토큰이 아직 없다.

| 레이어           | 필요한 컴포넌트             | 필요한 토큰                                                                   |
| ---------------- | --------------------------- | ----------------------------------------------------------------------------- |
| L3 Floating      | FAB, 플로팅 액션            | Shadow 스케일, `Layer.elevated` 배경                                          |
| L4 Transient     | Toast / Snackbar / Tooltip  | `Layer.elevated`, `Shadow.*`, presentation 지속시간                           |
| L5 Modal Surface | Modal / Sheet / BottomSheet | `Layer.elevated`, `Layer.scrim` (dimmer), `Shadow.*`, presentation transition |

정리 방향:

- Shadow 토큰 체계를 먼저 정의한다 (현재 SPEC에 Shadow 계층이 없음).
- `SemanticColor.Layer`에 `.elevated` / `.scrim`을 추가하여 L3~L5 표면을 위한 배경 토큰 체계를 갖춘다.
- 각 레이어 컴포넌트는 도입 시 12.4(레이어 간 상호작용 규칙)를 준수해야 한다.

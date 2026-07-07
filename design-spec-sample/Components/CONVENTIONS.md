# 컴포넌트 문서 작성 컨벤션

본 문서는 `Docs/Components/**/*.md` 의 작성 규칙을 정의한다. 새 컴포넌트 문서는 [`_template.md`](./_template.md) 를 복사해 시작한다.

> - [DESIGN_SYSTEM_SPEC.md](../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §1 토큰 계층](../DESIGN_SYSTEM_SPEC.md#1-토큰-계층) — 본 컨벤션의 토큰 표기 근거
> - [SPEC §11 컴포넌트 카탈로그](../DESIGN_SYSTEM_SPEC.md#11-컴포넌트-카탈로그)

---

## 0. 단일 출처 원칙 (SPEC vs 컴포넌트 문서)

- **컴포넌트의 상세 정의(속성·토큰 바인딩·세부 규칙)는 이 컴포넌트 문서가 단일 출처다.**
- **루트 SPEC 은 원칙과 색인(카탈로그)만** 담는다 — SPEC §11.x 는 "역할 한 줄 + → 상세 링크" 스텁이며 상세를 복창하지 않는다.
- 같은 정보를 양쪽에 두지 않는다. 반복은 변경 시 두 곳을 고쳐야 하는 드리프트 부채 — 추가·변경·삭제 어느 경우든 **상세는 컴포넌트 문서 한 곳만** 고친다.
- **토큰 값(치수·색·타이포 수치)은 문서(SPEC·컴포넌트 문서 모두)에 복제하지 않는다.** 값의 단일 진실원은 토큰 소스(Token Studio / 생성 토큰)다. 문서는 *어느 토큰을 참조하는지*(바인딩·토큰 이름)만 가리키고, 구체 값·값 사전·per-token 표는 넣지 않는다 — 토큰 추가·개명·값 변경에 문서를 다시 맞출 필요가 없도록.

## 1. 골격 (H2 순서)

순서는 고정한다. 등장하지 않는 섹션은 통째로 생략한다 (빈 섹션 두지 않음).

| 순서 | 섹션 | 분류 | 등장 조건 |
|----|------|------|----------|
| 1 | 한 줄 요약 (H1 다음 줄) | 필수 | 무조건 |
| 2 | backlink 블록 | 필수 | 무조건 |
| 3 | `## 역할과 범위` | 필수 | 무조건 |
| 4 | `## 다른 컴포넌트와의 구분` | 선택 | 역할이 모호한 짝이 있을 때 |
| 5 | `## Anatomy` | 조건부 | 시각 구조가 있을 때 |
| 6 | `## 속성` | 필수 | 무조건 |
| 7 | `## 행동` | 조건부 | 속성과 무관한 동작이 있을 때 |
| 8 | `## 토큰` | 조건부 | Component Token 을 보유할 때 |
| 9 | `## 불변 규칙` | 조건부 | 호출자가 위반할 수 있는 제약이 있을 때 |
| 10 | `## 확장 가이드` | 선택 | 새 토큰·variant 추가 절차가 정해져 있을 때 |
| 11 | `## 미해결 이슈` | 선택 | 실제 미해결 항목이 있을 때 |

섹션명은 한글 우선, 기술 용어(`Anatomy`, `Variant`, `Size`, `Kind`, `State`, `Enable`, `Selection`, `Shape`, `Layout`) 는 영문 그대로.

### 패밀리 문서 (한 파일에 여러 컴포넌트가 통합된 경우)

NavigationBar (NavigationBar + NavigationBarItem), Product (ProductThumbnail + ProductCard) 처럼 패밀리 결속이 강한 컴포넌트들은 한 파일에 통합된다. 이 경우 골격을 다음과 같이 변형 적용한다:

- H1 + 한 줄 요약 + backlink — 패밀리 차원 (필수).
- `## 개요` (선택) — 패밀리 차원 컴포넌트 관계·핵심 경계.
- `## <컴포넌트 A>` / `## <컴포넌트 B>` ... — 각 컴포넌트가 H2.
  - 그 안의 골격 (역할과 범위 / Anatomy / 속성 / 행동 / 토큰 / 불변 규칙) 은 한 단계 내려 H3 로.
  - 속성 H3 안의 값 사전은 H3 → H4 로 한 단계 down.
- `## <패밀리 공유 메커니즘>` (선택) — Intent 카탈로그처럼 패밀리 공유 부록.
- `## 확장 가이드` / `## 미해결 이슈` — 패밀리 차원 (선택).

**역할과 범위 생략:** 컴포넌트의 역할이 H2 도입 한 줄로 충분히 표현되는 경우 별도 `### 역할과 범위` 를 생략할 수 있다 (Product 의 ProductThumbnail / ProductCard 처럼). 짧은 섹션을 형식적으로 유지하기보다 H2 도입 한 줄에 흡수하는 것이 가독성에 유리할 때 적용.

### 컴포넌트 고유 H2 / H3 허용

위 골격에 없는 H2 또는 H3 라도 컴포넌트의 *핵심 메커니즘* 을 구분해야 할 때 추가할 수 있다.

- H2 예: NavigationBar 의 `## Intent 카탈로그`.
- H3 예: ProductThumbnail 의 `### 오버레이`, ProductCard 의 `### Variant 매트릭스`.

골격의 H2·H3 들과 *순서가 자연스럽도록* 배치한다.

## 2. backlink 블록

```
> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.x ...](../../DESIGN_SYSTEM_SPEC.md#anchor) — 카탈로그 위치
> - [SPEC §<번호> <원칙>](...) — 본 컴포넌트가 참조하는 핵심 원칙
> - [Components/<Category>/<Name>.md](../<Category>/<Name>.md) — 관련/유사 컴포넌트
```

- 항상 `> - [텍스트](경로) — 한줄 설명` 형식. 설명 없는 backlink 금지.
- 순서: 루트 SPEC → §-anchor → 자매 컴포넌트.
- backlink 블록과 본문 사이 `---` 구분선 1개.

## 3. 역할과 범위

컴포넌트가 *무엇을 하고 무엇을 하지 않는가* 를 1~2 문단으로 기술. **범위 밖** bullet 권장.

## 4. 다른 컴포넌트와의 구분 (선택)

역할이 모호한 짝(Tag↔ADTag, Icon↔Image 등) 이 있을 때만. 표 형식 권장:

| 컴포넌트 | 역할 | 사용 시점 |
|---------|------|----------|

본 섹션은 *컴포넌트 선택 단계* 에서 즉시 참조되도록 H1 직후(역할과 범위 다음) 위치한다.

## 5. Anatomy (조건부)

시각 구조 다이어그램. ASCII / SVG / 트리 사용. 플랫폼 코드(SwiftUI 등) 금지.

## 6. 속성

본문은 inventory 표로 시작한다.

**inventory 표 — 4열 고정**

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|

- **항목** — 속성의 식별자. 코드 폰트 (\``backtick`\`).
- **타입 또는 토큰** — 토큰 영역 (`Typography`, `SemanticColor.FG`), DS enum (`Alignment`(명세 정의)), 원시 타입 (`String`, `Int?`), 또는 *결합 대상 컴포넌트 타입* (예: `Text`) — *공개 합성* 슬롯인 경우 ([SPEC §10.4](../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화)).
- **기본값** — 미지정 시 사용 값. 없으면 `—`.
- **비고** — 1줄. 더 길면 표 아래 본문으로 빼고 표는 짧게 유지.

inventory 표 아래에 enum 타입의 *값 사전* 을 H3 로 펼친다.

**값 사전 표 — 3열 고정**

| 값 | 의미 | 비고 |

H3 후보 (해당될 때만 등장):

- `### Variant` — 색(컬러) 표현 축 (역할 + 채움/강조). Shape·Size·State·Kind 와 직교
- `### Size` — 기하 축 (height·padding·icon size 묶음)
- `### Kind` — 프리셋(컴포넌트 종류·용도) 선택 축
- `### State` — 상호작용 상태 축 (default / pressed 등)
- `### Enable` — 활성화 / 비활성화 축 (State 와 직교)
- `### Selection` — 선택 / 미선택 축 (State·Enable 과 직교)
- `### Shape` — 형태(컨테이너 기하) 축 (box / pill)
- `### Layout` — 배치 방식 축 (열 수·방향 등 하위 속성)
- `### <기타 enum>` — 컴포넌트 고유 enum (예: Text 의 `Alignment`, `LineBreakMode`)
- `### 속성 간 상호작용` — 속성들 사이 *조합 효과* (예: `lineLimit × lineBreakMode`)
- `### <슬롯 이름>` — *공개 합성* 슬롯의 결합 대상 컴포넌트가 노출하는 속성·제어 범위 ([SPEC §10.4](../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화))

## 7. 행동 (조건부)

*속성과 무관한* 동작·상호작용 규칙만 둔다. 예: focus 시 키보드 회피, scroll 시 sticky, 자동 dismissal.

속성 *조합 효과* 는 속성 H2 안의 `### 속성 간 상호작용` 으로 둘 것 — 본 섹션과 구분.

## 8. 토큰 (조건부)

**토큰을 표로 열거하지 않는다.** 토큰 name → Semantic 바인딩 → 값의 단일 출처는 토큰 소스(Token Studio / 생성된 `*Token.swift`)다. 문서가 그 목록을 복창하면 토큰 재생성·재바인딩 때마다 문서가 조용히 틀려진다(드리프트).

본 섹션은 **계약 + 포인터** 한두 문장으로 둔다:

> 색·치수·타이포 등 시각 표현은 `<Namespace>.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`<Namespace>Token` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

- *어떤 시각 속성이* 토큰 통제인지, 분기 차원(size/variant/state 등)은 짧게 한 절로 덧붙일 수 있다.
- *결정·행동* (예: "Gap = 0 구조적 고정", 자동 크기 전환 규칙)은 등록부에 없는 결정이라 문서 몫 — 보존하거나 「불변 규칙」에 둔다.
- **금지**: leaf 토큰명·Semantic 바인딩·리터럴 값의 나열, 미소비 토큰 열거(등록부 덤프).

### Component Token 의 정체성

Component Token 은 **호출자의 분기 속성 (`variant` / `size` / `kind` / `state` / `enable` / `selection` / `shape`) 에 따른 값 매핑** 을 표현한다. *분기 차원만* 표현하고, 실제 값은 시스템 Scale 토큰 또는 Semantic 토큰을 참조하는 것이 default 형태 (Semantic-bound).

```
ProductCardToken.TwoColumn.titleToPriceSpacing  →  Spacing.x1
ProductCardToken.ThreeColumn.titleToPriceSpacing  →  Spacing.x05
```

위 두 토큰은 variant 분기를 표현하고, 값은 Spacing scale 의 한 단계를 참조한다.

### Component Token 의 두 형태

- **Semantic-bound** (default) — Semantic Token 또는 시스템 Scale 토큰을 참조한다. 색상·foreground/background/border·spacing·sizing·radius·typography 등 *대부분* 이 본 형태.
- **Component-specific** (예외) — Primitive Token 을 직접 참조한다. 시스템 Scale 에 흡수되지 않고 *그 컴포넌트만의 정체성* 으로서 의식적 디자인 결정인 경우. 손에 꼽을 정도여야 정상.

두 형태는 별개의 토큰 트랙이며, Component-specific 가 단방향 흐름을 *우회* 하는 것이 아니라 *Semantic 단계가 정의되지 않는 토큰* 으로 처리된다.

→ 정의·분류는 [SPEC §1.4](../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태), 토큰화 정책은 [SPEC §1.5](../DESIGN_SYSTEM_SPEC.md#15-토큰화-정책-어디까지-토큰으로-둘-것인가).

## 9. 토큰화 결정 (어디까지 토큰으로 둘 것인가)

원칙:

> **치수 차원** (spacing / sizing / radius / typography / border-width) 에 해당하는 모든 수치는 시스템 Scale 토큰 또는 Component Token 을 참조한다. 코드·문서에서 인라인 매직 넘버 금지.
>
> **추상 단위** (count / ratio / opacity / scale-factor / lineLimit 등) 와 **구조적 0** 은 인라인 허용.

인라인 금지의 목적은 변경 단일점이나 가독성이 아니라 **디자인 자유도 제한** 이다 — 디자이너·개발자가 시스템 Scale 안에서만 값을 선택하도록 강제하는 메커니즘. 그래서 적용 범위는 시스템 Scale 이 정의된 치수 차원에 한정된다. 추상 단위는 Scale 의 절제 대상이 아니므로 인라인 허용.

판단 절차 (치수 차원에 한해):

```
1. 이 값이 호출자의 분기 속성 (variant / size / kind / state / enable / selection / shape) 에 따라 다른가?
   Yes → Component Token 등재 (분기 표현, 값은 시스템 Scale 참조)
   No  → 다음 단계

2. 이 값이 시스템 Scale (Spacing / Sizing / Radius / Typography) 의 한 단계와 일치하는가?
   Yes → 그 Scale 토큰 직접 참조 (Component Token 등재 불필요)
   No  → 다음 단계

3. 이 값이 그 컴포넌트의 *정체성* 으로서 의식적 디자인 결정인가?
   Yes → Component Token (Component-specific) 등재. 또는 Scale 단계를 추가해 1단계로 흡수.
   No  → 디자인을 Scale 안으로 끌어들이거나, 컴포넌트 내부 named constant 로 둠.
```

핵심 통찰:

- **Scale 의 절제가 토큰의 가치를 만든다.** 0~100 의 모든 값을 토큰화하면 토큰은 *이름 붙은 매직 넘버* 가 되어 가치를 잃는다. 시스템 Scale 이 좁고 절제되어 있을 때 토큰화의 의미가 살아난다.
- **Component Token 은 분기 매핑 layer 일 뿐, 값의 정의 layer 가 아니다.** 값의 정의는 항상 시스템 Scale 이 한다.
- **Component-specific 은 예외다.** 한 컴포넌트의 Component-specific 토큰이 많으면 (1) Scale 정의가 불완전하거나 (2) 디자인이 시스템 절제를 따르지 않는 신호로, 시스템 차원의 정정 대상이다.

### Scale 밖 값 처리

디자인 작업 중 시스템 Scale 에 없는 값이 시안에 등장할 수 있다. **default 는 *디자인 조정*** — 가장 가까운 Scale 단계로 수렴한다. 새 토큰 정의·Scale 단계 추가는 다음 세 기준을 모두 통과할 때만 정당화된다.

| 기준 | 질문 | Scale 추가 정당화 |
|------|------|-------------------|
| 재사용성 | 이 값이 다른 컴포넌트에서도 사용되거나 사용될 가능성이 있는가 | Yes 일 때만 |
| 빈도 | 여러 디자인에서 반복 등장하는 패턴인가 | Yes 일 때만 |
| 거리 | 기존 Scale 의 가장 가까운 단계와 2pt 이상 차이 + 의식적 결정인가 | Yes 일 때만 |

세 기준 중 하나라도 통과 못 하면 디자인 조정.

**홀수 단위 (7pt, 11pt, 13pt) 는 거의 항상 조정 대상.** Spacing scale 이 4 또는 8 의 배수 기반이 일반적이라 홀수는 시스템 절제 원칙에 본질적으로 어긋난다. (Typography 의 fontSize 는 예외 — typography scale 에 미리 포함.)

**Scale 단계 추가는 시스템 차원의 결정이지 컴포넌트별 결정이 아니다.** 컴포넌트마다 매번 토론하지 않고, PR · 디자인 리뷰 · 시스템 채널을 통해 한 번에 결정. 추가 후 모든 컴포넌트가 새 단계를 사용한다.

## 10. 불변 규칙 (조건부)

호출자가 위반할 수 없는 제약. Bound 컴포넌트(kind-bound)의 캡슐화 규칙, 슬롯 속성의 형태 제약 등.

bullet list 형식. 각 항목 1~2 문장.

## 11. 확장 가이드 (선택)

새 토큰·variant·size 추가 절차. "검토 → 토큰 추가 → SPEC 카탈로그 갱신 → 본 문서 갱신" 같은 단계를 짧게 명시.

## 12. 미해결 이슈 (선택)

실제 미해결 항목만 둔다. 비어 있으면 본 섹션 삭제. 항상 마지막 H2.

bullet 또는 표 형식. SPEC §16 과 cross-reference.

---

## 어휘 사전

| 용어 | 정의 | 표기 |
|------|------|------|
| Variant | 컴포넌트의 **색(컬러) 표현** 축 — 색상 역할(brand / positive / neutral) + 채움·강조(weak / solid / outline). 형태(Shape)·치수(Size)·상태(State)·종류(Kind)와 직교 — *색이 아닌 차원은 variant 가 아니다* | 영문 `Variant` 고정 |
| Emphasis | 단일 계열 **색 강도** 축 (strong / neutral / mute / subtle). 9-grid `Variant`(역할×채움)와 별개 — 한 색 계열 안의 진하기. 텍스트 계열(예: TextButton)이 쓴다 | 영문 `Emphasis` 고정 |
| Size | 기하 축 (height·padding·icon size 등 수치 묶음) | 영문 `Size` 고정 |
| Kind | 프리셋(컴포넌트 종류·용도) 선택 축 | 영문 `Kind` 고정 |
| State | 상호작용 상태 축 (default / pressed 등). 컴포넌트가 토큰으로 자동 적용 | 영문 `State` 고정 |
| Enable | 활성화 / 비활성화 축. State 와 직교 — 비활성화면 상호작용 차단. 호출자가 제어 | 영문 `Enable` 고정 |
| Selection | 선택 / 미선택 축. State·Enable 과 직교 — 선택된 값 상태, 상호작용으로 토글 | 영문 `Selection` 고정 |
| Shape | 형태(컨테이너 기하) 축 (box / pill). variant 와 직교. 컨테이너 없으면 shape 속성 미노출 | 영문 `Shape` 고정 |
| Layout | 배치 방식 축 (열 수·방향 등 하위 속성 포함) | 영문 `Layout` 고정 |
| 속성 | 컴포넌트가 노출하는 입력 항목들 (영문 *props* 와 동일 개념). 호출자는 호출 시점에 각 속성에 값을 제공한다. | 한글 |
| 행동 | 속성과 무관한 동작·상호작용 규칙 | 한글 |
| 토큰 | 본 컴포넌트가 보유하는 Component Token | 한글 |
| 불변 규칙 | 호출자가 위반할 수 없는 제약 | 한글 |
| Semantic-bound | Semantic Token 을 참조하는 Component Token | 영문 고정 |
| Component-specific | Primitive Token 을 직접 참조하는 Component Token | 영문 고정 |
| 내부 합성 | 결합 대상이 결합 주체 안에 숨고, 호출자에게는 결합 주체의 자기 속성만 보이는 결합 모드 ([SPEC §10.4](../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화)) | 한글 |
| 공개 합성 | 결합 대상이 결합 주체의 속성으로 *그 자체* 노출되어, 호출자가 결합 대상에 직접 접근·제어 가능한 결합 모드 ([SPEC §10.4](../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화)) | 한글 |

## Variant 값 (역할 × 채움·강조 격자)

variant 값 **어휘(naming 카탈로그)** 는 색상 역할 × 채움·강조의 **완전 격자** 다. 값 이름 = `{역할}{채움}` (camelCase). 이 격자는 *어휘 정의* 일 뿐 — **모든 컴포넌트가 9 종을 다 구현하는 것이 아니다.**

| 역할 \ 채움 | Solid | Weak | Outline |
|---|---|---|---|
| **brand** | `brandSolid` | `brandWeak` | `brandOutline` |
| **neutral** | `neutralSolid` | `neutralWeak` | `neutralOutline` |
| **positive** | `positiveSolid` | `positiveWeak` | `positiveOutline` |

- 역할 = `brand` · `neutral` · `positive`, 채움 = `solid` · `weak` · `outline` (둘 다 확장 가능).
- **격자는 완전하게 유지** — 새 역할이나 채움을 추가하면 *나머지 축과의 조합도 함께* 늘린다 (부분 채움 금지). 현재 3 × 3 = **9 종**.
- 각 컴포넌트는 이 어휘에서 **필요한 부분집합만** 골라 자기 `variant` 속성으로 노출한다 (값 이름은 격자 어휘 그대로). 지원 범위는 컴포넌트 문서의 값 표에 명시 — 예: Badge 는 9 종 중 5 종.

## Shape 값

**형태(컨테이너 기하)** 값 어휘 — variant(색)와 직교. 컨테이너 모양은 `box` · `pill` 2 종.

| 값 | 형태 |
|---|---|
| `box` | radius 토큰 적용 사각 |
| `pill` | `height / 2` 완전 타원 (radius 무관) |

- **컨테이너가 없는 컴포넌트는 `shape` 속성을 노출하지 않는다** — "컨테이너 없음"을 `shape = none` 같은 *값*으로 표현하지 않는다 (속성에 shape 이 없으면 형태 개념 자체가 없는 것).
- 각 컴포넌트는 필요한 부분집합만 노출 (예: Badge = `box` · `pill`).

## 속성 이름의 품사

속성 (props) 식별자는 *그것이 표현하는 것의 품사* 를 따른다. 축 레이블 (위 어휘 사전의 `Enable` · `Selection` 등 — 명사, 영문 고정) 과 **호출자가 코드에서 쓰는 속성 이름** 은 구분된다. 후자의 규칙:

| 표현 대상 | 품사 | 예 |
|----------|------|----|
| 이진 상태·조건 (참/거짓 = *WHETHER*) | 형용사 | `enabled`, `selected`, `hidden`, `expanded`, `pressed` |
| 다치 차원 (여러 값 중 하나 = *WHAT*) | 명사 | `variant`, `size`, `padding`, `opacity`, `selection` |
| 동작·이벤트 (지금 수행) | 동사 | `expand()`, `collapse()` |

**판별 기준은 "값을 받느냐" 가 아니다** (Bool 도 값이다). **다치 차원 (WHAT, 명사) ↔ 이진 술어 (WHETHER, 형용사 + Bool)** 로 가른다. 리트머스: "X 의 〈이름〉은 〈값〉이다" 가 자연스러우면 명사 (예: "버튼의 variant 는 brandSolid 다" ⭕ / "버튼의 enabled 는 true 다" ❌ — 후자는 "버튼은 enabled 다" 가 자연스러움 → 형용사).

세부 규칙:

- **형용사 + `is` 접두** — 분사형 어근이 단독으로 명확하면 생략 (`enabled`, `selected`). 어근이 모호할 때만 접두 (`isOn` · `isExpanded` — bare `on` / `expand` 는 명사·동사로도 읽혀 모호).
- **선언형 configurator 는 함수지만 동사가 아니다** — 값-복사 modifier 는 속성·상태 이름을 그대로 쓴다 (`.enabled(_:)`, `.padding(_:)`, `.selected(_:)`). "함수 = 동사" 는 *명령형* (즉시 부수효과) 함수에만 적용된다.
- **Bool 인자를 동사에 붙이면 범주 오류** (`enable(true)`, `expand(false)`) — 형용사 (`enabled` / `expanded`) 또는 명령형 쌍 (`enable()` / `disable()`) 으로 해소.

## 콜아웃·강조 가이드

- **인용 블록 (`>`)** — 설계 경계·불변식 강조 전용. 본문 보강 설명 남발 금지.
- **굵게 (`**...**`)** — 키워드 1개 단위 강조.
- 인용 블록 안에서 굵게 중첩 금지.

## 코드·다이어그램 가이드 (플랫폼 중립)

- **Anatomy / 레이아웃 다이어그램** — ASCII 또는 SVG. 플랫폼 코드(SwiftUI / Compose / React) 금지.
- **호출 예시** — 언어 비특정 의사코드 권장.
- **트리 구조** — 분류·계층 표현에 사용 (Icon 의 SemanticIcon 카테고리 등).
- **실제 구현 코드** — 본 문서에 두지 않음. 구현 저장소(designSystem) 또는 Sample 앱으로 분리.

## 한글·영문 표기

- 본문은 한글 우선. 기술 용어(`Anatomy`, `Variant`, `Size`, `Kind`, `State`, `Enable`, `Selection`, `Shape`, `Layout`, `Semantic-bound`, `Component-specific`) 는 영문 그대로.
- 토큰 이름·코드 식별자는 코드 폰트.
- 약어 정의 없이 사용 금지 — 첫 등장 시 1회 정의.

## 위치/방향 어휘

[SPEC §4.4](../DESIGN_SYSTEM_SPEC.md#44-위치방향-어휘-정책) 에 따라 본 문서들의 어휘는 **물리축** 으로 통일 — `left` / `right` / `top` / `bottom` / `horizontal` / `vertical`. reading-direction 어휘 (`leading` / `trailing`) 는 SwiftUI 매핑 경계 한정이며, 본 문서들에는 등장하지 않는다.

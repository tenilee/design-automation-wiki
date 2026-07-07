# Badge

텍스트(+ 선택 아이콘) 기반 *읽기 전용* 라벨 컴포넌트. **Text 위에 chrome 을 얹고, 라벨 앞에 선택적 아이콘**을 둘 수 있는 컴포넌트. 분류·속성·상태 등 정보를 짧은 문구로 전달하며, DS 가 색·형태 정책을 결정하고 호출 측은 텍스트·아이콘(선택)·variant·size·shape 만 지정한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §1.4 Component Token 의 정체성과 두 형태](../../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태) — Size · Variant 매트릭스의 토큰 형태 근거
> - [SPEC §16.1 ProductCardToken — Primitive 직접 참조](../../DESIGN_SYSTEM_SPEC.md#161-productcardtoken--primitive-직접-참조-semantic-계층-건너뜀) — 일부 Size 치수의 Component-specific 분류 근거
> - [Components/Display/AdBadge.md](../Display/AdBadge.md) — 법적 의무 표시용 전용 컴포넌트

---

## 명명 근거

`Badge` 가족을 **아이콘 옵셔널 단일 컴포넌트**로 통합한다.

**왜 아이콘을 속성으로 흡수하나.** 이전에는 content-axis (text / icon / 둘 다) 를 *별도 컴포넌트* (`Badge` · `IconBadge` · `IconTextBadge`) 로 나눴다. 그러나 같은 라벨에 *아이콘 유무만 다른* 변형이 **컴포넌트 선택 문제**가 되어 명명 부담을 키웠고, content 모양과 variant 색 의미가 컴포넌트·속성 양쪽에 함께 작용해 어휘가 흐려졌다. 아이콘을 *선택 속성* 으로 흡수하면 — content 차이는 속성, 색 의미는 variant — 하나의 `Badge` 로 충분하다.

- **아이콘 유무 = 속성 (`icon`, 옵셔널)**, 컴포넌트 선택 아님.
- *variant · size · shape* 는 그대로 속성으로 격리. variant 가 컴포넌트 선택에 개입하지 않음.
- 별개로 남는 컴포넌트: **`AdBadge`** (법적 의무 표시, 고정 문구) · **`ImageBadge`** (이미지 기반 도메인 배지) — content 성격이 근본적으로 달라 분리 유지.

**Tag ↔ Badge 경계 정의 불가.** 산업 어휘 (Material · Apple HIG · Bootstrap) 사이에서 Tag 와 Badge 의 경계가 본질적으로 모호함. 두 이름을 DS 에 동시 두면 새 라벨마다 경계 논쟁이 재발하므로 `Badge` 하나로 통합한다. `Tag` 가 아닌 `Badge` 채택 이유: Figma 측 컴포넌트와 `BadgeToken.*` namespace 가 이미 Badge 정렬.

---

## 역할과 범위

임의 문구를 DS 시각 언어 (pill / box + variant 색) 로 표시하고, **라벨 앞에 선택적 아이콘**을 둘 수 있다. 아이콘은 자기 `IconSource` 어휘로 정의되어 내부에서 렌더된다 (공개 `Icon` 을 합성하지 않음 — [SPEC §10.4](../../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화)). 색 의미는 variant 체계로 표현한다.

**범위 밖:**

- 상호작용 — 탭·선택·삭제가 필요하면 별도 컴포넌트 (Chip, 도입 예정) 사용.
- 컨테이너 없는 순수 아이콘 렌더 — Icon 사용.
- 법적 의무 표시 — AD 등은 AdBadge.
- 이미지 기반 배지 (번개케어·공식·pro 등) — ImageBadge.

## 다른 컴포넌트와의 구분

| | Badge | Icon | AdBadge | Chip (도입 예정) |
|--|-------|--------|---------|-------------------|
| 내용 | 텍스트 (+ 선택 아이콘) | 아이콘 | 텍스트 "AD" 고정 | 텍스트 (+ 선택 아이콘) |
| 컨테이너 | pill / box + padding | 없음 | variant 별 (overlay 일 때 pill) | pill / box + padding |
| 색 결정 주체 | DS (variant) | DS (tint) 또는 원본 | DS (전용 토큰) | DS |
| 상호작용 | 없음 | 없음 | 없음 | 있음 (탭·dismiss) |
| 법적·정책 요건 | 없음 | 없음 | 광고 표시 의무 | 없음 |

판별 질문:

- 컨테이너 없이 아이콘만 → **Icon**
- 짧은 텍스트 라벨 → **Badge**
- 텍스트 "AD" → **AdBadge**
- 상호작용 필요 → **Chip (도입 예정)**

**Badge ↔ Chip 경계 요약:** *상호작용 (트리거·상태 전환·dismiss) 이 있으면 Chip, 없으면 Badge.* 아이콘 유무는 둘 다 옵션이라 경계 기준이 아니다 — **오직 상호작용 여부**가 가른다.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `text` | `String` | — | 필수. 1 줄, truncate tail 강제. |
| `icon` | `IconSource` | `nil` (없음) | 선택. 라벨 *앞*에 배치. ↓ 값 사전. |
| `variant` | `Variant` (명세 정의) | `.neutralWeak` | 색상 의미 프리셋 5 종 (역할 + 처리). ↓ 값 사전. |
| `size` | `Size` (명세 정의) | `.sm` | xs / sm / md. ↓ 값 사전. |
| `shape` | `Shape` (명세 정의) | `.box` | box / pill. ↓ 값 사전. |

텍스트는 *1 줄 · 꼬리 잘림 (truncate tail)* 을 강제. 긴 문구는 ellipsis 처리.

### Icon (IconSource)

아이콘은 선택이며, 지정 시 라벨 *앞*에 놓인다. 입력 어휘는 아이콘 보유 컴포넌트 공통 `IconSource` 를 따른다.

| 값 | 의미 | 색 처리 |
|----|------|--------|
| `.semantic(icon:)` | DS Semantic 아이콘 | variant foreground 토큰으로 틴트 |
| `.featureOwned(image:)` | feature 소유 이미지 | 원본 색 유지 |

### Variant

Badge 가 쓰는 값 5 종 (역할 + 처리 큐레이션 프리셋, 실제 쓰는 조합만). 용어 정의는 [CONVENTIONS 어휘 사전](../CONVENTIONS.md#어휘-사전).

| 값 | 배경 | 전경 | 테두리 | 용도 |
|----|------|-----|--------|-----|
| `.brandWeak` | `BG.Brand.weak` | `FG.Brand.contrast` | 없음 | 브랜드 강조 분류 |
| `.positiveWeak` | `BG.Positive.weak` | `FG.Positive.contrast` | 없음 | 긍정 약한 강조 (검수 완료, 배송 완료 등) |
| `.positiveSolid` | `BG.Positive.solid` | `FG.Positive.onSolid` | 없음 | 긍정 강한 강조 |
| `.neutralWeak` | `BG.Neutral.weak` | `FG.Neutral.contrast` | 없음 | 중립 · 범용 분류. 기본값. |
| `.neutralOutline` | `BG.Neutral.surface` | `FG.Neutral.contrast` | `Border.Neutral.subtle` | 배경과 조화되는 약한 구분 |

### Size

3 단계 — 시각 표현은 [토큰](#토큰) 참조.

| 값 |
|----|
| `.xs` |
| `.sm` (기본값) |
| `.md` |

### Shape

| 값 | 동작 |
|----|------|
| `.box` | radius 토큰 적용. 기본값. |
| `.pill` | `height / 2` (완전 타원) — radius 토큰 무관. |

## 토큰

색·치수·타이포 등 시각 표현은 `Badge.*` Component Token 이 결정하며 — Size · Shape · Variant 분기 매핑 ([§1.4](../../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태)) — 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`BadgeToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

## 불변 규칙

- *상호작용 없음* — 탭 불가. 탭·삭제·이동 등이 필요하면 Chip 컴포넌트로 분리 (도입 예정).
- *텍스트 1 줄 · truncate tail* — 여러 줄이 필요하면 Badge 가 아니라 본문 텍스트.
- *아이콘은 선택, 위치 고정* — 지정 시 라벨 앞에만 놓인다. 아이콘 색은 variant 가 결정 (`.featureOwned` 제외).
- *variant 체계 바깥의 색 주입 불가* — "이 Badge 만 연두색" 같은 요구는 variant 확장 제안으로 해결.
- *너비는 intrinsic* — 상위 레이아웃에 의해 압축되지 않는다.

## 확장 가이드

### 새 variant 추가 시

1. 색상 의미가 기존 5 종으로 표현 불가한지 확인. 대개 기존 variant 로 해결됨.
2. 신규 variant 명은 *색상 역할* (예: `warning`, `info`) 또는 *강도* (예: `neutralOutline`) 를 설명.
3. `Badge.Variant.*` 네임스페이스에 foreground / background / (border) 토큰 추가. Semantic 색 참조 유지.
4. 본 문서 Variant 표에 한 줄 등재.

### 새 size 추가 시 (신중)

- Size 확장은 디자인 규약 변경에 가깝다. xs / sm / md 외 요구가 2 건 이상 누적되면 검토.
- 새 Size 는 `height` · `paddingHorizontal` (box, pill) · `paddingVertical` · `typography` 의 토큰 세트로 구성.

## 미해결 이슈

- Pill shape 의 `paddingHorizontal` (xs=5pt, sm=6pt, md=8pt) 이 Semantic 을 거치지 않고 직접 수치로 정의됨 — Component-specific 분류 ([§16.1](../../DESIGN_SYSTEM_SPEC.md#161-productcardtoken--primitive-직접-참조-semantic-계층-건너뜀)).

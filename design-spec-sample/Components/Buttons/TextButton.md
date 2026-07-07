# TextButton

본문 흐름 안에 녹아 있는 *탭 가능 텍스트* 컴포넌트. 컨테이너 chrome(배경·radius·padding)을 가지지 않고 텍스트 자체가 affordance — [Button](./Button.md) 과 chrome 유무로 갈린다. 표현 형태(`appearance`)·색 강도(`emphasis`)·크기(`size`)를 가진다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §1.4 Component Token 의 정체성과 두 형태](../../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태) — 매트릭스의 토큰 형태 근거
> - [Components/Buttons/Button.md](./Button.md) — chrome 을 가진 버튼(자매)
> - [Components/Display/Icon.md](../Display/Icon.md) — 우측 아이콘 어휘

---

## 역할과 범위

본문 흐름 안에서 "탭 가능한 한 줄"을 표현한다. 시각 무게는 의도적으로 최소 — *컨테이너가 비어 있고 텍스트만 흐른다.* 대표 맥락: Section Header 의 더보기/전체보기, 카드 내 보조 진입, footer 링크, 인라인 액션.

**범위 밖:**

- 박스 형태 액션 — [Button](./Button.md).
- 좌측(의미 접두) 아이콘이 필요한 텍스트 액션 — TextButton 의 자리가 아니다(우측 아이콘만).

## Anatomy

```
appearance = text       :  Label
appearance = rightIcon  :  Label ─gap─ rightIcon(닫힌 세트, 기본 chevronRight)
appearance = underline  :  Label
                           ───────  (하단 thin border)
```

- 컨테이너 없음 — 배경·radius·padding 토큰을 가지지 않는다.
- 라벨 1 줄 · truncate tail.
- 좌측 아이콘 슬롯 없음.

---

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `label` | `String` | — | 필수. 1 줄, truncate tail. |
| `appearance` | `Appearance` (명세 정의) | `.text` | 표현 형태. ↓ 값 사전. |
| `emphasis` | `Emphasis` (명세 정의) | `.neutral` | 색 강도. ↓ 값 사전. |
| `size` | `Size` (명세 정의) | `.md` | md / lg. ↓ 값 사전. |
| `weight` | `Weight` (명세 정의) | `.bold` | bold / medium. ↓ 값 사전. |
| `disabled` | `Bool` | `false` | 비활성 여부. `true` 면 상호작용 차단. 색 자동 전환. |

### Appearance (값 사전)

| 값 | 구성 |
|----|------|
| `text` | 라벨만 |
| `rightIcon(_)` | 라벨 + 우측 아이콘. 닫힌 세트 값을 동반 — `.rightIcon(.chevronRight)`. ↓ RightIcon |
| `underline` | 라벨 + 하단 thin 밑줄 |

### Emphasis (값 사전 — 색 강도 축)

색상 *강도 사다리*다. 9-grid `Variant`(역할×채움)와 별개 축 — 단일 계열(neutral) 안에서 진하기만 다르다.

| 값 | 강도 |
|----|------|
| `strong` | 가장 진함 |
| `neutral` | 대비 기본 |
| `mute` | 음소거 |
| `subtle` | 가장 약함 |

### Size (값 사전)

| 값 |
|----|
| `.md` (기본값) |
| `.lg` |

### Weight (값 사전)

| 값 |
|----|
| `.bold` (기본값) |
| `.medium` |

### RightIcon (값 사전 — `appearance == .rightIcon` 전용, 닫힌 세트)

| 값 | 비고 |
|----|------|
| `.chevronRight` | 기본값. 현재 세트 유일. |

> 닫힌 세트 — 호출 측이 임의 아이콘을 주입하지 않는다. 새 우측 아이콘은 `TextButtonToken.Icon.Asset.*` 에 등재하는 단일 절차로만 추가.

---

## 행동

- *액션* — 탭 시 `action` 실행. 상태를 유지하지 않는다(상태 전환은 토글류 컴포넌트가 담당).
- *색 자동* — `emphasis` × 활성/비활성이 라벨·아이콘 색을 토큰으로 결정. 호출 측은 색을 주입하지 않는다.
- *라벨 1 줄* · truncate tail.

---

## 토큰

색·치수·타이포 등 시각 표현은 `TextButton.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다 — `emphasis` × 활성/비활성, `size` × `weight` 분기 매핑. 토큰 이름·바인딩·값은 **토큰 소스(`TextButtonToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

---

## 불변 규칙

- *컨테이너 chrome 추가 금지* — 배경/radius/padding 을 가지면 그것은 TextButton 이 아니라 [Button](./Button.md).
- *좌측 아이콘 없음* — 우측 아이콘만, 그것도 닫힌 세트.
- *우측 아이콘은 닫힌 세트* — 호출 측이 임의 아이콘을 주입하지 않는다.
- *색 자동 적용* — `emphasis` × 상태 색은 토큰이 결정. 호출 측이 색을 주입하지 않는다.
- *라벨 1 줄 · truncate tail.*

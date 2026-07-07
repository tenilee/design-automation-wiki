# Button

사용자 액션 유도 컴포넌트. Text 라벨 위에 chrome 을 얹는 **텍스트 전용** 컴포넌트.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §1.4 Component Token 의 정체성과 두 형태](../../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태) — Size · Variant · State 매트릭스의 토큰 형태 근거
> - [Components/Display/Text.md](../Display/Text.md) — 라벨용 Text

---

## 역할과 범위

사용자 액션을 유도하는 인터랙티브 컴포넌트. **라벨만 가진다** — 아이콘/이미지 슬롯을 노출하지 않는다. 호출 측은 라벨·variant·size·disabled·width 만 지정하고, 시각 표현 (배경·전경·padding·radius) 은 모두 토큰으로 결정된다. State (default / pressed) 는 상호작용에 따라 컴포넌트가 자동 적용한다.

## Anatomy

```
┌ paddingX ┬ Label ┬ paddingX ┐
│          │       │          │
└──────────┴───────┴──────────┘
        height (고정)
```

- 라벨은 항상 1 줄.
- 아이콘/이미지 슬롯 없음 — 시각 강조는 variant 체계로만 표현한다.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `label` | `String` | — | 필수. 1 줄 텍스트. |
| `variant` | `Variant` (명세 정의) | `.brandSolid` | 6 종. ↓ 값 사전. |
| `size` | `Size` (명세 정의) | `.md` | xs / sm / md / lg / xl. ↓ 값 사전. |
| `state` | `State` (명세 정의) | `.default` | default / pressed. 상호작용 상태 (컴포넌트 자동 적용). ↓ 값 사전. |
| `disabled` | `Bool` | `false` | 비활성 여부. `true` 면 상호작용 차단. ↓ Disable. |
| `width` | `Width` (명세 정의) | `.fitContent` | ↓ 값 사전. |

### Variant

| 값 | 시각적 특성 | 용도 |
|----|-----------|------|
| `.brandSolid` | 브랜드 강조 배경 + onSolid 텍스트 | 주요 CTA. 기본값. |
| `.brandWeak` | 브랜드 약한 배경 + Brand 텍스트 | 보조 CTA |
| `.neutralSolid` | 어두운 배경 + onSolid 텍스트 | 범용 |
| `.neutralWeak` | 밝은 배경 + Neutral 텍스트 | 약한 강조 |
| `.neutralOutline` | 외곽선 + Neutral 텍스트 (테두리 구분) | 외곽선 보조 액션 |
| `.positiveWeak` | 긍정 약한 배경 + Positive 텍스트 | 긍정 액션 |

### Size

5 단계 — 시각 표현은 [토큰](#토큰) 참조.

| 값 |
|----|
| `.xs` |
| `.sm` |
| `.md` (기본값) |
| `.lg` |
| `.xl` |

### State

| 값 | 조건 |
|----|------|
| `.default` | 활성 + 비터치 |
| `.pressed` | 활성 + 터치 중 |

### Disable

| 값 | 조건 |
|----|------|
| `false` | 상호작용 가능 (기본값) |
| `true` | 상호작용 차단, 시각적 비활성 |

State / Enable 전환 애니메이션: 0.12 초, ease-out.

### Width

| 값 | 동작 |
|----|------|
| `.fitContent` | 콘텐츠에 맞춤. 기본값. |
| `.fillContainer` | 부모 너비를 채움. |

## 토큰

색·치수·타이포 등 시각 표현은 `Button.*` Component Token 이 결정한다 — Size · Variant · State · Disable 분기 매핑 ([§1.4](../../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태)). 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`ButtonToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

## 불변 규칙

- 라벨은 항상 1 줄.
- height / padding / radius / label font 는 Size 토큰이 묶음으로 결정. 호출 측이 변경 불가.
- 아이콘 / 이미지 슬롯을 추가하지 않는다 — 시각 강조는 variant 만으로 표현한다.
- variant 체계 바깥의 색 주입 불가.

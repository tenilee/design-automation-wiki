# IconButton

아이콘 하나로 액션을 유도하는 **정사각 아이콘 전용** 버튼. 라벨 없이 아이콘만 가진다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §1.4 Component Token 의 정체성과 두 형태](../../DESIGN_SYSTEM_SPEC.md#14-component-token-의-정체성과-두-형태) — Size · Variant 매트릭스의 토큰 형태 근거
> - [Components/Display/Icon.md](../Display/Icon.md) — 표시 아이콘
> - [Components/Buttons/Button.md](./Button.md) — 텍스트 버튼

---

## 역할과 범위

아이콘 1개로 액션을 유도하는 인터랙티브 컴포넌트. **아이콘만 가진다** — 라벨 슬롯이 없다. 호출 측은 아이콘·variant·size·disabled 만 지정하고, 시각 표현 (테두리·색·radius·크기) 은 모두 토큰으로 결정된다.

## Anatomy

```
┌───────────┐
│   Icon    │   정사각 (side = size, 아이콘 중앙 정렬)
└───────────┘
```

- 컨테이너는 정사각 — 너비 = 높이 = size.
- 아이콘 크기는 size 와 무관하게 고정 (Icon.md).

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `icon` | `Kind` (명세 정의) | — | 필수. 토큰 카탈로그에 고정. ↓ 값 사전. |
| `variant` | `Variant` (명세 정의) | `.neutralOutline` | neutralOutline / ghost. ↓ 값 사전. |
| `size` | `Size` (명세 정의) | `.md` | md / lg / xl. ↓ 값 사전. |
| `disabled` | `Bool` | `false` | 비활성 여부. `true` 면 상호작용 차단. 색 자동 전환. |

### Icon (값 사전)

아이콘은 토큰 카탈로그 `IconButtonItem` 에 고정 — 임의 아이콘 주입 불가.

| 값 | alarm · back · cart · close · home · menu · setting · share · star · talk |
|----|----|

### Variant

| 값 | 시각적 특성 | 용도 |
|----|-----------|------|
| `.neutralOutline` | 테두리(Border.Neutral.subtle) + 투명 배경 | 외곽선 보조 액션 |
| `.ghost` | 테두리 없음 + 투명 배경 | 최소 강조, 표면 위 아이콘 액션 |

### Size

정사각 컨테이너 한 변의 길이. 아이콘 크기는 size 무관 고정. 단계: `.md · .lg · .xl`. 컨테이너 pt 값은 토큰 소스(`IconButtonToken`)가 단일 진실원.

### Disable

| 값 | 조건 |
|----|------|
| `false` | 상호작용 가능 (기본값) |
| `true` | 상호작용 차단, 아이콘·테두리 색 비활성으로 자동 전환 |

## 불변 규칙

- *아이콘 전용* — 라벨 슬롯 없음. 아이콘 1개만 표시한다.
- *정사각 고정* — 컨테이너는 size 기준 정사각, 아이콘은 중앙 정렬.
- *색·테두리·치수는 토큰* — `IconButtonToken` 이 결정, 호출 측 주입 없음. ghost 는 테두리 없음.
- *pressed 상태 없음* — 상태는 enabled / disabled 두 가지.

# Text

텍스트 표시 기본 단위. `Typography` · `SemanticColor.FG` 토큰을 받아 한 덩어리의 텍스트를 렌더한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §3.2 Typography (Semantic)](../../DESIGN_SYSTEM_SPEC.md#32-typography-semantic) — 본 컴포넌트의 폰트 토큰
> - [SPEC §3.3 행간(LineHeight) 계산](../../DESIGN_SYSTEM_SPEC.md#33-행간lineheight-계산) — 행간 산출·정렬 방식
> - [SPEC §4.4 위치/방향 어휘 정책](../../DESIGN_SYSTEM_SPEC.md#44-시스템-전-영역-위치방향-어휘-정책) — Alignment 어휘 근거

---

## 역할과 범위

화면에서 한 덩어리의 텍스트를 일관된 typography·color 토큰으로 렌더하는 컴포넌트. 헤더·본문·캡션·라벨 등 *더 큰 컴포넌트로 흡수되지 않는 텍스트 케이스* 를 위해 단독 사용을 허용한다.

**범위 밖:**

- 텍스트 입력 (편집 가능한 필드는 별도 컴포넌트)
- 다중 스타일 텍스트 (한 덩어리는 단일 typography·color)
- 상호작용 (탭·링크) — 필요하면 상위 컴포넌트가 감싼다

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `text` | `String` | — | 필수. 표시할 텍스트 본문. |
| `typography` | `Typography` 토큰 | `Typography.Label4.bold` | 폰트·자간·행간·크기를 묶음으로 결정. 개별 요소 변경 불가. |
| `color` | `SemanticColor.FG` | `FG.Neutral.contrast` | `FG` 네임스페이스 내에서만 선택. |
| `alignment` | `Alignment` (명세 정의) | `.left` | 본 컴포넌트 enum. ↓ 값 사전. |
| `lineLimit` | `Int?` | `nil` (제한 없음) | 명시하지 않으면 `lineBreakMode` 가 default 를 결정. |
| `lineBreakMode` | `LineBreakMode` (명세 정의) | `.wordWrap` | 본 컴포넌트 enum. ↓ 값 사전. |

속성 조합 예 (의사코드):

```
Text(text: "...")
  · typography:    .Label4.bold
  · color:         .FG.Neutral.contrast
  · alignment:     .left
  · lineLimit:     2
  · lineBreakMode: .truncateTail
```

### Alignment

물리축 어휘 (§4.4). reading-direction 어휘 (`leading` / `trailing`) 는 노출하지 않으며, 플랫폼 매핑은 구현 경계 한정.

| 값 | 의미 | 비고 |
|----|------|------|
| `.left` | 왼쪽 정렬 | 기본값 |
| `.center` | 가운데 정렬 | |
| `.right` | 오른쪽 정렬 | |

### LineBreakMode

DS 자체 정의. 각 플랫폼이 정의한 줄바꿈·말줄임 모드 값 묶음의 어휘·집합 차이를 흡수하기 위해 DS 가 직접 정의하며, 특정 플랫폼 타입에 의존하지 않는다.

| 값 | 의미 | 비고 |
|----|------|------|
| `.wordWrap` | 단어 단위 줄바꿈, 자르지 않음 | 기본값 |
| `.characterWrap` | 단어 중간에서도 줄바꿈 | CJK · URL · 코드 케이스 |
| `.clip` | 줄임표 없이 잘림 | |
| `.truncateHead` | 앞부분 줄임표 (`…text`) | |
| `.truncateMiddle` | 가운데 줄임표 (`te…xt`) | 긴 파일명·경로 |
| `.truncateTail` | 끝부분 줄임표 (`text…`) | 가장 흔함 |

### 속성 간 상호작용

`lineLimit` 과 `lineBreakMode` 는 독립적으로 결합된다:

- `lineLimit` — 표시할 최대 줄 수.
- `lineBreakMode` — 줄 수에 도달했을 때의 처리 방식 (wrap / clip / truncate*).

두 속성은 자유롭게 조합된다. 예: `lineLimit = 3` + `.truncateTail` → 3 줄 표시 후 끝 줄임표.

## 불변 규칙

- 폰트·자간·행간·크기는 `Typography` 토큰이 묶음으로 결정한다. 개별 요소만 따로 바꿀 수 없다.
- 색상은 `SemanticColor.FG` 네임스페이스 내에서만 선택된다 — `BG` / `Border` 등 다른 네임스페이스 거부.
- `alignment` · `lineLimit` · `lineBreakMode` 는 본문 *행동* 속성이며 `typography` · `color` 와 직교한다.

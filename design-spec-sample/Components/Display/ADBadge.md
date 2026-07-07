# AdBadge

광고 · 유료 콘텐츠 *표시 의무* 를 수행하는 전용 컴포넌트. 텍스트 "AD" 와 접근성 레이블 "광고" 가 타입에 *고정* 되어 있어, 호출 측에서 문구 · 스타일 · 접근성 레이블을 조작할 수 없다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §16.1 ProductCardToken — Primitive 직접 참조](../../DESIGN_SYSTEM_SPEC.md#161-productcardtoken--primitive-직접-참조-semantic-계층-건너뜀) — 본 컴포넌트 미세 치수의 Component-specific 분류 근거
> - [Components/Display/Badge.md](../Display/Badge.md) — 범용 텍스트 라벨 (분리 근거는 아래 참조)
> - [Components/Domain/TwoColumnProductCard.md](../Domain/TwoColumnProductCard.md) — 상품 카드에서의 사용

---

## 역할과 범위

"이 콘텐츠는 광고입니다" 를 시각·접근성 두 경로로 표시한다.

**범위 밖:**

- 광고 여부 판단 — 호출 측이 결정.
- 광고 연결 액션 — 탭 라우팅은 상위 컨테이너 (예: 광고 상품 카드) 의 책임.
- 썸네일 오버레이 배치 좌표 — Domain 래퍼가 담당.

## 다른 컴포넌트와의 구분

광고 표시는 *법적 · 정책적 의무* 가 있는 disclosure. 일반 분류 라벨 (Badge) 과 다른 요건을 갖는다.

| 요건 | Badge | AdBadge |
|-----|-------|---------|
| 텍스트 자유 | 임의 문구 허용 | "AD" 고정 |
| 분기 축 | `variant` (색상 의미) | `kind` (배치 맥락: overlay / inline) |
| 반투명 배경 | 지원하지 않음 | overlay kind 에서 필수 |
| 접근성 레이블 | 텍스트 그대로 | "광고" 고정 |
| 문구 누락 방지 | 호출 측 책임 | 타입으로 강제 |

Badge 에 흡수 불가한 구체 사유:

1. overlay kind 의 *반투명 배경 + opacity* 조합이 Badge 의 단색 variant 체계로 표현되지 않음.
2. 접근성 레이블을 호출 측이 누락하면 법적 리스크 — 타입이 강제해야 함.
3. 문구 변경 (예: "AD" → "광고") 이 발생할 때 *단일 지점* 에서 대응해야 함. 호출 측이 "AD" 문자열을 들고 있으면 일괄 수정 불가.
4. Badge 의 variant 확장이 AdBadge 를 깨뜨릴 수 있음 — 결합을 끊어 독립 진화.

→ Badge 와 별개 컴포넌트로 두는 것이 설계 원칙상 올바름. (AdBadge 는 단일 Text 의 고정 문구 래퍼로 합성 없이 단독 표시 의무를 수행한다.)

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `kind` | `Kind` (명세 정의) | `.inline` | 배치 맥락 프리셋 2 종. ↓ 값 사전. |

문구 · 색 · 크기 · 접근성 레이블은 입력 불가. 타입이 고정.

### Kind

*배치 맥락 프리셋* 축으로 정의된 2 종. 두 kind 모두 텍스트는 "AD" 로 *동일*. 차이는 *배치 맥락에서의 시각 규약*.

| 값 | 시각 특징 | 사용 맥락 |
|----|---------|---------|
| `.overlay` | 반투명 배경 + 둥근 모서리 · opacity 조합 | 썸네일·미디어 위 오버레이 (ProductThumbnail 등) |
| `.inline` | 배경 없음, 약한 opacity 로 텍스트만 | 섹션 헤더·리스트 인라인 표시. 기본값. |

## 토큰

색·치수·타이포 등 시각 표현은 `AdBadge.*` Component Token 이 결정하며 — Kind 분기 매핑 — 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`AdBadgeToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

## 불변 규칙

- 텍스트 "AD" 고정. 호출 측이 변경 불가.
- 접근성 레이블 "광고" 고정. 변경 불가.
- kind 는 배치 맥락 2 종으로 한정. 새 kind 추가는 법적 · 정책 검토 수반.
- Badge 와 토큰을 공유하지 않음.

## 사용 가이드

### `.overlay` — 썸네일·미디어 오버레이

이미지 위에 겹쳐 표시할 때. 반투명 배경으로 미디어 가독성을 해치지 않으면서 광고 의무 표시 만족.

```
썸네일
  └─ 이미지
  └─ AdBadge(.overlay)              ← top-left 오버레이
```

상품 카드에서는 `adBadge` 입력으로 이 kind 가 자동 배치되므로, 호출 측이 kind 를 직접 선택할 필요 없음 ([Components/Domain/TwoColumnProductCard.md](../Domain/TwoColumnProductCard.md)).

### `.inline` — 인라인 텍스트 마크

리스트나 헤더 옆에 텍스트로 나란히 배치.

```
섹션 헤더
  └─ "추천 상품"  AdBadge(.inline)
```

## 확장 가이드

### 문구·스타일 변경이 필요할 때

호출 측에서 우회하지 말고 *AdBadge 내부의 kind 또는 토큰을 수정* 한다. 예: "AD" → "광고" 전환 시 AdBadge 의 고정 문구만 수정하면 전체 사용처에 일괄 반영.

### 새 kind 추가 시

1. 기존 overlay / inline 으로 표현 불가한 배치 맥락인지 확인.
2. kind 명은 *배치 맥락* (예: `compact`, `emphasis`) 또는 *매체* (예: `video`) 축으로 명명.
3. AdBadgeToken 하위에 전용 토큰 세트 추가.
4. 본 문서 Kind 표에 한 줄 등재 + 사용 가이드 업데이트.

## 미해결 이슈

- 미세 치수 (16pt, 24pt) 는 Semantic 계층을 거치지 않고 Component-specific 으로 분류되어 있다 ([§16.1](../../DESIGN_SYSTEM_SPEC.md#161-productcardtoken--primitive-직접-참조-semantic-계층-건너뜀)). 향후 Sizing scale 확장 시 재분류 여지 있음.

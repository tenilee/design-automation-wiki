# ImageBadge

상품의 신뢰·상태·혜택을 *DS 소유 고정 이미지* 로 표시하는 배지. `kind` 가 표시할 이미지를 결정하며, 호출 측은 이미지·색·치수를 주입하지 않고 kind 만 고른다. 도메인적 kind(번개케어·공식 등)를 갖지만 전체에서 공용으로 쓰여 범용으로 둔다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [Components/Display/Badge.md](../Display/Badge.md) — 텍스트 기반 범용 배지
> - [Components/Display/AdBadge.md](./AdBadge.md) — 법적 의무 표시 배지
> - [Components/Domain/TwoColumnProductCard.md](../Domain/TwoColumnProductCard.md) — 상품 카드 오버레이에서의 사용

---

## 역할과 범위

번개케어·공식·pro·오늘의 딜·비디오 같은 *커머스 신뢰/상태/혜택* 표식을 DS 가 소유한 고정 이미지로 표시한다. 각 kind 가 이미지를 고정하므로 호출 측은 kind 만 선택한다.

**범위 밖:**

- 텍스트 라벨 — 임의 문구는 Badge.
- 법적 의무 표시(AD) — AdBadge.
- 임의 이미지 렌더 — Image.
- 표시 여부·배치 좌표 — 상위 컴포넌트(ProductThumbnail 등)가 결정.

## 다른 컴포넌트와의 구분

| | ImageBadge | Badge | AdBadge | Image |
|--|-----------|-------|---------|-------|
| 내용 | 고정 이미지 (kind) | 텍스트 (+ 선택 아이콘) | "AD" 고정 텍스트 | 임의 이미지 |
| 결정 주체 | DS (kind → 자산) | 호출자(문구) + DS(색) | DS 고정 | 호출자 (source) |
| 입력 | kind 만 | text·variant·size·shape | kind | source 등 |

판별: *DS 소유 신뢰/상태 이미지* → ImageBadge · 텍스트 라벨 → Badge · "AD" → AdBadge · 임의(호출자) 이미지 → Image.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `kind` | `Kind` (명세 정의) | — | 필수. 표시할 배지 8 종. ↓ 값 사전. |

이미지·색·치수는 입력 불가 — kind 가 전부 고정.

### Kind

신뢰/상태/혜택 프리셋. 각 kind = 고정 이미지 자산.

| 값 | 의미 |
|----|------|
| `care` | 번개케어 (안전·보호 서비스) |
| `edition1` | 에디션1 |
| `edition1_fill` | 에디션1 (채움형) |
| `mercari` | 머카리 (파트너) |
| `official` | 공식 스토어/판매자 |
| `pro` | pro 셀러 |
| `today_deal` | 오늘의 딜 |
| `video` | 비디오 (영상 포함) |

## 토큰

색·치수·타이포 등 시각 표현은 `ImageBadge.*` Component Token 이 결정하며, 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`ImageBadgeToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다. — kind 별 고정 자산 분기 매핑.

이미지는 height-primary 로 렌더(높이 고정, 너비는 자산 비율).

## 불변 규칙

- kind 가 이미지·치수를 고정 — 호출 측이 이미지/색/문구를 주입할 수 없다.
- 표시 여부·배치는 상위 컴포넌트(ProductThumbnail 등) 책임.
- kind 가 커머스 의미를 담아도 컴포넌트 자체는 *전체 공용 범용 컴포넌트* — 특정 도메인에 종속되지 않는다.

## 확장 가이드

### 새 kind 추가 시

1. 기존 8 종으로 표현 불가한 *신뢰/상태/혜택 표식* 인지 확인.
2. kind 명은 표식의 *의미* (예: `bestseller`) 로 명명.
3. `ImageBadgeToken.Image.Asset` 에 자산 토큰 추가(자동 생성 파이프라인).
4. 본 문서 Kind 표에 한 줄 등재.

## 미해결 이슈

- 토큰 자산 미완(자동 생성 `ImageBadgeToken`): `video` 자산 미정의 · `today_deal` 이 `pro` 자산에 매핑(오매핑) · `edition1` 이 `SemanticImage` 가 아닌 String 토큰. → 토큰 소스에서 자산 보정 필요(코드는 8 종 골격으로 선반영).

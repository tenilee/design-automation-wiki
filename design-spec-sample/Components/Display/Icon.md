# Icon

DS 의 단일 아이콘 렌더링 컴포넌트. 기능 글리프, 상품 속성 마크, 브랜드·소셜 아이덴티티, 그리고 비틴터블 기능 일러스트까지 *모든 아이콘형 에셋* 을 한 컴포넌트로 표현한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §7 아이콘 체계](../../DESIGN_SYSTEM_SPEC.md#7-아이콘-체계) — Primitive · Semantic · 렌더링 규칙
> - [Components/Display/Badge.md](../Display/Badge.md) — 텍스트 라벨 + 컨테이너
> - [Components/Display/Image.md](./Image.md) — 대형 일반 이미지
> - [Components/Display/AdBadge.md](./AdBadge.md) — 광고 표시 전용 컴포넌트

---

## 역할과 범위

단일 아이콘을 렌더링한다. 그게 전부.

**범위 밖:**

- 상호작용 · 탭 · 상태 전환 — 필요하면 상위 컴포넌트가 감싼다.
- 컨테이너 · 배경 · 패딩 — 필요하면 Badge 또는 Chip 사용.
- 대형 일반 이미지 — Image 사용.
- **다른 컴포넌트의 내부 합성 재료** — Icon 은 단발 전용 컴포넌트다 (↓).

**사용 경계 — 단발 전용:** Icon 은 호출처가 **단독으로 직접 쓸 때만** 사용한다. 다른 DS 컴포넌트의 내부 합성 재료로는 쓰지 않는다 — 결합 주체는 자기 slot.Kind 어휘로 아이콘을 정의하고 내부 렌더 수단으로 직접 그린다 ([SPEC §10.4](../../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화) 내부 합성 대상에서 제외, [SPEC §7](../../DESIGN_SYSTEM_SPEC.md#7-아이콘-체계)). 이에 따라 Icon 의 source · tint · size 어휘와 `.featureOwned` · `.remote` escape 는 결합 주체로 전파되지 않는다.

## 다른 컴포넌트와의 구분

| 필요 | 선택할 컴포넌트 |
|------|----------------|
| 순수 아이콘 렌더 (컨테이너 없음) | **Icon** |
| 텍스트 라벨 + DS 색 컨테이너 (pill / box) | Badge |
| "AD" 광고 표시 | AdBadge |
| 대형 일반 이미지 (썸네일 등) | Image |

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `source` | `IconSource` (명세 정의) | — | 필수. Semantic · FeatureOwned · Remote 3 종. ↓ 값 사전. |
| `tint` | `Tint` (명세 정의) | `.none` | 4 종 큐레이션 enum. 모든 source 에 동일하게 노출. `.none` 이면 자산 원본 색, 그 외 (`.neutral` / `.brand` / `.positive`) 는 DS 가 매핑한 역할 색으로 템플릿 틴팅. ↓ 값 사전. |
| `size` | `Size` (명세 정의) | — | 필수. ↓ 값 사전. |

### Source

세 source 는 *소유자 기준* 으로 명명된다. 각 case 의 이름이 "이 자산을 누가 소유하는가" 를 그대로 말한다.

| 값 | 소유자 | 비고 |
|----|-------|------|
| `.semantic(SemanticIcon)` | **DS** | DS 에 등록된 도메인 무관 어휘. SemanticIcon 카테고리에서 선택. |
| `.featureOwned(Image)` | **Feature** | DS 어휘에 들지 않은 기능 모듈 소유 자산을 Icon 의 렌더링 규율 (Size · 색 토큰 · 정렬) 안에서 표시하기 위한 명시적 예외 경로. **Icon 단독 사용 한정** — 결합 시 결합 주체의 slot.Kind 가 자산을 결정하므로 예외 경로가 노출되지 않는다 ([§10.4 결합 캡슐화](../../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화)). |
| `.remote(URL)` | **Remote** | 원격 자원 참조. |

**Feature Owned 의 정체** — DS 자산 라이브러리는 도메인 무관한 의미군만 보유한다 ([§0.2](../../DESIGN_SYSTEM_SPEC.md#02-이-시스템의-핵심-원칙) 범용 / 도메인 분리). 기능 모듈에서만 의미를 갖는 아이콘 (예: 특정 이벤트 뱃지, 기능 모듈 한정 마크) 은 DS Primitive · Semantic 으로 등재되지 *않는다*. 기능 모듈의 자체 자산으로 보유하고 `.featureOwned(...)` 로 전달한다. DS 가 강제하는 것은 *렌더링 규율 (Size · 색 · 정렬)* 까지이며, *자산의 출처와 관리* 는 기능 모듈의 책임. 여러 기능 모듈이 같은 아이콘을 공유하기 시작하면 DS Semantic Icon 으로 *승격 검토*. **이 예외 경로는 Icon 단독 사용 경로에서만 유효** — 결합 시 결합 주체의 slot.Kind 가 매체·자산을 결정하는 일반 원칙([§10.4](../../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화))의 한 사례.

**디자인 도구 표현 경계** — `.featureOwned` · `.remote` 는 런타임 데이터 (Image · URL) 를 본질로 가지므로 Figma 컴포넌트 모델에는 표현하지 않는다. Figma 측 Icon 은 정적 데이터 (variant · instance) 로 표현 가능한 `.semantic` 만 모델링하며, Code Connect 매핑도 `.semantic` 만 다룬다. 두 예외 경로는 호출자가 요구사항에 따라 코드에서 직접 작성한다.

### Tint

호출자는 **DS 가 큐레이션한 4 종 enum** 중에서만 선택한다. 임의 `SemanticColor` 를 박는 길은 차단 — Figma 측 variant 와도 1:1 정렬.

| 값 | 의미 | DS 매핑 |
|----|------|--------|
| `.none` | 자산 원본 색 (틴팅 없음). 기본값. | — (no tint) |
| `.neutral` | 중립 역할 틴팅 | `FG.Neutral.contrast` |
| `.brand` | 브랜드 역할 틴팅 | `FG.Brand.contrast` |
| `.positive` | 긍정 역할 틴팅 | `FG.Positive.contrast` |

자산이 틴팅 가능한 형태인지 (단색 템플릿 vs 멀티컬러 / 그라디언트) 의 판단은 호출자 책임이며, DS 는 source 종류나 SemanticIcon 카테고리로 type 강제를 두지 않는다.

**카테고리별 사용 가이드** — `.semantic` 사용 시 참고. 타입 수준 강제 없이 리뷰·문서로 일관성 확보.

| 카테고리 | 권장 사용 | 근거 |
|---------|---------|------|
| Action / Navigation / Status / Notification | tint 지정 | DS 의 맥락 색상에 맞춰 틴팅 |
| Commerce | tint 지정 (대부분) | 단, edition 류 에셋은 tint 미사용 |
| Product | 혼합 | wish / view / comment 등은 tint 지정, care / edition 류는 tint 미사용 |
| Brand | tint 미사용 | 브랜드 아이덴티티 보존 |
| Social | tint 미사용 | 플랫폼 색 아이덴티티 보존 |
| Functional | tint 미사용 | 멀티컬러 · 그라디언트 등 |

*"틴팅 불가 자산에 tint 를 지정하면 시각이 어색해진다"* 는 것이 유일한 가이드.

### Size

10 단계 t-shirt scale. Size 는 **정사각(size × size)** 박스를 지정한다. 단계: `xxxxs · xxxs · xxs · xs · sm · md · lg · xl · xxl · xxxl` (기본 `md`).

pt 값은 `SemanticSizing.Icon` 토큰이 단일 진실원 — 문서는 값을 복제하지 않는다.

## SemanticIcon 카테고리

```
SemanticIcon
├── Action         search, filter, close, share, ...
├── Navigation     chevronLeft, chevronRight, chevronDown, ...
├── Commerce       cart, heart, product, ...
├── Notification   bell, ...
├── Status         info, warning, error, caution
├── Product        wishOff, wishOn, view, comment, care,
│                  edition1, edition1WithBackground, timeSale, ...
├── Brand          bunjang, edition1, edition1WithBackground
├── Social         apple, band, facebook, kakao, line, naver,
│                  naverCafe, toss, x, zicgoo
└── Functional     (비틴터블 기능 글리프 — 필요 시 추가)
```

- 카테고리는 *단순 분류 태그*. "Brand" / "Social" 이라는 이름이 있지만 *로고라는 별도 개념의 잔재가 아니라* 브랜드 / 소셜 관련 아이콘을 묶는 라벨.
- **Product** 와 **Brand** 에 `edition1` 처럼 같은 원천 에셋을 가리키는 중복 등록이 있을 수 있음 — 사용 맥락 (상품 속성 vs 브랜드 표식) 에 따라 호출 측이 의도에 맞는 경로를 선택.

## 토큰

색·치수·타이포 등 시각 표현은 `Icon.*` Component Token 이 결정하며 — Size 분기 매핑 — 호출 측은 주입하지 않는다. 토큰 이름·바인딩·값은 **토큰 소스(`IconToken` / Token Studio)가 단일 출처** — 본 문서는 목록을 복창하지 않는다.

## 불변 규칙

- **정사각(size × size) 박스** — Size 는 정사각 영역을 지정하며, 글리프는 그 안에서 비율을 유지한다.
- **항상 비율 유지 + 전체 표시** — 잘림 (clip) · 늘림 (stretch) 없음. contentMode 개념 자체를 두지 않는다 (Image 와의 의도적 차이).
- **상호작용 없음** — 단일 렌더 컴포넌트. 탭이 필요하면 상위 컴포넌트가 래핑.
- **단발 전용** — 다른 컴포넌트의 내부 합성 재료로 쓰지 않는다. 결합 주체는 자체 아이콘 재료를 소유하고 직접 렌더한다 (§10.4 내부 합성 대상 제외).
- 임의 pt 주입 금지 — 모든 크기는 Size 토큰에서 선택.
- **Tint 는 모든 source 에 선택 입력** — 자산 형태(단색 템플릿 / 멀티컬러)에 따른 적절성 판단은 호출자 책임이며 DS 는 type 으로 강제하지 않는다.
- SemanticIcon 네임스페이스 밖의 자산은 `.featureOwned` · `.remote` 로 받는다. DS 는 색 정책을 제안만 하고 강제하지 않는다. `.featureOwned` 는 Icon 단독 사용 한정이며 결합 컴포넌트의 slot 에서는 노출되지 않는다.

## 확장 가이드

### 새 아이콘 추가 시

1. 에셋 (Primitive) 을 등록.
2. SemanticIcon 의 적절한 카테고리에 entry 추가.
3. 호출 측이 `Icon(source: .semantic(.Category.name))` 으로 사용.

### 새 카테고리 추가 시

- 기존 8 카테고리 (Action / Navigation / Commerce / Notification / Status / Product / Brand / Social / Functional) 로 묶이지 않는 아이콘 묶음이 누적되면 신규 카테고리 검토.
- SPEC §7 (아이콘 체계) 와 본 문서의 카테고리 목록 동시 갱신.

### 비틴터블 기능 아이콘

- Functional 카테고리에 등록.
- 호출 측은 tint 미지정 사용 권장 (정책 강제 아님 · 시각 일관성 차원).

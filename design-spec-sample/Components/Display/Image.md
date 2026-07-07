# Image

이미지 표시 기본 단위. 표시 규율 (`contentMode` · `placeholder`) 만 관여하고, 자산의 출처와 관리는 source 의 소유자 (DS / Feature / Remote) 책임.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [SPEC §8 이미지 체계](../../DESIGN_SYSTEM_SPEC.md#8-이미지-체계) — Primitive · Semantic · 등록 절차
> - [Components/Display/Icon.md](./Icon.md) — 단일 아이콘 렌더 (대안 컴포넌트, 동일 source 모델)

---

## 역할과 범위

이미지를 표시하는 컴포넌트. Icon 과 동일한 *소유자 기준* source 모델 (DS / Feature / Remote) 을 사용한다.

**범위 밖:**

- 크기·비율(box) — 호출측 레이아웃이 결정. Image 는 주어진 상자를 `contentMode` 로 채울 뿐이다. 정해진 비율은 그 비율을 가진 상위 컴포넌트(예: ProductThumbnail)가 부여한다.
- 자산 큐레이션 — 각 source 의 소유자 (DS Semantic / Feature 모듈 / Remote URL 발급자) 책임.

## 속성

| 항목 | 타입 또는 토큰 | 기본값 | 비고 |
|------|--------------|--------|------|
| `source` | `ImageSource` (명세 정의) | — | 필수. Semantic · FeatureOwned · Remote 3 종. ↓ 값 사전. |
| `contentMode` | `ContentMode` (명세 정의) | `.fill` | ↓ 값 사전. |
| `placeholder` | `Placeholder` (DS, 큐레이션 set) | `.default` | `remote` source 의 로딩 자리표시. ↓ 값 사전. |

### Source

세 source 는 *소유자 기준* 으로 명명된다 (Icon 과 동일 원칙).

| 값 | 소유자 | 비고 |
|----|-------|------|
| `.semantic(SemanticImage)` | **DS** | DS 에 등록된 SemanticImage 어휘. |
| `.featureOwned(Image)` | **Feature** | DS 어휘에 들지 않은 기능 모듈 소유 자산을 Image 의 표시 규율 (contentMode) 안에서 그리기 위한 명시적 예외 경로. **Image 단독 사용 한정** — 결합 시 결합 주체의 slot.Kind 가 자산을 결정하므로 예외 경로가 노출되지 않는다 ([§10.4 결합 캡슐화](../../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화)). |
| `.remote(URL)` | **Remote** | 원격 자원 참조 (placeholder 동반). |

**Feature Owned 의 정체** — DS 자산 라이브러리는 도메인 무관한 의미군만 보유한다 ([§0.2](../../DESIGN_SYSTEM_SPEC.md#02-이-시스템의-핵심-원칙) 범용 / 도메인 분리). 기능 모듈에서만 의미를 갖는 이미지 (예: 기능 모듈 한정 일러스트, 이벤트 한정 비주얼) 는 DS Primitive · Semantic 으로 등재되지 *않는다*. 기능 모듈의 자체 자산으로 보유하고 `.featureOwned(...)` 로 전달한다. DS 가 강제하는 것은 *표시 규율 (contentMode · 클립)* 까지이며, *자산의 출처와 관리* 는 기능 모듈의 책임. **이 예외 경로는 Image 단독 사용 경로에서만 유효** — 결합 시 결합 주체의 slot.Kind / 데이터가 자산을 결정하는 일반 원칙([§10.4](../../DESIGN_SYSTEM_SPEC.md#104-결합-캡슐화))의 한 사례.

### ContentMode

| 값 | 동작 |
|----|------|
| `.fill` | 비율 유지, 영역 채움 (넘침 자동 잘림). 기본값. |
| `.fit` | 비율 유지, 영역 안에 맞춤. |

### Placeholder (큐레이션 set)

`remote` source 의 로딩 자리표시. **큐레이션된 좁은 set 에서만 선택**하며, 호출 측이 임의 색·이미지를 꽂지 않는다.

그래픽 placeholder (`.product` · `.store`) 는 **`.default` 배경 위에 중앙 아이콘이 올라간 합성**이다. 아이콘은 **한 변이 영역 가로폭 × 비율인 정사각**으로 중앙 정렬된다 — 크기는 고정이 아니라 가로폭에 비례하며(폭이 넓어지면 아이콘도 커진다), 세로 길이와 무관하게 정사각을 유지한다.

| 값 | 용도 |
|----|------|
| `.default` | 평평한 중립 배경. 콘텐츠가 비어 있을 때 조용한 자리표시. **기본값.** |
| `.product` | 상품 placeholder — 중앙 상품 아이콘. ProductThumbnail 등 도메인 컴포지트 기본. |
| `.store` | Store placeholder — 중앙 스토어 아이콘. 도메인 비특화. |
| `.featureOwned(Image)` | Feature 소유 예외 — 기능 모듈 자체 자산 사용. **Image 단독 사용 한정** (결합 시 결합 주체 큐레이션 set 으로 제약). |

## 토큰

Image 는 이미지 자산과 표시 규율(`contentMode`)을 호출 측이 정하며, 토큰이 관여하는 건 `remote` 로딩 placeholder 뿐이다(`Image.Placeholder.*`, 호출 측 주입 없음). 토큰 이름·바인딩·값의 단일 출처는 `ImageToken` / Token Studio.

## 확장 가이드

### Placeholder 큐레이션 set 확장

새 placeholder 는 **Token Studio 에 해당 토큰(배경색 또는 아이콘 색·자산)을 추가하고 `Placeholder` enum 에 case 를 추가**해 등재한다. 호출부가 직접 색·이미지를 꽂는 경로는 열지 않는다. (그래픽형은 `.default` 배경 위 중앙 아이콘으로 자동 합성.)

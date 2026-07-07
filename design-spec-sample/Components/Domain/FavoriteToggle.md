# FavoriteToggle

찜(관심) 이진 상태(on/off) 토글. 도메인 engagement 액션 — 상태는 외부에서 보유하고 토글은 표시·전환만 한다.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [Components/Display/Icon.md](../Display/Icon.md) — 표시 아이콘

---

## 속성

| 항목 | 타입 | 기본값 | 비고 |
|------|------|--------|------|
| `kind` | `Kind` (명세 정의) | `.default` | default / overlay. ↓ 값 사전. |
| `isOn` | 외부 상태 (`Bool` 양방향) | — | 필수. on/off. 토글 탭 시 전환. |

### Kind

| 값 | 구성 |
|----|------|
| `.default` | 원형 컨테이너(배경·테두리) + 하트 아이콘 |
| `.overlay` | 컨테이너 없음 — 이미지 위 하트 아이콘 단독 |

## 불변 규칙

- *상태 외부 보유* — on/off 는 호출 측이 보유(양방향 바인딩), 토글은 표시·전환만.
- *아이콘·색·치수는 토큰* — `FavoriteToggleToken` 이 결정, 호출 측 주입 없음.
- *kind 가 컨테이너 유무 고정* — overlay 는 무컨테이너(이미지 오버레이용).

## 미해결

- `favoriteToggle.icon.asset.*` 가 미해결 토큰(String) — 아이콘 참조 보정 후 토큰 소비로 교체 예정.

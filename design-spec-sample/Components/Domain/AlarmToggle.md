# AlarmToggle

알림 받기 ↔ 해제 이진 상태(on/off) 토글. 도메인 액션. 아이콘 박스 또는 텍스트 칩 두 형태.

> - [DESIGN_SYSTEM_SPEC.md](../../DESIGN_SYSTEM_SPEC.md) — 전체 원칙
> - [SPEC §11.1 분류](../../DESIGN_SYSTEM_SPEC.md#111-분류) — 카탈로그 위치
> - [Components/Display/Icon.md](../Display/Icon.md) — 표시 아이콘

---

## 속성

| 항목 | 타입 | 기본값 | 비고 |
|------|------|--------|------|
| `kind` | `Kind` (명세 정의) | — | icon / text. ↓ 값 사전. |
| `isOn` | 외부 상태 (`Bool` 양방향) | — | 필수. on/off. 토글 탭 시 전환. |

### Kind

| 값 | 구성 |
|----|------|
| `.icon` | 박스 컨테이너(배경·테두리) + 종 아이콘 |
| `.text` | 텍스트 칩 — "알림 받기"(on) / "알림 해제"(off) |

## 불변 규칙

- *상태 외부 보유* — on/off 는 호출 측 보유, 토글은 표시·전환만.
- *색·치수·라벨은 토큰* — `AlarmToggleToken`. 라벨 문자열은 현재 baked(i18n 검토 대상).
- *textOnly 테두리는 on 에만.*

## 미해결

- textOnly 가로 패딩 토큰 미정의 — 현재 상수(10) 연결.

# 컴포넌트가 없을 때 워크플로우

> Screen Brief 작성 중 필요한 컴포넌트가 디자인 시스템에 없을 때의 판단 기준과 처리 방법.

---

## 판단 흐름

```
Screen Brief 작성 중 컴포넌트 없음 발견
          │
          ▼
  다른 화면에서도 쓰일 것 같은가?
          │
    ┌─────┴─────┐
   Yes          No
    │            │
    ▼            ▼
 A. 시스템     B. 커스텀 슬롯
   에 추가       으로 처리
```

---

## A. 디자인 시스템에 추가

**조건**: 2개 이상의 화면에서 반복 사용될 가능성이 있는 경우

### 순서

**1. PM → 디자이너에게 요청**

Screen Brief에 아래 형식으로 누락 컴포넌트를 명시해서 전달.

```yaml
# 누락 컴포넌트 요청
missing_components:
  - name: FixedTab
    description: "화면 상단 고정 탭 바. 탭 목록과 선택 상태 표시."
    used_in: "찜 화면, 마이페이지, 검색 결과 화면"
    reference: "https://www.figma.com/design/QcFMEU0RtSwprkpur9Lo1g?node-id=163-2014"
```

**2. 디자이너 → Figma에 컴포넌트 제작**

- 디자인 시스템 파일에 컴포넌트 추가
- 토큰 맵핑 완료

**3. 디자이너 → Contract 작성**

`Component_Contracts.md`에 추가.

```
## FixedTab

Status: Core
Purpose: ...
Use when: ...
Avoid when: ...
Key props:
- ...
Composition: none
Owner: Platform Design System
```

**4. 디자이너 → Screen_Brief_Convention.md 업데이트**

사용 가능한 컴포넌트 목록에 추가.

```markdown
- `fixedTab` — 화면 상단 고정 탭 바
```

**5. PM → Screen Brief 작성 (또는 업데이트)**

```yaml
sections:
  - content:
      component: fixedTab
      tabs:
        - label: "전체 목록"
          selected: true
        - label: "브랜드"
```

**6. AI → Figma에 화면 생성**

---

## B. 커스텀 슬롯으로 처리

**조건**: 특정 화면에서만 쓰이는 일회성 구조인 경우

### 순서

**1. PM → Screen Brief에 `custom: true`로 작성**

컴포넌트 이름 대신 구조를 설명합니다.

```yaml
sections:
  - content:
      custom: true
      description: "고정 탭 바 — '전체 목록' 단일 탭, 하단 보더 라인"
      tabs:
        - label: "전체 목록"
          selected: true
```

**2. AI → 설명 기반으로 직접 Figma에 그리기**

디자인 시스템 컴포넌트 없이 description을 보고 프레임 직접 구성.

**3. 디자이너 → 결과물 검토 후 판단**

- 다른 화면에서도 필요해지면 → 컴포넌트화 후 A 경로로 전환
- 이 화면에서만 쓰인다면 → 그대로 유지

---

## 판단 기준 요약

| 상황 | 방법 |
|---|---|
| 2개 이상 화면에서 사용 | A. 시스템에 추가 |
| 기존 컴포넌트의 variant만 추가하면 됨 | A. Contract만 업데이트 |
| 이 화면에서만 쓰이는 레이아웃 | B. 커스텀 슬롯 |
| 아직 사용 빈도가 불확실 | B. 커스텀 슬롯으로 먼저 그리고 추후 판단 |

---

## 찜 화면 적용 예시

| 컴포넌트 | 판단 | 이유 |
|---|---|---|
| App Bar | A. 시스템에 추가 | 검색, 홈 등 여러 화면에서 반복 사용 |
| FixedTab | A. 시스템에 추가 | 찜, 마이페이지, 검색 결과 등 반복 사용 |
| Multi-image ProductCard | 사용 빈도 확인 후 결정 | 찜 화면 전용이면 B, 다른 화면에도 나오면 A |

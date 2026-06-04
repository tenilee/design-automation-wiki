# BGZT Platform Design System

BGZT 디자인 시스템 기반으로 Figma 화면을 자동 생성하는 프로젝트.

---

## 워크플로 규칙 (필수)

### 1. 플랜 퍼스트
모든 작업 요청 수신 시:
1. **작업 계획을 텍스트로 먼저 작성해서 사용자에게 보여준다**
2. 사용자 OK 확인 후에만 실제 작업(Figma 조작 포함) 시작
3. OK 없이 `use_figma` 호출 절대 금지

### 2. Platform Design System 필수 참조
- Figma DS: https://www.figma.com/design/wJaqzyxIazITPQ7ULFJY4o/Platform-Design-System
- 컴포넌트 키/구조는 반드시 이 파일에서 확인
- 명령어에 없는 내용을 임의로 추가하거나 이전 화면 내용을 재활용하지 않는다

### 3. MCP Inter 폰트 우선 접근
DS 컴포넌트 텍스트 수정 시 항상 아래 순서 준수:
```js
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });
// textStyleId가 있는 노드는 반드시 해제 후 수정
if (node.textStyleId) node.textStyleId = "";
node.fontName = { family: "Inter", style: "..." };
node.characters = "텍스트";
```

---

## 폴더 구조

```
claude-code/
├── CLAUDE.md                          ← 이 파일
├── docs/                              ← Claude 참조 문서
│   ├── Component_Contracts.md
│   ├── Component_Contract_Convention.md
│   ├── Missing_Component_Workflow.md
│   └── Figma_Design_Build_Guide.md
├── tokens/                            ← 토큰 파일
│   ├── primitive_color.json
│   ├── semantic_theme_light.json
│   ├── semantic_theme_dark.json
│   ├── colorMode/
│   │   ├── light.json
│   │   └── dark.json
│   ├── BGZT_Token_Naming_Convention.md
│   └── Component_Token_Naming_Convention.md
├── assets/                            ← 이미지 등 에셋
│   └── sampleImage1.png
└── archive/                           ← 구버전/더 이상 사용 안 함
    ├── Screen_Brief_Convention.md
    ├── Screen_Brief_Convention_v2.md
    ├── LegacyColor.md
    ├── USER-GUIDE.md
    ├── button.build-spec.html
    └── token_color_legacy.zip
```

---

## 화면 생성 작업 시

### 참고 문서
- **컴포넌트 명세**: `docs/Component_Contracts.md` — 사용 가능한 컴포넌트 전체 목록, 각 prop과 variant
- **누락 컴포넌트 처리**: `docs/Missing_Component_Workflow.md` — 없는 컴포넌트가 있을 때 판단 기준과 처리 방법
- **Figma 디자인 빌드 가이드**: `docs/Figma_Design_Build_Guide.md` — SLOT/INSTANCE_SWAP 처리, 클론 워크플로, 폰트 패턴
- **Figma 디자인 시스템**: https://www.figma.com/design/wJaqzyxIazITPQ7ULFJY4o/Platform-Design-System — 실제 컴포넌트를 가져올 소스

### 작업 순서
1. 명령 수신 → 작업 계획 작성 → 사용자 OK 확인
2. `docs/Component_Contracts.md`에서 컴포넌트 Contract 확인
3. Figma 디자인 시스템에서 컴포넌트 가져올 때 `node.description`도 함께 읽어 용도/제약사항 파악
4. 파악한 내용을 바탕으로 화면 조합

### 주의사항
- `Status: Internal` 컴포넌트는 화면에 절대 노출하지 않는다
- `Status: Experimental` 컴포넌트는 신규 화면에서 직접 의존하지 않는다
- `contentPaddingX: none`이 명시된 섹션만 full-width로 처리한다
- 명령에 없는 텍스트/이미지/구조를 임의로 추가하지 않는다
- 이전 화면 내용을 재활용하지 않는다

### Slot 처리 규칙
1. **우선: swapComponent** — Slot 안 인스턴스를 preferred instances의 DS 컴포넌트로 교체
2. **차선: 커스텀 디자인** — 맞는 DS 컴포넌트가 없을 때만 Slot 내부에 커스텀 Frame 배치 허용

---

## 토큰 관련 작업 시

- **토큰 네이밍 컨벤션**: `tokens/BGZT_Token_Naming_Convention.md`
- **토큰 파일 위치**: `tokens/` 폴더 (primitive / semantic / colorMode)
- **CSS 변수 출력**: `tokens/colorMode/light.json`, `tokens/colorMode/dark.json`

---

## 문서 관리 규칙

| 문서 | 관리 주체 | 업데이트 시점 |
|---|---|---|
| `docs/Component_Contracts.md` | 디자이너 | 컴포넌트 추가/변경 시 |
| `docs/Component_Contract_Convention.md` | 디자이너 | 컨벤션 변경 시 |
| `docs/Missing_Component_Workflow.md` | 디자이너 | 워크플로우 변경 시 |
| `tokens/` | 디자이너 | 토큰 변경 시 |

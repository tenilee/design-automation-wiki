# Figma Design Build Guide

> MCP(Claude)를 사용해 Platform Design System 컴포넌트로 Figma 화면을 빌드할 때 따르는 기술 가이드입니다.
> 개발 가이드와 무관하며, Figma Plugin API 기반 디자인 자동화에만 적용됩니다.

---

## 목차

1. [기본 원칙](#1-기본-원칙)
2. [클론 워크플로](#2-클론-워크플로)
3. [INSTANCE_SWAP 처리 규칙](#3-instance_swap-처리-규칙)
4. [Slot 처리 규칙](#4-slot-처리-규칙)
5. [Section 속성 처리](#5-section-속성-처리)
6. [Inter 폰트 우선 접근](#6-inter-폰트-우선-접근)
7. [컴포넌트별 주의사항](#7-컴포넌트별-주의사항)

---

## 1. 기본 원칙

- **항상 DS 컴포넌트 우선** — 직접 Frame/Shape을 그리지 않는다. DS에 있는 컴포넌트를 가져와 조합한다.
- **명령에 있는 것만** — 지시하지 않은 텍스트, 이미지, 구조를 임의로 추가하지 않는다.
- **이전 화면 재활용 금지** — 클론은 구조(껍데기)만 사용하고, 콘텐츠는 항상 새로 구성한다.
- **컴포넌트 description 확인** — DS에서 컴포넌트 가져올 때 `node.description`을 함께 읽어 용도/제약 파악 후 적용한다.

---

## 2. 클론 워크플로

Page DS 컴포넌트(Section Group 포함)는 직접 생성 시 Pretendard Variable 폰트 제약으로 append가 차단된다. **기존 파일에 있는 외부 FRAME을 클론해서 구조를 가져오는 방식**을 사용한다.

```js
// 1. 소스 페이지로 이동 후 외부 FRAME 클론
const sourcePage = figma.root.children.find(p => p.name === "소스 페이지명");
await figma.setCurrentPageAsync(sourcePage);
const sourceFrame = await figma.getNodeByIdAsync("FRAME_ID");
const clone = sourceFrame.clone();

// 2. 대상 페이지로 이동 (FRAME은 appendChild 가능)
const targetPage = figma.root.children.find(p => p.name === "대상 페이지명");
targetPage.appendChild(clone);
clone.x = 0;
clone.y = 0;
clone.name = "화면 이름";

// 3. 이후 대상 페이지에서 콘텐츠 수정
const targetPage2 = figma.root.children.find(p => p.name === "대상 페이지명");
await figma.setCurrentPageAsync(targetPage2);
```

**주의:**
- Page DS 인스턴스 자체가 아니라 그것을 감싸는 **외부 FRAME**을 클론한다.
- 클론 후 콘텐츠(텍스트, 이미지, 슬롯 내용)는 반드시 명령 기준으로 새로 설정한다.

---

## 3. INSTANCE_SWAP 처리 규칙

**`setProperties`로 INSTANCE_SWAP 타입 prop 변경 불가.** 에러: `"Property value is incompatible with component property type"`

INSTANCE_SWAP이 걸린 prop은 컴포넌트 종류와 무관하게 동일하게 처리한다.

### 처리 방법

해당 prop에 연결된 인스턴스를 직접 찾아 `swapComponent()`로 교체한다.

```js
// ❌ 잘못된 방법 — INSTANCE_SWAP은 setProperties로 변경 불가
navBar.setProperties({ "rightItem1#hash": "컴포넌트키" }); // 에러 발생

// ✅ 올바른 방법 — 인스턴스를 직접 찾아 swapComponent
const targetItem = navBar.findOne(n => n.type === "INSTANCE" && n.name === "navigationBarItem-share");
const newComp = await figma.importComponentByKeyAsync("컴포넌트키");
targetItem.swapComponent(newComp);
```

### 적용 예시

| 케이스 | INSTANCE_SWAP prop | 처리 방법 |
|---|---|---|
| NavBar 우측 아이템 | `rightItem1`, `rightItem2`, `rightItem3` | 각 아이템 인스턴스 findOne → swapComponent |
| NavBar 좌측 아이템 | `leftItem1` | 아이템 인스턴스 findOne → swapComponent |
| Section headerSlot | `headerSlot` | slot.children[0] → swapComponent |
| Section contentSlot | `contentSlot` | slot.children[0] → swapComponent |
| DSIcon 아이콘 교체 | `tintedIcon`, `originalIcon` | 아이콘 인스턴스 findOne → swapComponent |

### VARIANT / TEXT 타입은 setProperties 사용 가능

```js
// VARIANT — setProperties ✅
section.setProperties({ "contentPaddingX": "page" });
navBar.setProperties({ "rightItem": "3" });

// TEXT — setProperties ✅ (단, 폰트 로드 필요 시 에러 가능 → 섹션 6 참고)
navBar.setProperties({ "title#hash": "추천 상품" });
```

---

## 4. Slot 처리 규칙

Section의 `headerSlot`, `contentSlot`, `footerSlot`은 SLOT 타입으로, setProperties로 직접 교체 불가.

### 우선순위

**1순위: swapComponent — preferred instances의 DS 컴포넌트로 교체**

Slot 안에 이미 있는 인스턴스를 DS 컴포넌트로 교체한다.

```js
const contentSlot = section.findOne(n => n.name === "contentSlot" && n.type === "SLOT");
const slotInst = contentSlot.children[0]; // 기존 인스턴스

if (slotInst.type === "INSTANCE") {
  const gridSet = await figma.importComponentSetByKeyAsync("GRID_KEY");
  const variant = gridSet.children.find(c => c.name.includes("twoColumn"));
  slotInst.swapComponent(variant); // ✅
}
```

**2순위: 커스텀 디자인 — DS에 맞는 컴포넌트가 없을 때만 허용**

Slot 안 기존 노드를 제거하고 커스텀 Frame을 배치한다.

```js
const existingNode = contentSlot.children[0];
existingNode.remove();

const customFrame = figma.createAutoLayout("VERTICAL");
// ... 커스텀 구성
contentSlot.appendChild(customFrame); // ✅ SLOT에 FRAME 배치 가능
```

### headerSlot preferred instances

Section의 `headerSlot`에는 `sectionHeader` 컴포넌트가 preferred instances로 등록되어 있다.

```js
const headerSlot = section.findOne(n => n.name === "headerSlot" && n.type === "SLOT");
const headerInst = headerSlot.children[0];

if (headerInst.type === "INSTANCE") {
  const sectionHeaderSet = await figma.importComponentSetByKeyAsync("SECTION_HEADER_KEY");
  const baseVariant = sectionHeaderSet.children.find(c => c.name.includes("variant=base"));
  headerInst.swapComponent(baseVariant); // ✅ sectionHeader로 교체
}
```

---

## 5. Section 속성 처리

Section 작업 시 `showHeader`와 `showFooter`를 **항상 명시적으로** 설정한다. 설정하지 않으면 빈 영역이 화면에 노출된다.

```js
const showHeaderKey = Object.keys(section.componentProperties).find(k => k.startsWith("showHeader"));
const showFooterKey = Object.keys(section.componentProperties).find(k => k.startsWith("showFooter"));

section.setProperties({
  "contentPaddingX": "page",       // "page" | "none"
  [showHeaderKey]: true,            // header 필요 여부
  [showFooterKey]: false,           // footer 필요 여부 (불필요하면 반드시 false)
});
```

### SectionGroup count

화면에 필요한 Section 수만큼 count를 설정한다. count 초과 Section은 숨겨지지만 노드는 존재한다.

```js
const sg = clone.findOne(n => n.name === "Section Group" && n.type === "INSTANCE");
sg.setProperties({ "count": "3" }); // 1~5
```

---

## 6. Inter 폰트 우선 접근

MCP 플러그인 환경에서 Pretendard Variable 로드 불가. **Inter만 사용 가능.**

### DS 컴포넌트 텍스트 수정 패턴

```js
// 1. Inter 폰트 먼저 로드
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });

// 2. textStyleId 해제 → fontName 교체 → characters 수정
const textNode = instance.findOne(n => n.type === "TEXT");
if (textNode.textStyleId) textNode.textStyleId = ""; // 바인딩 해제
textNode.fontName = { family: "Inter", style: "Semi Bold" };
textNode.characters = "새 텍스트";
```

### DS 컴포넌트 append 전 swapToInter

Pretendard Variable을 사용하는 DS 컴포넌트를 새로 생성해서 append할 때, **배치 전에** 폰트를 Inter로 교체해야 한다.

```js
async function swapToInter(instance) {
  await figma.loadFontAsync({ family: "Inter", style: "Bold" });
  await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" });
  await figma.loadFontAsync({ family: "Inter", style: "Medium" });
  await figma.loadFontAsync({ family: "Inter", style: "Regular" });
  const styleMap = {
    "Bold": "Bold", "Medium": "Medium",
    "SemiBold": "Semi Bold", "Semi Bold": "Semi Bold",
    "Regular": "Regular", "Light": "Regular"
  };
  instance.findAll(n => n.type === "TEXT").forEach(t => {
    if (t.textStyleId) t.textStyleId = "";
    t.fontName = { family: "Inter", style: styleMap[t.fontName.style] || "Regular" };
  });
  return instance;
}

// 사용
const btnInst = brandSolidComp.createInstance();
await swapToInter(btnInst);
parentFrame.appendChild(btnInst); // ✅
```

**화면 완성 후:** Figma 데스크탑에서 **Check design → Fix font**로 Inter → Pretendard Variable 일괄 교체.

---

## 7. 컴포넌트별 주의사항

### ProductCard Grid

- DS 내부적으로 DSGrid 기반 → rows prop으로 행 수 조절 가능
- 8개 이상 상품이 필요할 때 Grid 두 개 쌓지 말고 rows 조절 사용
- variants: `twoColumn` (4-6개 권장) / `threeColumn` (6-9개 권장)

### NavigationBar

- `title`: TEXT 타입 → `setProperties({ "title#hash": "타이틀" })` ✅
- `leftItem` / `rightItem` 개수: VARIANT 타입 → `setProperties({ "rightItem": "3" })` ✅
- `leftItem1`, `rightItem1~3`: INSTANCE_SWAP 타입 → **setProperties 불가**, swapComponent 사용

### Button

- TEXT 타입 prop도 Pretendard Variable 폰트 필요 시 setProperties 에러 발생
- `textStyleId = ""` 해제 후 직접 TEXT 노드 수정 방식으로 처리

### Section

- `contentPaddingX`: `page`(좌우 패딩 있음) / `none`(full-width, hero 이미지 등)
- `showHeader` / `showFooter`: 항상 명시적으로 설정 (기본값에 의존하지 않음)
- SLOT 안 노드 타입 확인 필수: INSTANCE면 swapComponent, FRAME이면 remove 후 재배치

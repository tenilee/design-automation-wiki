# Figma Design Build Guide

> MCP(Claude)를 사용해 Platform Design System 컴포넌트로 Figma 화면을 빌드할 때 따르는 기술 가이드입니다.
> 개발 가이드와 무관하며, Figma Plugin API 기반 디자인 자동화에만 적용됩니다.

---

## 목차

1. [기본 원칙](#1-기본-원칙)
2. [빌드 워크플로](#2-빌드-워크플로)
3. [INSTANCE_SWAP 처리 규칙](#3-instance_swap-처리-규칙)
4. [Slot 처리 규칙](#4-slot-처리-규칙)
5. [Section 속성 처리](#5-section-속성-처리)
6. [Inter 폰트 우선 접근](#6-inter-폰트-우선-접근)
7. [Page/Section append 실패 대응](#7-pagesection-append-실패-대응)
8. [컴포넌트별 주의사항](#8-컴포넌트별-주의사항)

---

## 1. 기본 원칙

- **항상 DS 컴포넌트 우선** — 직접 Frame/Shape을 그리지 않는다. DS에 있는 컴포넌트를 가져와 조합한다.
- **명령에 있는 것만** — 지시하지 않은 텍스트, 이미지, 구조를 임의로 추가하지 않는다.
- **이전 화면 재활용 금지** — 클론은 구조(껍데기)만 사용하고, 콘텐츠는 항상 새로 구성한다.
- **컴포넌트 description 확인** — DS에서 컴포넌트 가져올 때 `node.description`을 함께 읽어 용도/제약 파악 후 적용한다.

---

## 2. 빌드 워크플로

Page DS 컴포넌트(Section Group 포함)는 직접 생성 시 Pretendard Variable 폰트 제약으로 append가 차단된다. **파일 내 아무 Page 인스턴스나 찾아 클론 후 즉시 blank reset하는 방식**을 사용한다.

소스 화면의 콘텐츠에 의존하지 않으므로 어떤 파일/페이지/사용자가 작업해도 항상 동일한 빈 구조에서 시작할 수 있다.

### Step 1: 아무 Page 인스턴스 찾아 클론 + blank reset

```js
// 1. 대상 페이지로 이동
const targetPage = figma.root.children.find(p => p.name === "대상 페이지명");
await figma.setCurrentPageAsync(targetPage);

// 2. 파일 내 아무 Page 인스턴스를 감싸는 FRAME 찾기 (어떤 화면이든 무관)
let sourceFrame = null;
for (const page of figma.root.children) {
  if (page.id === targetPage.id) continue;
  await figma.setCurrentPageAsync(page);
  sourceFrame = page.findOne(n =>
    n.type === "FRAME" && n.findOne(c => c.name === "Page" && c.type === "INSTANCE")
  );
  if (sourceFrame) break;
}
await figma.setCurrentPageAsync(targetPage);

// 3. 클론 후 대상 페이지로 이동
const clone = sourceFrame.clone();
targetPage.appendChild(clone);
clone.name = "화면 이름";

// 4. 기존 프레임과 겹치지 않도록 우측 배치
let maxX = 0;
for (const child of targetPage.children) {
  if (child.id !== clone.id) maxX = Math.max(maxX, child.x + child.width);
}
clone.x = maxX > 0 ? maxX + 80 : 0;
clone.y = 0;

// 5. Blank reset — 소스 콘텐츠 무관하게 항상 동일한 빈 구조로 초기화
const navBar = clone.findOne(n => n.name === "Navigation Bar" && n.type === "INSTANCE");
const titleKey = Object.keys(navBar.componentProperties).find(k => k.startsWith("title"));
navBar.setProperties({ [titleKey]: "", "leftItem": "none", "rightItem": "none" });

const sg = clone.findOne(n => n.name === "Section Group" && n.type === "INSTANCE");
sg.setProperties({ "count": "5" });

const dsImageComp = await figma.importComponentByKeyAsync("6155ef9924b9b5eb89ddb6b9c448cd4c0095beec"); // DSImage fill/product

for (let i = 1; i <= 5; i++) {
  const section = clone.findOne(n => n.name === `Section${i}` && n.type === "INSTANCE");
  if (!section) continue;
  const showHeaderKey = Object.keys(section.componentProperties).find(k => k.startsWith("showHeader"));
  const showFooterKey = Object.keys(section.componentProperties).find(k => k.startsWith("showFooter"));
  section.setProperties({
    "contentPaddingX": "page",
    [showHeaderKey]: false,
    [showFooterKey]: false
  });
  // contentSlot을 DSImage(기본 placeholder)로 초기화
  const contentSlot = section.findOne(n => n.name === "contentSlot" && n.type === "SLOT");
  const slotInst = contentSlot?.children[0];
  if (slotInst?.type === "INSTANCE") slotInst.swapComponent(dsImageComp);
}
```

### Step 2: 화면 구성

blank reset 완료 후 명령 기준으로 NavBar, SectionGroup count, 각 Section 콘텐츠를 설정한다.

### Step 3: 완성 후 Frame 리사이즈 (필수)

SectionGroup count 변경으로 실제 높이가 달라지므로 항상 실행한다.

```js
const pageInst = clone.findOne(n => n.name === "Page" && n.type === "INSTANCE");
clone.resize(clone.width, pageInst.y + pageInst.height);
```

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
| Icon 아이콘 교체 | `icon` | 아이콘 인스턴스 findOne → swapComponent |

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
  const baseVariant = sectionHeaderSet.children.find(c => c.name.includes("variant=titleOnly"));
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

## 7. Page/Section append 실패 대응

MCP 환경에서는 `Page` 또는 `Section` 인스턴스를 직접 생성해서 append할 때 Pretendard Variable 폰트가 로드되지 않아 실패할 수 있다.

대표 오류:

```text
appendChild: unloaded font "Pretendard Variable Medium"
appendChild: unloaded font "Pretendard Variable Bold"
```

### 발생 조건

- `Page` / `Section` 기본 인스턴스 내부에 `SlotPlaceholder(제거 후 사용)`이 남아 있다.
- `SlotPlaceholder` 또는 기본 텍스트 노드가 Pretendard Variable `Bold` / `Medium`을 사용한다.
- `setProperties()`로 TEXT prop을 수정하려고 할 때 컴포넌트가 미로드 Pretendard 폰트를 참조한다.
- Slot에 새 DS 컴포넌트를 append하는 순간 부모 `Section` 내부의 남은 Pretendard 텍스트가 다시 검사된다.

### 권장 대응 순서

1. **정상 DS Page 외부 Frame 클론**
   - 가장 안정적인 방식이다.
   - 같은 파일 안에 이미 정상 배치된 `Page` 외부 Frame이 있으면 그것을 클론한다.
   - 클론 후 즉시 blank reset → 명령에 맞게 navigation, section, slot content, 텍스트를 전부 새로 설정한다.

2. **append 전 SlotPlaceholder 제거 및 실제 DS 콘텐츠 선배치**
   - 직접 생성이 불가피하면 `Page` / `Section`을 canvas에 append하기 전에 `headerSlot`, `contentSlot`, `footerSlot`의 기본 placeholder를 제거한다.
   - 실제 `sectionHeader`, `ProductCard Grid`, `Button` 등 DS 콘텐츠를 먼저 넣고, 모든 TEXT 노드를 Inter로 바꾼 뒤 append를 시도한다.
   - 순서: `variant/boolean prop 설정 → Inter 폰트 정규화 → characters 수정 → append`

3. **실패 시 중단하고 사용자에게 보고**
   - `Page` / `Section` append가 계속 실패하면 완료된 것처럼 말하지 않는다.
   - DS 하위 컴포넌트를 외부 Frame에 조합하는 임시 우회는 가능하지만, `Page` 컴포넌트 기반이 아님을 명확히 보고한다.

### 금지

- `SlotPlaceholder(제거 후 사용)`를 실제 화면에 노출하지 않는다.
- Page/Section 없이 외부 Frame으로 우회한 결과를 `Page` 컴포넌트로 만든 화면이라고 설명하지 않는다.
- 오류를 무시하고 커스텀 Frame/Shape 중심으로 재작성하지 않는다. DS 하위 컴포넌트를 최대한 유지한다.

---

## 8. 컴포넌트별 주의사항

### ProductCard Grid

- DS 내부적으로 DSGrid 기반 → rows prop으로 행 수 조절 가능
- 8개 이상 상품이 필요할 때 Grid 두 개 쌓지 말고 rows 조절 사용
- variants: `grid=two` / `grid=three`
- `grid=two`는 `ProductCard TwoColumn`, `grid=three`는 `ProductCard ThreeColumn`을 사용한다.
- `ProductCard Thumbnail`, `ProductCard Info TwoColumn`, `ProductCard Info ThreeColumn`, `ProductCard Brand Badge`, `ProductCard Favorite Toggle`은 Internal 부품 — 화면 조합 시 직접 배치하지 않는다.

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

### Icon

- 컴포넌트화 대상이 아닌 1회성 커스텀 슬롯에서만 사용한다.
- Button, NavigationBar, Toggle, Badge 등 기존 컴포넌트 내부 아이콘에는 직접 사용하지 않는다.
- 반복 사용되거나 2개 이상 화면에서 재사용될 가능성이 있는 아이콘 UI는 별도 컴포넌트화를 검토한다.
- `icon`은 INSTANCE_SWAP으로 교체한다.
- `sic.*`: tint 적용 대상인 단색 시스템 아이콘 — `tint=neutral | brand | positive | inverse` 사용
- `iic.*`: 브랜드/서비스 아이콘처럼 원본 컬러 유지가 필요한 아이콘 — `tint=none` 사용
- `tint=none`: icon 슬롯에 들어온 소스 컴포넌트의 원본 fill/binding을 그대로 따른다.
- `tint=inverse`: `sic.*` 아이콘에 `color.fg.neutral.on-solid` 바인딩 (어두운 배경 위 아이콘)

---
name: build-figma
description: "用於 \\\"/build-figma\\\"、帶有 node-id 的 figma.com/design URL，或將 \\\"implement this Figma\\\" 應用於網頁、桌面或共用 UI。協調 Figma MCP 接收流程，將節點對應至儲存庫自有的設計系統，然後執行 verify-this 視覺審判。請勿貼上 Figma Tailwind。"
icon: paintbrush
color: magenta
# Intentionally model-invocable so agents discover it from Figma URLs.
---

<a id="build-figma"></a>

# 建置 Figma

Figma 至生產環境 UI 的協調器。它**不**取代下列的 skills。在該階段開始時閱讀每個 skill；請勿在此重複說明。

**需要** `figma`（plugin 和已連線的 MCP）以及 `cursor-team-kit`。請先透過 [../dyl-mode/references/requirements.md](../dyl-mode/references/requirements.md) 檢查。若缺失，請停止並告知使用者執行 `/add-plugin <name>`。

塑造這些閘門的失敗模式：[references/failure-lessons.md](references/failure-lessons.md)。

<a id="delegate-to-do-not-copy"></a>

## 委派給（請勿複製）

| 階段 | 閱讀 |
| --- | --- |
| Figma MCP 機制 | 來自 Figma plugin 的 `figma-design-to-code`，在每次 `get_design_context` 之前 |
| 設計系統清單 | [references/design-system-discovery.md](references/design-system-discovery.md)，然後是它找到的任何儲存庫本地設計系統 skill 或規則 |
| 視覺宣告 / 審判格式 | 來自 `cursor-team-kit` 的 `verify-this` + [references/visual-judge.md](references/visual-judge.md) |
| 驅動即時 UI | 來自 `cursor-team-kit` 的 `control-ui`，或該介面儲存庫自有的控制 skill |

<a id="gates"></a>

## 閘門

```
Build-figma progress:
- [ ] 0. Parse fileKey + nodeId (refuse file-only URLs)
- [ ] 1. Intake done before any product UI edit (see below)
- [ ] 2. Mapped to the repo's primitives/tokens only
- [ ] 3. Styling in the surface's own styling system
- [ ] 4. Visual judge VERIFIED per visual-judge.md (verify-this claim shape)
```

閘門 1 會阻擋編碼。"先功能，後視覺潤飾"是主要的失敗模式。

<a id="workflow-thin"></a>

## 工作流程（精簡）

<a id="0-parse-url"></a>

### 0. 解析 URL

`figma.com/design/:fileKey/...?node-id=1-2` → `fileKey`，`nodeId`（`1-2` → `1:2`）。分支 URL 使用 `branchKey` 作為 `fileKey`。沒有 `node-id` → 停止。

<a id="1-intake-blocking"></a>

### 1. 接收（阻擋）

遵循 `figma-design-to-code`。呼叫 `get_design_context` 和 `get_screenshot`。如果回應過於稀疏或過大，請拆分為可實作的**子**節點。從 MCP URL 下載品牌資產；切勿重新繪製標誌。

執行 [design-system discovery](references/design-system-discovery.md)。然後撰寫 `/tmp/build-figma/<slug>/intake.md`：

- 目標介面、程式碼路徑，以及您將如何驅動其即時狀態。
- 節點樹。
- **指標表格。** Figma px → token，或在沒有相符 token 時使用固定值。
- **節點 → 原始元件對應。** 將每個 Figma 節點對應至現有元件。將任何沒有相符項的節點標記為新增，並說明原因。

<a id="2-implement"></a>

### 2. 實作

使用該介面的樣式系統和已對應的元件。當 Figma px 與原始元件的預設大小不一致時，請在該樣式系統中固定 Figma 指標。請勿全域性更改原始元件的預設值。下方為嚴格禁止事項。

<a id="3-visual-judge-blocking"></a>

### 3. 視覺審判（阻擋）

使用 [references/visual-judge.md](references/visual-judge.md) 特化 `verify-this`。將 Figma 截圖視為基準，將即時擷取視為處理組。先匹配主題極性，然後套用評分標準並撰寫審判結果。在 NOT VERIFIED 之後，修正失敗的專案，重新擷取，並再次套用評分標準。重複直到 VERIFIED 或 INCONCLUSIVE。在 INCONCLUSIVE 後停止並報告阻擋因素；請勿宣稱閘門 4 已完成。

<a id="4-report"></a>

### 4. 報告

接收路徑、對應重點、審判結果 + 證據路徑、剩餘缺口。

<a id="hard-bans"></a>

## 嚴格禁止事項

1. 將 Figma MCP Tailwind/React 貼上為產品程式碼。
2. 在 Figma 或儲存庫已有品牌資產時，自行發明品牌資產。
3. 對不同的 Figma 結構使用"差不多"的原始元件。
4. 在未檢查 Figma px 的情況下，信任文字/按鈕/對話方塊大小 token。
5. 在沒有審判的情況下宣稱視覺已完成。
6. 在 `intake.md` 存在之前編輯產品 UI。

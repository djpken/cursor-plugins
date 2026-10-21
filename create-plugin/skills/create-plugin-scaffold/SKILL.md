---
name: create-plugin-scaffold
description: "建立新的 Cursor 外掛程式骨架，包含有效的 manifest、元件目錄以及市集整合。適用於開始建立新外掛程式，或在多外掛程式儲存庫中新增外掛程式時使用。"
---

<a id="create-plugin-scaffold"></a>

# 建立外掛程式骨架

<a id="trigger"></a>

## 觸發條件

您需要從零開始建立新的 Cursor 外掛程式，並使其準備好供本地使用或提交至市集。

<a id="required-inputs"></a>

## 必要輸入

- 外掛程式名稱（小寫 kebab-case）
- 外掛程式用途與目標使用者
- 要包含的元件集合（`rules`、`skills`、`agents`、`commands`、`hooks`、`mcpServers`）
- 儲存庫樣式（`single-plugin` 或 `multi-plugin marketplace`）

<a id="output-location"></a>

## 輸出位置

預設情況下，在外掛程式使用者的本地外掛程式目錄中建立外掛程式：

```
~/.cursor/plugins/local/<plugin-name>/
```

此路徑可讓外掛程式立即在 Cursor 中可用，無需任何安裝步驟。如果使用者明確要求在其他位置建立外掛程式（例如在現有儲存庫或特定目錄中），請遵循該選擇。

<a id="workflow"></a>

## 工作流程

1. 驗證外掛程式名稱格式：小寫 kebab-case，以字母數字字元開頭和結尾。
2. 確定目標目錄：
   - 預設：`~/.cursor/plugins/local/<plugin-name>/`
   - 覆寫：如果使用者指定了路徑，則使用該路徑。
   - 如果目錄不存在，請建立該目錄（及其父目錄）。
3. 在目標目錄中建立基礎檔案：
   - `.cursor-plugin/plugin.json`
   - `README.md`
   - `LICENSE`
   - 選填 `CHANGELOG.md`
4. 填入 `plugin.json`：
   - 必要：`name`
   - 建議：`version`、`description`、`author`、`license`、`keywords`
   - 僅在需要非預設偵測時，才新增明確的元件路徑。
5. 建立具有有效 frontmatter 的元件檔案：
   - 規則：`.mdc` 搭配 `description`、`alwaysApply`，選填 `globs`
   - Skills：`skills/<skill-name>/SKILL.md` 搭配 `name`、`description`
   - Agents：`agents/*.md` 搭配 `name`、`description`
   - Commands：`commands/*.(md|txt)` 搭配 `name`、`description`
6. 如果儲存庫使用 `.cursor-plugin/marketplace.json`，請新增外掛程式專案：
   - `name`
   - `source`
   - 選填中繼資料（`description`、`keywords`、`category`、`tags`）
7. 確保所有 manifest 路徑都是相對路徑、有效，且不使用絕對路徑或父目錄遍歷。

<a id="guardrails"></a>

## 防護措施

- 保持外掛程式專注於單一使用案例。
- 優先使用簡潔、可執行的 skill 和 rule 文字，而非長篇大論。
- 不要參考不存在的檔案。
- 除非需要自訂路徑，否則使用資料夾偵測預設值。
- 除非使用者提供不同路徑，否則一律儲存至 `~/.cursor/plugins/local/<plugin-name>/`。

<a id="output"></a>

## 輸出

- 外掛程式的已建立檔案樹（包含輸出目錄的完整路徑）
- 最終 `plugin.json`
- 市集專案（如適用）
- 必要欄位和元件中繼資料的簡短驗證報告
- 確認外掛程式已儲存於 `~/.cursor/plugins/local/` 下並準備好使用

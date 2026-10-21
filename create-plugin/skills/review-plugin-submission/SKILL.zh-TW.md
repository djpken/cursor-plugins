---
name: review-plugin-submission
description: "稽核 Cursor plugin 是否已準備好上架 marketplace。在發布前，用於驗證 manifest、元件 metadata、發現路徑以及提交品質。"
---

<a id="review-plugin-submission"></a>

# 檢視 plugin 提交

<a id="trigger"></a>

## 觸發條件

Plugin 已實作完成，需要在提交或發布前進行最終品質檢查。

<a id="workflow"></a>

## 工作流程

1. 驗證 manifest 有效性：
   - `.cursor-plugin/plugin.json` 存在
   - `name` 為有效的小寫 kebab-case
   - metadata 欄位一致（`description`、`version`、`author`、`license`）
2. 驗證元件可發現性：
   - Skills 位於 `skills/*/SKILL.md`
   - Rules 位於 `rules/`，格式為 `.mdc` 或 markdown 變體
   - Agents 位於 `agents/` 的 markdown 檔案
   - Commands 位於 `commands/` 的 markdown 或文字檔案
   - Hooks 位於 `hooks/hooks.json`
   - MCP 設定位於 `mcp.json`（或 `mcpServers` 覆寫）
3. 驗證元件 metadata：
   - Skills 包含 `name` 和 `description` frontmatter
   - Rules 包含有效的 frontmatter 和明確的指引
   - Agents 和 commands 包含 `name` 和 `description`
4. 驗證儲存庫整合：
   - 對於 marketplace 儲存庫，plugin 專案存在於 `.cursor-plugin/marketplace.json`
   - `source` 解析至 plugin 目錄，且名稱唯一
5. 驗證檔案品質：
   - `README.md` 說明用途、安裝方式及元件涵蓋範圍
   - 選用的 logo 路徑有效且由儲存庫託管

<a id="checklist"></a>

## 檢查清單

- Manifest 存在且可解析為有效 JSON
- 所有宣告的路徑均存在且為相對路徑
- 沒有損壞的檔案參考
- Skills、Rules、Agents、Commands 沒有遺漏 frontmatter
- Plugin 範圍明確且專注
- Marketplace 註冊完成（若為多 plugin 儲存庫）

<a id="output"></a>

## 輸出

- 依章節劃分的透過/失敗報告
- 優先排序的修復清單
- 最終提交建議

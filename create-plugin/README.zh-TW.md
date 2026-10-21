<a id="create-plugin"></a>

# 建立 plugin

用於建立可上架 marketplace 的 Cursor plugin 的 meta workflows。

<a id="installation"></a>

## 安裝

```bash
/add-plugin create-plugin
```

<a id="components"></a>

## 元件

### Skills

| Skill | 說明 |
|:------|:------------|
| `create-plugin-scaffold` | 建立新的 plugin 目錄，包含 manifest、元件以及 repository 的連結設定 |
| `review-plugin-submission` | 根據 marketplace 的規範執行提交前的品質檢查 |

### Rules

| Rule | 說明 |
|:-----|:------------|
| `plugin-quality-gates` | 確保 plugin manifest、元件 metadata 以及路徑有效且一致 |

### Agents

| Agent | 說明 |
|:------|:------------|
| `plugin-architect` | 根據具體的使用案例設計 plugin 結構與元件組合 |

### Commands

| Command | 說明 |
|:--------|:------------|
| `create-plugin` | 建立包含正確檔案與 metadata 的新 plugin 骨架 |

<a id="typical-flow"></a>

## 典型流程

1. 使用 `/create-plugin` 並提供 plugin 名稱、用途以及目標元件型別。
2. 產生或更新 `plugin.json`，然後視需要新增 rules/skills/agents/commands。
3. 在發布或提交至 marketplace 之前執行 `review-plugin-submission`。

<a id="license"></a>

## 授權條款

MIT

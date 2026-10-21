<a id="incident--postmortem-context"></a>

# 事件與事後分析背景

- **Linear**：尋找標有 `incident`、`sev-*`、`postmortem-action-item`、`reliability` 的票證
- **Slack**：在目標程式碼新增的日期附近，搜尋 `#sev-*` 和 `#incident-*` 頻道
- **Git**：包含「fix for incident」、「add defensive check」、「revert」後接「re-apply with...」等訊息內容的提交，是強烈的訊號

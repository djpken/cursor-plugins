<a id="adapters"></a>

# 介面卡

介面卡是外部 IO 外殼。它們不擁有編排狀態。

## SlackAdapter

`SlackAdapter` 是 orchestrate 附帶的唯一介面卡。Slack 是人機可見性層：

- 發布執行啟動訊息
- 在啟動執行緒中，為每個任務發布或編輯一則任務訊息
- 當操作員明確要求時上傳檔案
- 讀取反應以進行 Andon
- 在執行執行緒中發布自由格式評論

`postRunKickoff` 是唯一會寫入頻道根目錄的介面卡方法。CLI 會解析一次頻道，然後使用該頻道建構介面卡。之後所有的 Slack 寫入操作都會接收一個 `threadTs`，並保持在同一個執行執行緒中。

Orchestrate 擁有 Slack 狀態映象、Andon 以及評論重試佇列。代理仍可透過 MCP 直接呼叫 Linear、GitHub、Slack、Notion 以及其他即席外部工作。這些系統不是介面卡的目的地。

<a id="comment-retry-queue"></a>

## 評論重試佇列

`comment-retry-queue.json` 依目的地字串儲存必要的評論。有效的 Slack 格式為 `slack:<channel>:<thread_ts>`。Workspace 呼叫會在發布或清空佇列之前，針對 `plan.slackKickoffRef` 驗證目的地。

常規生命週期映象是盡力而為的。必要評論會依佇列的退避排程進行重試。

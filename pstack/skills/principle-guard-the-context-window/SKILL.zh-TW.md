---
name: principle-guard-the-context-window
description: "在上下文即將填滿時套用：大型輸出、長檔案、重複讀取、扇出規劃。將大量內容路由到子代理；在主執行緒中保留摘要，而非原始資料。"
disable-model-invocation: true
---

<a id="guard-the-context-window"></a>

# 保護上下文視窗

**原因：** 上下文溢位會降低推理品質、產生壓縮失真，並使進度停滯。

**模式：**
- **大小階段與上限範圍。** 限制每個階段的檔案數量，設定回合預算，並考量機製成本。

---
name: principle-laziness-protocol
description: "在重構、評估差異大小或傾向於新增抽象、層級或訊號傳遞時應用。傾向於刪除和最小的變更來解決問題。"
disable-model-invocation: true
---

<a id="laziness-protocol"></a>

# 懶惰協議

- **整合決策。** 不要在多個地方重複相同的選擇。將其置於單一真相來源之後，並將結果作為簡單標誌傳遞。
- **最小化差異。** 做出最小的變更來解決問題。更少的行數勝過“優雅”的樣板程式碼。

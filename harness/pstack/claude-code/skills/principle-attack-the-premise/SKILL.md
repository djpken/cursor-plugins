---
name: principle-attack-the-premise
description: "當兩個或更多共享同一前提的修正在同一關卡失敗時，請將前提寫下來並直接測試它，再寫下另一個假定該前提的修正。"
disable-model-invocation: true
---

<a id="attack-the-premise"></a>

# 攻擊前提

當兩個或更多共享同一前提的修正在同一關卡失敗時，應懷疑前提本身，而非修正。

**原因：** 每個失敗的修正都是對它所假定信念的證據。在同一前提上進行第三次修正，是在證據已表明原因不在那裡的情況下浪費精力。

**模式：**
- **將前提寫下來。** 前提是每個失敗修正都假定的一句話。
- **在下一個修正之前測試前提。** 尋找一個若前提為假則結果會不同的觀察，並進行該觀察。檢視失敗的分佈方式，而非僅看其規模。使檢查可重跑，依據 [Build the Lever](../principle-build-the-lever/SKILL.md)。
- **遵循證據。** 若證據與前提矛盾，原因就在其他地方。尋找失敗的共同點，而非前提所未預測的部分。若證據確認前提，則依據 [Fix Root Causes](../principle-fix-root-causes/SKILL.md) 提出下一個「為什麼」。早期的修正可能處理了結果而非原因。
- **移除條件而非補償它**，依據 [Laziness Protocol](../principle-laziness-protocol/SKILL.md)。若證據顯示某部分總是落在失敗的一方，改變導致它在那裡的因素。一個在之後清理的機制會留下原因並在每次執行增加工作量。

**停止：** 在前提寫下並測試之前，不要開始下一個修正。

此原則與 [Redesign from First Principles](../principle-redesign-from-first-principles/SKILL.md) 不同，該原則根據新需求重建設計。它質疑當前設計所假定的一個事實。

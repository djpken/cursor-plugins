<a id="visual-judge"></a>

# 視覺評審

將 `verify-this`（源自 `cursor-team-kit`）專用於 Figma 到實際 UI 的轉換。使用其可證偽的宣告、產物樹，以及 VERIFIED / NOT VERIFIED / INCONCLUSIVE 判定。將宣告定義為視覺等效性。處理方式必須在每個評分標準行上與基準相符。此專化在相符時返回 VERIFIED，在任何一行失敗時返回 NOT VERIFIED，在無法擷取可比較的處理方式時返回 INCONCLUSIVE。畫素機器差異比對為選配；結構化的並排比較為必要條件。

<a id="artifacts"></a>

## 產物

```
/tmp/build-figma/<slug>/
├── intake.md
├── claim.md
├── baseline/        # Figma screenshots + assets
├── treatment/       # live UI captures
├── diff/            # optional composites
├── timeline.md
└── verdict.md
```

<a id="rubric-any-fail--not-verified"></a>

## 評分標準（任何 Fail 即為 NOT VERIFIED）

0. **主題極性。** 在檢查其他行之前，實際介面必須與 Figma 的淺色或深色模式相符。極性錯誤：切換應用程式主題並重新擷取。不要將反轉的按鈕顏色視為原始缺陷。
1. **結構。** 與 Figma 具有相同的區域；沒有遺漏或額外的外框。
2. **原始元件。** 來自接收階段的對應元件；沒有自創的品牌標誌；圖示與 Figma 相符，且存在於儲存庫的圖示集中。
3. **字型。** 大小與行高與接收階段的度量表（或固定值）相差約 1px 以內。
4. **間距。** 內邊距、間隙與控制項高度與接收階段的度量相符。
5. **資產。** 使用 Figma 或儲存庫的匯出檔，透過版面設定調整大小；沒有自然尺寸膨脹或預先縮小的圖片。
6. **控制項。** 核取方塊、按鈕與關閉操作介面相符；沒有猜測的圖示名稱。

Figma 佔位符數量與實際資料的差異，除非需要測試資料，否則不視為 Fail。

使用 `control-ui` 或儲存庫自有的控制 skill 來驅動介面。在 VERIFIED 之後，如果儲存庫有設計潤飾或 QA skill，請執行該 skill。

在 NOT VERIFIED 時，修正失敗的行，重新擷取，並再次套用評分標準。重複此過程直到 VERIFIED 或 INCONCLUSIVE。如果在使用控制 skill 的檔案化設定後仍無法擷取實際 UI，請以 INCONCLUSIVE 停止。在 `verdict.md` 中記錄嘗試的擷取與阻礙因素；不要宣稱 Gate 4 已完成。

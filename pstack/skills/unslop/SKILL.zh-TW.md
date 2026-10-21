---
name: unslop
description: "從任何文字中去除 AI 痕跡。"
disable-model-invocation: true
---

<a id="unslop"></a>

# 去除 AI 痕跡

編輯文字以移除 AI 模式。掃描以下模式，然後重新撰寫。保留意義並符合目標語氣。

<a id="patterns-to-detect-and-fix"></a>

## 要偵測與修正的模式

規則編號是其他技能引用時使用的穩定 ID。移除的規則會留下空缺。

<a id="content"></a>

### 內容

3. **表面的 -ing 片語。** "highlighting..."、"ensuring..."、"reflecting..."、"showcasing..."、"fostering..."。刪除或以真實來源補充說明。
5. **模糊的歸因。** "Experts believe"、"Industry reports suggest"、"Some critics argue"。指名來源或刪除。

<a id="language"></a>

### 語言

7. **AI 詞彙。** Additionally、crucial、delve、enduring、enhance、fostering、garner、interplay、intricate、landscape（抽象用法）、pivotal、showcase、tapestry（抽象用法）、testament、underscore、vibrant。改用平實的詞。
8. **「是」的華麗替代。** "serves as"、"stands as"、"boasts"、"features"。直接說 "is" 或 "has"。
9. **「不只是 X，而是 Y」。** 直接陳述重點。
10. **三點法則。** 不要強迫想法分成三項。使用自然的數量。
11. **同義詞輪換。** Protagonist、main character、central figure、hero 在同一段落中交替使用。選一個，並重複使用。
12. **假範圍。** "from X to Y" 但 X 和 Y 不在有意義的尺度上。直接列出主題。

<a id="style"></a>

### 風格

13. **破折號過度使用。** 完全避免破折號。只使用句號或逗號（不使用括號、連線號、或以連字號代替破折號）。如果想法需要分隔，結束句子或使用逗號。
14. **冒號過度使用。** 冒號在列表或例子之前是適當的，但不是作為句子中間的連線詞。"If you're coming from traditional automation: instead of registering event handlers, you describe conditions" 加上冒號沒有增加任何資訊。重新撰寫讓重點獨立呈現，不使用比較框架。"Describing when the scheduler should fire works best as plain English." 意義相同，沒有輔助性標點。
15. **粗體過度使用。** 不要將每個專有名詞或縮寫都加粗。
16. **行內標題列表。** 特徵是粗體標籤和冒號重述該行內容："**Performance:** Performance improved..."。將這些轉為散文。以句號結束的粗體開頭、指名專案、並接著真正的新細節（"**Schema in TypeScript.** Tables live in one file."）是適當的，不是特徵。
17. **標題使用句首大寫。** 使用句子式大小寫。
18. **裝飾性表情符號。** 從標題和列表專案中移除。
19. **彎曲引號。** 使用直引號。

<a id="communication-artifacts"></a>

### 溝通痕跡

20. **聊天機器人語句。** "I hope this helps!"、"Let me know if..."、"Of course!"、"Certainly!"、"Found the smoking gun!" 移除。
22. **迎合語氣。** "Great question! You're absolutely right!" 直接回應。

<a id="filler"></a>

### 填充詞

23. **填充片語。** "In order to" 改成 "To"。"Due to the fact that" 改成 "Because"。刪除 "It is important to note that"。
24. **過度含糊。** "could potentially possibly be argued that it might" 改成 "may"。
25. **籠統結論。** "The future looks bright." 陳述具體計畫或事實。

<a id="jargon"></a>

### 術語

26. **抽象比喻名詞。** Substrate（基礎）、wedge、wedge in（加入）、vector（方式、方法）、locus、vantage、nexus、primitive 作為名詞、harness 作為比喻、surface 如 "API surface"、bedrock、scaffolding 作為比喻、modality、paradigm、gold-plating（超出工作所需）、ratchet 作為比喻（機制實際名稱，或只會收緊的限制）、evacuate 用於移動程式碼（移出）、endgame（最後階段）、north star、flywheel。這些讀起來像技術詞，但通常有更平實具體的詞。選擇具體的詞。

<a id="plain-speech"></a>

### 平實語言

27. **說它做什麼，而不是它感覺如何。** "the database stays close at hand"、"SQL you can read"、"types that follow your schema" 描述感覺。修正方式是指名機制或數字："`.toSQL()` 回傳實際送到資料庫的字串"、"欄位改名會導致構建失敗"。問這個句子告訴讀者要做什麼或知道什麼，然後寫出來。如果無法以具體指令、事實或數字重新陳述，就刪掉。另一個檢查：如果句子可以不改動出現在另一個專案的檔案中，它就沒有說明這個專案的任何資訊。刪掉。
28. **縮短或拆分密集句子。** 如果讀者需要回溯才能理解句子，將其分成兩句或刪除子句。每個句子一個想法。
29. **主動語態。** 優先使用。找出 "is/are/was/were + 過去分詞" 並指名動作者："queries are validated" 改成 "the compiler validates queries"，"the file is parsed by the loader" 改成 "the loader parses the file"。只有在動作者未知或真的不重要時才使用被動語態。
30. **刪除副詞，或使用更強的動詞。** "runs quickly" 改成 "is fast" 或數字，"significantly improves" 改成測得的差異。副詞撐住弱動詞表示動詞選錯了。
31. **偏好平實詞。** "utilize" 和 "leverage" 改成 "use"，"facilitate" 改成 "help"，"numerous" 改成 "many"，"in the event that" 改成 "if"。較華麗的同義詞很少更清楚。
32. **矯揉造作的散文。** 有平實片語存在時使用比喻或修飾：格言（"wire it or delete it"）、為了效果而用的修辭片段、擬人化的程式碼（"the plan holds it"）、比喻動詞（"rides along"、"stands on"）、慣用框架片語。"A dial worth turning" 改成 "a parameter worth varying"。說你真正想表達的。規則 26 涵蓋比喻名詞。
33. **過度壓縮。** 省略冠詞、無動詞片段、符號語言、以及讓讀者需要解碼而非閱讀的縮寫。"Parser rejects bad date → exit 2, no write" 改成 "The parser rejects a bad date, exits with code 2, and writes nothing." 寫完整的句子並包含冠詞和動詞，並將箭頭和縮寫完整寫出。

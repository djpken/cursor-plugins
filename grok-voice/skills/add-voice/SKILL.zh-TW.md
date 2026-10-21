---
name: add-voice
description: >-
  當使用者執行 /add-voice、輸入 Voice Mode，或要求新增 Grok 時使用
  將即時語音加入應用程式，包括取代 STT-LLM-TTS 串接或
  OpenAI Realtime。 連線語音對語音、安全驗證以及應用程式麥克風。Composer：波形按鈕，
  麥克風圖示保留給聽寫。僅用於麥克風轉文字時使用 /add-dictation；要朗讀文字回覆時使用
  /add-read-aloud. 若要新增除錯記錄並從記錄中修正，請使用 /debug-voice.
---

<a id="add-voice"></a>

# 新增語音

將 Grok Speech to Speech 加入現有應用程式。在 `/add-voice` 上執行，輸入 **Voice Mode**，或明確的「新增 Grok 語音」意圖。

<a id="goal"></a>

## 目標

運作中的雙工路徑：使用者應用程式麥克風輸入、音訊輸出、`wss://api.x.ai/v1/realtime?model=grok-voice-latest`、安全驗證。Cursor 沒有原生麥克風；請連線**應用程式**（或範例客戶端），而非 IDE。

<a id="protocol-first"></a>

## 先定義協定

語言無關的事件迴圈。**預設為 TypeScript 範例。** 僅在客戶端 API 不同時提供簡短的 Python 對應版本（例如 `ws` 與 `websockets`）。

<a id="docs"></a>

## 檔案

- https://docs.x.ai/developers/model-capabilities/audio/speech-to-speech
- https://docs.x.ai/developers/model-capabilities/audio/ephemeral-tokens
- 定價（僅引用檔案）：https://docs.x.ai/developers/pricing（約 $0.08/分鐘 STS + $0.004/文字專案；最長會話 120 分鐘）

<a id="steps"></a>

## 步驟

1. **對映應用程式**
   - 技術棧：無、OpenAI Realtime、STT→LLM→TTS 串接、僅 TTS/STT。
   - 客戶端：web / Node / iOS / Android / 伺服器。
   - 如果存在串接或 OpenAI Realtime：用下方的單一雙工迴圈取代它（URL、模型、語音、事件差異）；僅當產品仍需要在 agent 之外進行一次性聆聽或朗讀時，才保留獨立的 `/v1/stt` 或 `/v1/tts`。

2. **驗證**
   - 伺服器：Bearer `XAI_API_KEY`。
   - 瀏覽器/行動裝置：後端 `POST https://api.x.ai/v1/realtime/client_secrets`，客戶端使用暫時性 token（Bearer 或瀏覽器 `sec-websocket-protocol`: `xai-client-secret.<token>`）。
   - 切勿將長期有效的金鑰放入客戶端套件中。不要在聊天中貼上金鑰。

3. **連線 + 會話**
   - URL: `wss://api.x.ai/v1/realtime?model=grok-voice-latest`
   - 開啟時：使用 `voice`（預設 `eve`）、`instructions`、`turn_detection: { type: "server_vad" }`（或 `null` 用於按下說話）的 `session.update`，除非應用程式已標準化在其他地方，否則使用 PCM 24 kHz。
   - 設定 `audio.input.transcription.model: "grok-transcribe"` 或不會收到使用者文字記錄（`conversation.item.input_audio_transcription.updated` 是累積的，不是增量）。
   - 如需工具：`web_search`、`x_search`、`file_search`、`mcp`、自訂 `function`。

4. **音訊 I/O（應用程式端）**
   - 每個會話使用一個 `AudioContext` 進行擷取和播放，在使用者手勢內建立（自動播放政策）。要求 24 kHz；如果瀏覽器提供其他速率，請在傳送前重新取樣。
   - 麥克風 → AudioWorklet 以約 100 ms 的區塊 → `input_audio_buffer.append`（或二進位傳輸）。並行啟動 WS 和麥克風；緩衝早期音訊，在開啟時重新整理。
   - 立即播放 `response.output_audio.delta`；使用約 150 ms 的提前量排程，使區塊緊密相連。在 `input_audio_buffer.speech_started` 時，停止所有排隊的內容（搶話）。
   - 文字記錄列：在 `input_audio_buffer.committed`（`item_id`）時建立使用者列，在 `…transcription.updated` 時填充；助理文字來自 `response.output_audio_transcript.delta` / `.done`，在 `response.done` 時結束回合。
   - 對於函式工具：`function_call_output`，完成播放，然後 `response.create`。

5. **Composer UI 慣例**
   - 一個主要按鈕，位於 composer 右側。空的 composer → **波形**圖示（描邊，例如 Phosphor `WaveformIcon weight="bold"`；切勿使用 `fill` 字重，因為它在 16 px 下會顯示為一團），啟動語音模式。存在任何文字 → 經典**傳送**箭頭；在語音模式下，該文字進入即時會話（`conversation.item.create` + `response.create`）。文字回覆串流 → 停止方塊。
   - 當語音處於即時狀態時，同一按鈕顯示**動畫波形**（4 條，約 3 px 寬，2 px 間距，約 16 px 高，最小縮放 0.4 以確保在 28 px 按鈕中清晰可讀），點選時結束會話。階段驅動動畫：聆聽時慢速，說話時快速，連線/思考時較慢且略微變暗（不透明度 ≥ 0.75）。遵守 `prefers-reduced-motion`。沒有 X 按鈕，沒有脈動圓環。
   - 狀態位於 **composer 內部，而非周圍**：佔位符顯示 `Connecting…` / `Listening…` / `Thinking…` / `Speaking…`，外加一個 `sr-only` `role="status"`。沒有獨立的狀態列。
   - **麥克風圖示保留給聽寫**（`/add-dictation`）。切勿用於語音模式。

6. **TS 骨架（預設）**

```ts
const url = "wss://api.x.ai/v1/realtime?model=grok-voice-latest";
// Node: pass Authorization header. Browser: use xai-client-secret.<token> protocol.
const ws = new WebSocket(url /* , { headers: { Authorization: `Bearer ${token}` } } */);

ws.addEventListener("open", () => {
  ws.send(JSON.stringify({
    type: "session.update",
    session: {
      voice: "eve",
      instructions: "You are a helpful voice agent.",
      turn_detection: { type: "server_vad" },
    },
  }));
});

ws.addEventListener("message", (ev) => {
  const event = JSON.parse(String(ev.data));
  if (event.type === "response.output_audio.delta") {
    // decode base64 PCM and play
  }
});
```

7. **Python 對應版本（僅當應用程式是 Python 時）**

```python
import json, os, websockets

url = "wss://api.x.ai/v1/realtime?model=grok-voice-latest"
headers = {"Authorization": f"Bearer {os.environ['XAI_API_KEY']}"}

async with websockets.connect(url, additional_headers=headers) as ws:
    await ws.send(json.dumps({
        "type": "session.update",
        "session": {
            "voice": "eve",
            "instructions": "You are a helpful voice agent.",
            "turn_detection": {"type": "server_vad"},
        },
    }))
    async for raw in ws:
        event = json.loads(raw)
        if event.get("type") == "response.output_audio.delta":
            pass  # decode and play
```

8. **儀器化（在首次人類測試之前）**
   - 執行 `/debug-voice`：它會提出計畫，然後安裝僅供開發使用的記錄匯出（`POST /api/voice/log` → `.voice-logs/<sessionId>.ndjson`，gitignored）、將音訊簡化為位元組數的客戶端記錄器，以及在應用程式自身語言中的 UI 會話 ID。
   - 相同的 skill 包含修正迴圈以及症狀 → 記錄特徵 → 修正表格。

9. **冒煙測試**
   - 透過 `conversation.item.create` + `response.create` 進行文字回合；確認音訊或文字記錄事件。
   - 確認客戶端中沒有長期有效的金鑰（grep 建置後的客戶端套件中的環境變數名稱和 `client_secrets`）。
   - 將應用程式交給使用者，並附上**耳機**。在喇叭上，麥克風會聽到回覆，模型會自我回答；那是回聲，不是迴圈中的錯誤。
   - 使用 `/debug-voice` 進行迭代。

<a id="out-of-scope"></a>

## 範圍之外

- 僅語音轉文字（`/add-dictation`），朗讀文字（`/add-read-aloud`）
- 影像生成和純文字推論
- 虛構的端點、事件或 CLI 旗標

---
name: add-read-aloud
description: >-
  當使用者執行 /add-read-aloud 或希望應用程式朗讀文字時使用
  Grok 文字轉語音：助理回覆上的朗讀按鈕、自動朗讀、TTS、
  語音輸出、旁白、IVR 提示、語音標籤、voice_id。若要建立雙向
  語音 agent，請使用 /add-voice.。若要使用語音轉文字，請使用 /add-dictation.
---

<a id="add-read-aloud"></a>

# 新增朗讀功能

將 Grok 文字轉語音加入現有應用程式：在助理回覆上加入喇叭按鈕、自動朗讀，或朗讀任何文字。在 `/add-read-aloud` 上執行，輸入 **Read aloud**，或明確表達「朗讀此內容」或「TTS」意圖。Cursor 沒有喇叭功能；請連線 **應用程式**，而非 IDE。

<a id="docs"></a>

## 檔案

- https://docs.x.ai/developers/model-capabilities/audio/text-to-speech
- API 參考：https://docs.x.ai/developers/rest-api-reference/inference/voice
- 自訂語音：https://docs.x.ai/developers/model-capabilities/audio/custom-voices
- 定價（僅引用檔案）：https://docs.x.ai/developers/pricing

<a id="pick-the-path"></a>

## 選擇路徑

| 需求 | 路徑 |
| --- | --- |
| 點選喇叭，聆聽完整回覆。朗讀頁面。產生檔案。 | **批次** `POST https://api.x.ai/v1/tts`（預設） |
| LLM 仍在串流時音訊即開始；中斷輸入；超過 15,000 個字元的文字 | **串流** `wss://api.x.ai/v1/tts` 透過後端中繼 |

批次是朗讀按鈕的預設選項：單一請求、單一 MP3、可快取，金鑰不會離開伺服器。僅當 UX 需要在文字完成前播放音訊時，才使用串流。`POST /v1/tts` 沒有檔案記載的串流旗標；請勿自行發明。

<a id="auth"></a>

## 驗證

- Bearer `XAI_API_KEY`，僅限伺服器端。TTS 檔案未記載臨時權杖流程，且瀏覽器無法設定 WebSocket 標頭，因此瀏覽器串流需透過您的後端中繼。
- 切勿將金鑰放入客戶端套件。請勿在聊天中貼上金鑰。

<a id="steps"></a>

## 步驟

1. **對應應用程式**
   - 助理訊息的渲染位置、每則訊息的操作位置（複製、重新產生）、回覆串流的結束方式、伺服器框架、套件管理器。
   - **喇叭圖示屬於朗讀功能**。波形是語音模式（`/add-voice`），麥克風是聽寫（`/add-dictation`）。在訊息操作列中放置一個幽靈喇叭按鈕；載入時顯示旋轉器，播放時顯示停止方塊。一次僅朗讀一段：開始新朗讀會停止目前朗讀。
   - 將操作列對齊至回覆的**文字邊緣**，而非按鈕的方框：圖示按鈕會將符號置中，因此如果助理氣泡沒有內距，請將該列向左移動該內距值（例如，24 px 按鈕中的 12–14 px 圖示使用 `-ml-[5px]`）。在瀏覽器中測量；`getBoundingClientRect` 上的 `<p>` 和 `<svg>` 應共享左邊緣。
   - 僅在回覆**完成串流**後才渲染按鈕；在即時訊息上，它會朗讀部分回覆。
   - 當螢幕上有許多訊息時，請將播放器狀態（活躍訊息 ID、`loading | playing`、最後錯誤）儲存在一個共享 store 中（`useSyncExternalStore`、signal、或應用程式使用的任何機制），以便每個按鈕都能反映狀態，且錯誤可顯示在應用程式現有的狀態區域。每個按鈕的 `let current` 是不夠的。
   - 自動朗讀：選用切換，預設為關閉，且僅在頁面上有使用者手勢後才啟用（自動播放政策）。切勿在載入時自動朗讀。
   - 如果已安裝 `/add-voice`，其 `AudioContext` 和 PCM 播放器可播放串流 TTS；請勿新增第二個音訊圖。

2. **準備文字**
   - 朗讀散文，而非標記。移除 markdown：標題 → 文字，`**bold**` → 文字，連結 → 連結文字，行內程式碼 → 程式碼，圍欄區塊 → `[pause] Code block omitted.`，表格 → 每行一句話或省略。保留標點符號；它控制節奏。
   - 中和回覆中出現的語音標籤（`[laugh]`、`<whisper>`…），以免模型文字影響朗讀方式。僅移除**檔案記載的標籤名稱**（見步驟 5 的清單），而非所有括號：`[1]` 引用和 `[note]` 必須保留。
   - 批次限制為**每個請求 15,000 個字元**。將較長文字依段落、然後句子、再依字元邊界分割，並依序播放各部分；在播放第 N 部分時取得第 N+1 部分，否則每個邊界都會有靜音間隙。或使用串流。
   - 依 `hash(text + voice_id + language + speed)` 快取；相同回覆常被重播。

3. **批次路徑（預設）**
   - 伺服器：您的路由接收 `{ text, voice_id?, language? }`，驗證每個專案的格式（`voice_id` `^[a-z0-9-]{1,64}$`、`language` BCP-47 或 `auto`），轉送 JSON，並使用上游的 `Content-Type` 和 `Cache-Control: no-store` 將主體串流回傳。將上游 404 對應至「未知語音」，以便客戶端收到可讀的錯誤。預設輸出為 24 kHz / 128 kbps 的 MP3，可在瀏覽器中任何地方播放。
   - `language`：聊天應用程式預設為 `"auto"`，因為回覆會跟隨使用者的語言；僅在固定語言的產品中固定 `"en"` 等。

```ts
// server (any runtime with fetch)
export async function speak(text: string, voice_id = "eve", language = "auto") {
  if (!text.trim() || text.length > 15_000) throw new Error("TTS text must be 1–15,000 chars");
  const res = await fetch("https://api.x.ai/v1/tts", {
    method: "POST",
    headers: { Authorization: `Bearer ${process.env.XAI_API_KEY}`, "Content-Type": "application/json" },
    body: JSON.stringify({
      text,
      voice_id,
      language,                       // required: `auto` or BCP-47 (`en`, `pt-BR`); omitting it → 422
      // output_format: { codec: "mp3", sample_rate: 24000, bit_rate: 128000 }, // default
      // speed: 1.0,                  // 0.7–1.5
      // text_normalization: true,    // "$5" → "five dollars"
      // replace: { nginx: "/ˈɛndʒɪn ˈɛks/" },
    }),
  });
  if (!res.ok) throw new Error(`TTS ${res.status}`); // 400 bad text/format, 401 key, 404 unknown voice_id, 422 missing required field (e.g. language), 429/500/503 back off and retry
  return new Response(res.body, { headers: { "Content-Type": res.headers.get("content-type") ?? "audio/mpeg" } });
}
```

```ts
// client
let current: HTMLAudioElement | null = null;
async function readAloud(text: string, voiceId = "eve") {
  current?.pause(); current = null;                       // one utterance at a time
  const res = await fetch("/api/tts", { method: "POST", headers: { "Content-Type": "application/json" }, body: JSON.stringify({ text, voice_id: voiceId }) });
  if (!res.ok) throw new Error("TTS request failed");
  const url = URL.createObjectURL(await res.blob());
  const audio = new Audio(url);
  audio.addEventListener("ended", () => URL.revokeObjectURL(url)); // avoid blob leaks
  current = audio;
  await audio.play();                                      // call from the click handler’s promise chain
}
function stop() { current?.pause(); current = null; }
```

   - Safari：`audio.duration` 在 blob URL 上是 `Infinity`。如果需要進度條，請使用 `AudioContext.decodeAudioData(buf.slice(0))` 解碼，或請求 `with_timestamps: true` 並從 JSON 封包中讀取 `duration`（音訊則以 base64 形式存在於 `audio` 中）。
   - Safari 會永久暫停在手勢外建立的 `AudioContext`。請在點選處理器中同步建立它，在任何 `await` 之前。

4. **串流路徑**
   - 中繼：伺服器持有金鑰，升級瀏覽器 socket，建立查詢字串，將客戶端 JSON 向上轉送，將伺服器 JSON 向下轉送。為瀏覽器請求 `codec=pcm`：原始 PCM16 區塊可在到達時排程，而 MP3 區塊無法在沒有 `MediaSource` 的情況下逐段解碼。

```ts
import { WebSocketServer, WebSocket } from "ws";

new WebSocketServer({ port: 8789 }).on("connection", (client) => {
  const q = new URLSearchParams({ language: "en", voice: "eve", codec: "pcm", sample_rate: "24000" /* optimize_streaming_latency: "1" */ });
  const up = new WebSocket(`wss://api.x.ai/v1/tts?${q}`, { headers: { Authorization: `Bearer ${process.env.XAI_API_KEY}` } });
  up.on("message", (d) => client.send(d.toString()));                                   // audio.delta, audio.done, audio.clear, session.updated, error
  client.on("message", (d) => up.readyState === WebSocket.OPEN && up.send(d.toString())); // text.delta, text.done, text.clear, session.update
  const end = () => { client.close(); up.close(); };
  up.on("close", end); up.on("error", end); client.on("close", end);
});
```

   - 客戶端：每個聊天 session 一個 socket（它在朗讀之間保持開啟；每個 team 50 個並行 session，permit TTL 600 秒，因此在關閉時重新連線）。將 LLM tokens 作為 `text.delta` 轉送（每個 ≤ 15,000 字元），在回覆完成時傳送 `text.done`，在停止或新回覆開始時傳送 `text.clear`；在 `audio.clear` 時丟棄佇列中的音訊。

```ts
const ws = new WebSocket(relayUrl);
const ctx = new AudioContext({ sampleRate: 24000 }); // create in the click handler; resume if suspended
let playhead = 0, sources: AudioBufferSourceNode[] = [];

ws.addEventListener("message", (e) => {
  const ev = JSON.parse(e.data);
  if (ev.type === "audio.delta") {
    const bytes = Uint8Array.from(atob(ev.delta), (c) => c.charCodeAt(0));
    const pcm = new Int16Array(bytes.buffer, 0, bytes.byteLength >> 1);
    const buf = ctx.createBuffer(1, pcm.length, 24000);
    const ch = buf.getChannelData(0);
    for (let i = 0; i < pcm.length; i++) ch[i] = pcm[i] / 32768;
    const src = ctx.createBufferSource(); src.buffer = buf; src.connect(ctx.destination);
    playhead = Math.max(playhead, ctx.currentTime + 0.15);   // ~150 ms lead so chunks butt together
    src.start(playhead); playhead += buf.duration; sources.push(src);
  } else if (ev.type === "audio.done") { /* utterance finished; socket stays open */ }
  else if (ev.type === "audio.clear") { sources.forEach((s) => s.stop()); sources = []; playhead = 0; }
  else if (ev.type === "error") showError(ev.message);
});
// on each LLM token:  ws.send(JSON.stringify({ type: "text.delta", delta: token }))
// on reply finished:  ws.send(JSON.stringify({ type: "text.done" }))
// on stop / barge-in: ws.send(JSON.stringify({ type: "text.clear" }))  → wait for audio.clear before the next text.delta
```

   - 跨 `text.delta` 邊界分割的字元沒問題；比對和合成會跨 deltas 執行。

5. **選項**（批次的 JSON 欄位，串流的查詢引數）

| 需求 | 設定 |
| --- | --- |
| 不同語音 | `voice_id`（批次）/ `voice`（串流）。來自 `GET /v1/tts/voices` 的內建語音：`eve`（預設）、`ara`、`rex`、`leo`、`luna`、`atlas`、`aurora`、`orion`、… 共 28 種，全部多語言，不區分大小寫。自訂語音：來自主控臺或 `GET /v1/custom-voices` 的 8 字元 ID |
| 非英文或混合 | `language`：`en`、`ar-EG`、`ar-SA`、`ar-AE`、`bn`、`zh`、`fr`、`de`、`hi`、`id`、`it`、`ja`、`ko`、`pt-BR`、`pt-PT`、`ru`、`es-MX`、`es-ES`、`tr`、`vi`，或 `auto` |
| 更快或更慢 | `speed` 0.7–1.5 |
| 「$5」、「Dr.」、「3/4」朗讀為文字 | `text_normalization: true` |
| 品牌名稱、縮寫、術語 | `replace: { "Acme Mobile": "Acme Mobull", "nginx": "/ˈɛndʒɪn ˈɛks/" }`；≤200 個專案，金鑰 ≤100 字元（字母、數字、撇號、空格），值 ≤128；整字、不區分大小寫、最長比對優先。串流：在第一個 `text.delta` 之前使用 `session.update { replace }` |
| 富有表現力的語音 | 內嵌 `[pause]`、`[long-pause]`、`[laugh]`、`[chuckle]`、`[giggle]`、`[cry]`、`[sigh]`、`[breath]`、`[inhale]`、`[exhale]`、`[tsk]`、`[tongue-click]`、`[lip-smack]`、`[hum-tune]`。將 `<whisper>`、`<soft>`、`<loud>`、`<emphasis>`、`<build-intensity>`、`<decrease-intensity>`、`<slow>`、`<fast>`、`<higher-pitch>`、`<lower-pitch>`、`<singing>`、`<sing-song>` 包裹整個短語 |
| 字幕、卡拉 OK、唇形同步 | `with_timestamps: true` → JSON `{ audio (base64), content_type, duration, audio_timestamps: { graph_chars[], graph_times[][start,end] } }`；依序處理 `graph_chars`，切勿依索引切割輸入 |
| 更早取得第一段音訊（串流） | `optimize_streaming_latency=1`（檔案也列出 `2`；API 參考列出 `0`/`1`） |
| 電話 / IVR | `output_format: { codec: "mulaw" \| "alaw", sample_rate: 8000 }`；無法在瀏覽器中播放 |
| 編輯、後製 | `codec: "wav"`、`sample_rate: 44100` 或 `48000` |
| 較小的檔案 | `codec: "mp3"`、`bit_rate: 64000` |

6. **Python 對應版本（僅限伺服器為 Python 時）**

```python
import os, requests
r = requests.post(
    "https://api.x.ai/v1/tts",
    headers={"Authorization": f"Bearer {os.environ['XAI_API_KEY']}"},
    json={"text": text, "voice_id": "eve", "language": "en"},
)
r.raise_for_status(); audio_bytes = r.content  # audio/mpeg
# streaming: websockets.connect(url, additional_headers={"Authorization": f"Bearer {key}"}); send {"type":"text.delta",...}, {"type":"text.done"}; read audio.delta / audio.done
```

7. **冒煙測試**
   - 批次：`curl -X POST https://api.x.ai/v1/tts -H "Authorization: Bearer $XAI_API_KEY" -H "Content-Type: application/json" -d '{"text":"Hello from read aloud.","voice_id":"eve","language":"en"}' --output /tmp/hello.mp3` → 200 `audio/mpeg`（MP3、24 kHz、128 kbps、單聲道），可播放。省略 `language` → 422（實際觀察結果；檔案表格僅列出 400）。`voice_id: "nope"` → 404。
   - 在應用程式中：回覆串流期間不顯示喇叭圖示；回覆結束後才會出現。點選 → 旋轉指示器 → 停止方塊 → 音訊結束後回到喇叭圖示；播放中再次點選 → 立即停止；在第一段播放時點選第二段回覆 → 第一段停止，第二段播放。Safari：首次播放從點選時開始，`URL.revokeObjectURL` 在 `ended` 時觸發。
   - 文字預處理：包含圍欄程式碼區塊、表格、`[1]` 引用以及一個多餘的 `[laugh]` 的回覆 → 朗讀為「程式碼區塊已省略」，每行一句話，引用保持完整，標籤已移除。請為此撰寫單元測試；這是純函式。
   - 串流：逐個 token 傳送兩句話的回覆；音訊應在 `text.done` 之前開始。在語句中途傳送 `text.clear` → `audio.clear`，播放停止且無殘留內容。在同一 socket 上傳送第二句語句 → 全新的 `audio.delta`，無來自第一句的幹擾。
   - 在客戶端 bundle 中搜尋 `XAI_API_KEY`；它不應存在。針對執行中的開發伺服器，fetch `/`，收集其參考的 `/_next/static/chunks/*.js`（或等效）URL，並逐一 grep；不要依賴您尚未建立的生產環境建置。
   - 使用 `/debug-voice` 從紀錄進行除錯；將其掛鉤點替換為 `audio.delta`（位元組數）、`audio.done`、`audio.clear`、`error`。

<a id="out-of-scope"></a>

## 範圍之外

- 能聆聽並回答的語音 agent（`/add-voice`）、語音轉文字（`/add-dictation`）
- 超越傳遞現有自訂 `voice_id` 的語音複製
- 發明 TTS token 流程、`POST /v1/tts` 上的 `stream` 旗標，或檔案中未列出的事件名稱

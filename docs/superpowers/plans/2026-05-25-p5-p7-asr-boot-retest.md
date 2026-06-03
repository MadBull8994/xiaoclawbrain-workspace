# P5/P7 ASR Boot Retest Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Verify the real end-to-end result of `BOOT -> Opus upload -> backend ASR` after the oMLX ASR model fix, and only make the smallest targeted code change if the retest still fails.

**Architecture:** The ESP32 thin client starts and stops manual listening with BOOT, uploads Opus frames over WebSocket binary, and waits in `recognizing`. The Python backend receives `listen start/stop`, buffers binary frames, calls the non-stream ASR provider, and should return either valid STT text or a real `ASR_EMPTY` instead of a configuration or routing failure.

**Tech Stack:** ESP-IDF C++, Python backend, WebSocket JSON + binary frames, oMLX OpenAI-compatible ASR API, Docker container logs.

---

### Task 1: Reproduce The Current Retest Goal

**Files:**
- Inspect: `项目开发进度_XiaoClawBrain.md`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/config.yaml`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/config/config_loader.py`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/core/handle/textHandler/listenMessageHandler.py`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/core/connection.py`
- Inspect: `xiaoclaw-ghproxycom/main/boards/openclaw-v2.7/openclaw_v2_7_board.cc`
- Inspect: `xiaoclaw-ghproxycom/main/xiaoclaw_ws_client.cc`
- Inspect: `xiaoclaw-ghproxycom/main/application.cc`

- [ ] **Step 1: Confirm the goal and do not expand scope**

Only work on this:

```text
BOOT press -> listen start -> Opus upload -> listen stop -> backend ASR result
```

Do not work on:

```text
P6 cleanup
P8 Skill runtime
P9 wake word
listening/uploading_audio jitter unless it directly blocks ASR result
```

- [ ] **Step 2: Confirm the fixed backend ASR config is still present**

Expected backend config:

```yaml
ASR:
  OMLXASR:
    type: openai
    api_key: local-omlx
    base_url: http://host.docker.internal:8009/v1/audio/transcriptions
    model_name: Qwen3-ASR-1.7B-8bit
```

Source references:

```text
xiaozhi-esp32-server/main/xiaozhi-server/config.yaml
xiaozhi-esp32-server/main/xiaozhi-server/config/config_loader.py
```

- [ ] **Step 3: Confirm the exact backend hooks that must fire during BOOT retest**

Expected backend path:

```python
if msg_json["state"] == "start":
    conn.reset_audio_states()
elif msg_json["state"] == "stop":
    conn.client_voice_stop = True
    if len(conn.asr_audio) > 0:
        asr_audio_task = conn.asr_audio.copy()
        conn.reset_audio_states()
        await conn.asr.handle_voice_stop(conn, asr_audio_task)
```

And binary frames must be queued here:

```python
elif isinstance(message, bytes):
    self.asr_audio_queue.put(message)
```

- [ ] **Step 4: Confirm the exact ESP32 hooks that must fire during BOOT retest**

Expected BOOT path:

```cpp
boot_button_.OnPressDown([this]() {
    app.SetDeviceState(kDeviceStateWakeupDetected);
    app.SetDeviceState(kDeviceStateListening);
    app.XiaoClawSendListenStart();
    app.XiaoClawStartAudioUpload();
});

boot_button_.OnPressUp([this]() {
    esp_timer_start_once(upload_flush_timer_, 1500000);
});
```

Expected delayed stop:

```cpp
if (state == kDeviceStateListening || state == kDeviceStateUploadingAudio) {
    app.XiaoClawStopAudioUpload();
    app.XiaoClawSendListenStop();
    app.SetDeviceState(kDeviceStateRecognizing);
}
```

### Task 2: Run The Retest And Collect Only The Decisive Evidence

**Files:**
- Inspect: `项目开发进度_XiaoClawBrain.md`
- Inspect if needed: `开发问题解决记录.md`

- [ ] **Step 1: Start with a backend log watch**

Run a backend log command appropriate for the current setup and capture these markers only:

```text
[LISTEN] START
[LISTEN] STOP
[WS_BINARY] FIRST frame
[ASR_DIAG] manual stop captured frames=
ASR triggering manually:
语音识别耗时:
语音识别失败:
ASR 空识别，返回 ASR_EMPTY
```

- [ ] **Step 2: Run one BOOT retest on hardware**

Expected sequence on ESP32:

```text
DIAG_BOOT_PRESS
P5.2 BOOT listen start, audio upload enabled
FIRST opus frame uploaded
P5.2 BOOT released, upload flush scheduled in 1.5s
P5.2 BOOT delayed listen stop completed
recognizing timeout started (10s)
```

Expected sequence on backend:

```text
[LISTEN] START
[WS_BINARY] FIRST frame
[LISTEN] STOP
[ASR_DIAG] manual stop captured frames=...
ASR triggering manually: frames=...
```

- [ ] **Step 3: Classify the result into exactly one bucket**

Bucket A:

```text
Backend returns real recognized text
```

Bucket B:

```text
Backend returns real ASR_EMPTY without upstream HTTP/provider error
```

Bucket C:

```text
Backend still fails before recognition, such as 404/401/5xx/parse error/request mismatch
```

### Task 3: If Bucket C Happens, Instrument The ASR Provider Minimally

**Files:**
- Modify: `xiaozhi-esp32-server/main/xiaozhi-server/core/providers/asr/openai.py`

- [ ] **Step 1: Add sanitized diagnostics to the ASR HTTP failure path**

Use this minimal shape and keep it secret-safe:

```python
response = requests.post(
    self.api_url,
    files=files,
    data=data,
    headers=headers,
    timeout=5,
)

logger.bind(tag=TAG).info(
    "ASR request sent url=%s model=%s status=%s",
    self.api_url,
    self.model,
    response.status_code,
)

if response.status_code != 200:
    preview = sanitize_text((response.text or "")[:200])
    raise Exception(f"API请求失败: {response.status_code} body={preview}")
```

Rules:

```text
Do not log full Authorization
Do not log full response body
Do not add unrelated refactors
```

- [ ] **Step 2: Re-run one BOOT retest after the instrumentation**

Now determine whether the real problem is:

```text
wrong URL
wrong model field
wrong multipart form name
timeout
oMLX endpoint compatibility mismatch
```

### Task 4: If Bucket A Or B Happens, Verify The Result Reaches ESP32 Correctly

**Files:**
- Inspect: `xiaoclaw-ghproxycom/main/xiaoclaw_ws_client.cc`
- Inspect: `xiaoclaw-ghproxycom/main/application.cc`

- [ ] **Step 1: Confirm STT and state messages are accepted**

Relevant code:

```cpp
if (msg_type == "state") {
    HandleStateMessage(root);
} else if (msg_type == "stt") {
    HandleSttMessage(root);
}
```

And:

```cpp
if (strcmp(state, "recognizing") == 0) {
    SetDeviceState(kDeviceStateRecognizing);
}
```

- [ ] **Step 2: Confirm the 10-second recognizing timeout is not masking a late backend result**

Relevant timeout:

```cpp
void Application::StartRecognizingTimeout() {
    esp_timer_start_once(xiaoclaw_recognizing_timeout_timer_, 10000000);
}
```

If backend ASR is now healthy but the board still times out before the result arrives, only then consider a tiny follow-up change to align the timeout with the documented backend round timing. Do not change this in the same pass unless it is proven to be the blocker.

### Task 5: Update Project Records After The Retest

**Files:**
- Modify: `项目开发进度_XiaoClawBrain.md`
- Modify if a new root-caused issue was fixed: `开发问题解决记录.md`

- [ ] **Step 1: Add one newest-first progress entry**

Record:

```text
test date
whether BOOT produced valid text / real ASR_EMPTY / upstream error
whether oMLX loaded Qwen3-ASR-1.7B-8bit
whether ESP32 received the result before recognizing timeout
next single task
```

- [ ] **Step 2: Only update the problem record if a new issue was actually solved**

Good examples:

```text
fixed oMLX multipart mismatch
fixed wrong ASR endpoint path
fixed recognizing timeout blocking result display
```

Not a reason to update it:

```text
mere retest with no new fix
```

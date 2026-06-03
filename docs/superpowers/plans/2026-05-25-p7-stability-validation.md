# P7 Stability Validation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Validate that the already-working `BOOT -> ASR -> LLM -> TTS -> playback` main path is stable across repeated conversations and key failure scenarios, and make only the smallest fixes needed to guarantee recovery back to `idle` or `reconnecting`.

**Architecture:** The ESP32 thin client drives BOOT-triggered listen start/stop, uploads Opus over WebSocket binary, and receives state plus TTS audio back from the Python backend. P7 is not about new features; it is about proving the current path survives repeated use, empty ASR, disconnects, TTS failures, and timeout conditions without getting stuck.

**Tech Stack:** ESP-IDF C++, Python backend, Docker, WebSocket JSON + binary Opus, oMLX/OpenAI-compatible ASR, DeepSeek LLM, streaming TTS.

---

### Task 1: Lock Scope And Baseline

**Files:**
- Inspect: `START_HERE_项目速览.md`
- Inspect: `CLAUDE.md`
- Inspect: `项目开发进度_XiaoClawBrain.md`
- Inspect: `项目开发路线图_XiaoClawBrain.md`

- [ ] **Step 1: Confirm the baseline**

Treat this as already proven:

```text
BOOT -> listen start -> Opus upload -> ASR -> LLM -> TTS -> binary audio downlink
```

Confirmed from progress:

```text
ASR text: "你好，你是谁？"
LLM: success
TTS: three segments generated and sent to ESP32
```

- [ ] **Step 2: Do not expand scope**

Do not work on:

```text
P6 cleanup
P8 Skill runtime
P9 wake word
large refactors
cross-end protocol redesign
```

Only work on:

```text
P7 repeated conversation and failure recovery
```

### Task 2: Prepare The Exact Observation Points

**Files:**
- Inspect: `xiaoclaw-ghproxycom/main/boards/openclaw-v2.7/openclaw_v2_7_board.cc`
- Inspect: `xiaoclaw-ghproxycom/main/xiaoclaw_ws_client.cc`
- Inspect: `xiaoclaw-ghproxycom/main/application.cc`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/core/handle/textHandler/listenMessageHandler.py`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/core/providers/asr/base.py`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/core/handle/textHandler/pingMessageHandler.py`
- Inspect: `xiaozhi-esp32-server/main/xiaozhi-server/core/protocol.py`

- [ ] **Step 1: Confirm the main success path hooks**

ESP32 side:

```cpp
boot_button_.OnPressDown(...)
boot_button_.OnPressUp(...)
SendListenStartJson()
SendListenStopJson()
SendOpusFrame(...)
OnTtsStart(...)
OnTtsEnd(...)
```

Backend side:

```python
[LISTEN] START
[LISTEN] STOP
ASR triggering manually
startToChat(...)
ASR 空识别，返回 ASR_EMPTY
```

- [ ] **Step 2: Confirm the recovery hooks**

Relevant ESP32 behavior:

```cpp
OnDisconnected -> SetDeviceState(kDeviceStateReconnecting)
OnConnected -> SetDeviceState(kDeviceStateIdle)
OnError -> SetDeviceState(kDeviceStateError)
recognizing timeout -> idle
tts_end -> idle
```

Relevant backend behavior:

```python
ErrorCode.ASR_EMPTY
ErrorCode.LLM_TIMEOUT
ErrorCode.TTS_TIMEOUT
ErrorCode.ROUND_TIMEOUT
ping -> pong
```

### Task 3: Run The Continuous Conversation Test First

**Files:**
- Modify if needed later: `项目开发进度_XiaoClawBrain.md`

- [ ] **Step 1: Execute 10 consecutive BOOT conversations**

Run ten rounds with short natural prompts such as:

```text
1. 你好，你是谁
2. 现在几点
3. 讲一句自我介绍
4. 说一句短笑话
5. 你能听到我吗
6. 用一句话回答我
7. 我现在在测试你
8. 继续说一句话
9. 介绍一下自己
10. 再见
```

- [ ] **Step 2: For each round, record only the decisive fields**

Per round capture:

```text
round number
BOOT accepted or ignored
ASR text or ASR_EMPTY
whether LLM replied
whether TTS binary arrived
whether device returned to idle
whether audio actually played on speaker
```

- [ ] **Step 3: Stop immediately if any round gets stuck**

Stuck means:

```text
device remains in recognizing
device remains in error
device remains in reconnecting after backend is healthy
backend never reaches ASR triggering manually
tts_end never arrives and state does not recover
```

### Task 4: Run The Failure Matrix In This Order

**Files:**
- Inspect first, modify only if a concrete bug is proven:
  - `xiaoclaw-ghproxycom/main/application.cc`
  - `xiaoclaw-ghproxycom/main/xiaoclaw_ws_client.cc`
  - `xiaozhi-esp32-server/main/xiaozhi-server/core/providers/asr/base.py`
  - `xiaozhi-esp32-server/main/xiaozhi-server/core/protocol.py`

- [ ] **Step 1: Verify ASR_EMPTY path**

How to trigger:

```text
Press BOOT and stay mostly silent, or speak too softly/too briefly.
```

Expected backend evidence:

```text
ASR 空识别，返回 ASR_EMPTY
```

Expected device behavior:

```text
receives error/stt handling as designed
does not enter LLM
returns to idle instead of hanging in recognizing
```

- [ ] **Step 2: Verify disconnect and auto-reconnect**

How to trigger:

```text
temporarily stop backend service or disconnect Wi-Fi/backend network path
wait for ESP32 to enter reconnecting
restore backend service
```

Expected device evidence:

```text
XiaoClaw WS disconnected
display/status -> reconnecting
reconnect scheduled in N ms
XiaoClaw WS connected
state returns to idle
```

Expected pass result:

```text
after reconnect, BOOT can start a new successful round
```

- [ ] **Step 3: Verify TTS failure recovery**

How to trigger:

```text
temporarily misconfigure or stop the active TTS backend only if this can be done safely and reverted quickly
```

Expected result:

```text
backend reports TTS failure or timeout
device does not stay stuck in synthesizing or speaking forever
system returns to idle or error, then can recover for the next round
```

- [ ] **Step 4: Verify round timeout / late result handling**

How to trigger:

```text
create a deliberately slow response path if needed
or observe whether long response latency causes recognizing timeout before result arrives
```

Known ESP32 behavior:

```cpp
if (state == kDeviceStateRecognizing) {
    ESP_LOGW(TAG, "recognizing timeout -> idle");
    app->SetDeviceState(kDeviceStateIdle);
}
```

Expected result:

```text
either the backend responds before timeout
or the device times out cleanly and remains usable for the next round
```

### Task 5: Only Fix Proven Bugs, One At A Time

**Files:**
- Modify the smallest relevant file only after reproducing a bug

- [ ] **Step 1: If the bug is "stuck in recognizing", inspect timeout ownership**

Check:

```text
whether STT/state message arrives but does not cancel or supersede recognizing timeout
whether timeout is too short for the proven healthy backend latency
```

Small allowed fixes:

```text
cancel timeout on first valid downstream state
increase timeout only if logs prove current 10s is too short
```

- [ ] **Step 2: If the bug is "stuck after TTS", inspect end-of-TTS state handling**

Check:

```cpp
OnTtsEnd -> SetDeviceState(kDeviceStateIdle)
```

Small allowed fixes:

```text
ensure both legacy and current tts end messages converge to idle
ensure binary playback completion does not leave speaking without end signal
```

- [ ] **Step 3: If the bug is "reconnect never recovers", inspect reconnect scheduling**

Check:

```cpp
OnDisconnected(...)
StartXiaoClawReconnectTimer()
OnConnected(...)
```

Small allowed fixes:

```text
preserve retry schedule
reset state cleanly on reconnect success
avoid BOOT remaining locked after reconnect
```

### Task 6: Verification Commands And Expected Evidence

**Files:**
- No code changes required for this task

- [ ] **Step 1: Use log watchers**

Backend log markers to watch:

```text
[LISTEN] START
[LISTEN] STOP
[WS_BINARY] FIRST frame
ASR triggering manually
ASR 空识别，返回 ASR_EMPTY
处理PING消息
TTS
timeout
error
```

ESP32 log markers to watch:

```text
DIAG_BOOT_PRESS
P5.2 BOOT listen start, audio upload enabled
FIRST opus frame uploaded
recognizing timeout started (10s)
recognizing timeout -> idle
XiaoClaw WS disconnected
XiaoClaw WS connected
XiaoClaw tts_start -> synthesizing
XiaoClaw tts_end -> idle
XiaoClaw server error -> error
```

- [ ] **Step 2: Rebuild only when code changed**

If backend Python changed:

```bash
cd /Volumes/软件/opencode/ESP32S3语音陪伴设备开发/xiaozhi-esp32-server/main/xiaozhi-server
python3 -m py_compile core/**/*.py
```

If ESP32 firmware changed:

```bash
cd /Volumes/软件/opencode/ESP32S3语音陪伴设备开发/xiaoclaw-ghproxycom
. /Users/mashiyue/esp/esp-idf-v5.5.2/export.sh && idf.py build
```

Expected:

```text
backend compile passes
or ESP32 build shows Project build complete
```

### Task 7: Update Records And Report The Single Next Blocker

**Files:**
- Modify: `项目开发进度_XiaoClawBrain.md`
- Modify if a new problem was truly solved: `开发问题解决记录.md`

- [ ] **Step 1: Update the progress document newest-first**

Include:

```text
test date
device/port used
10-round result summary
ASR_EMPTY test result
reconnect test result
TTS failure test result
whether any code was changed
current single blocker
next single task
```

- [ ] **Step 2: Update the problem record only for a real solved issue**

Examples:

```text
fixed recognizing timeout causing false idle before late STT
fixed reconnect state not unlocking BOOT after recovery
fixed TTS end signal mismatch leaving device in speaking
```

- [ ] **Step 3: Final output format**

Return the result in exactly this structure:

```text
1. Which P7 cases were tested
2. Which cases passed
3. Which case failed first
4. What evidence proves the failure
5. What code was changed, if any
6. What the single next blocker is
7. What the next single task is
```

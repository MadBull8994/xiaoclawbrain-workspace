# XiaoClawBrain Agent Guardrails

This file is project memory for Claude Code and a shared guardrail for coding agents.
It intentionally stays short. The canonical project details live in the files listed below.

## Read First

Before changing code or documentation, read these in order:

1. `START_HERE_项目速览.md`
2. `AGENTS.md`
3. `项目开发进度_XiaoClawBrain.md`
4. `开发问题解决记录.md` when debugging or touching an area with prior incidents
5. `架构方案_后端服务器_XiaoClawBrain_GPT修订定稿版.md` when changing architecture, protocol, milestones, or cross-end behavior

Do not scan these directories unless the user explicitly asks for history, cache/build investigation, archive comparison, or release inspection:

- `待移出工作区_低上下文归档_2026-05-14/`
- any `build/`
- any `managed_components/`
- any `releases/`
- `.omx/logs/`

## Project Shape

XiaoClawBrain has two active product lines plus one diagnostic area:

- Workspace root wrapper repo: `/Volumes/软件/opencode/ESP32S3语音陪伴设备开发` is a lightweight Git repo for shared documents, plans, and progress logs only. It must not absorb the backend or firmware child repository history.

- Backend server line: `MadBull8994/xiaozhi-esp32-server`, branch `xiaoclawbrain-v0.1`, baseline commit `fb7e2e17a8340232155d35bb2f988522d3b3232b`.
- ESP32 firmware line: branch `xiaoclaw-ghproxycom`, an ESP32-S3 thin client for audio I/O, TFT, buttons, WebSocket binary protocol, and local Opus fallback audio.
- `hardware_bringup`: diagnostic tools only. Do not grow it into the product system.

Always decide whether the task belongs to the backend, the ESP32 firmware, or diagnostics before opening many files.

## Version And Stable Backups

- Current backend runtime version: `0.9.3` from `xiaozhi-esp32-server/main/xiaozhi-server/config/logger.py`.
- Current hello/protocol config version: `1` from backend `config.yaml`; do not treat it as the project release version.
- Current stable baseline: `XCB-STABLE-P9-complete+P6clean+20260602.123952`.
- Baseline non-sensitive full asset backup: `backup_snapshot_20260602_123952_full`.
- Baseline sensitive local asset backup: `backup_sensitive_20260602_123952`.
- Baseline versioned asset directory: `stable_version_assets/XCB-STABLE-P9-complete+P6clean+20260602.123952`.
- Previous stable baseline: `XCB-STABLE-P9-complete+20260602.115528` (`backup_snapshot_20260602_115528_full` + `backup_sensitive_20260602_115528`).
- Only create new full asset backups after the user and agent agree that the current state is a stable version. Do not create full asset backups for intermediate development iterations between two stable versions.
- Keep only the latest 3 stable full asset versions. When creating a 4th stable full asset backup, delete the oldest stable full asset backup.
- Record the stable version number in backup notes and `项目开发进度_XiaoClawBrain.md` before continuing development from that point.

## Architecture Guardrails

- The product architecture is ESP32 thin client plus backend fat service.
- ESP32 records, Opus-encodes, uploads binary frames, receives binary Opus frames, decodes, plays, displays state, handles buttons, and later performs local wake word detection.
- The backend owns WebSocket connection management, Token auth, ASR, LLM, TTS, Opus packet downlink, timeout/error handling, SQLite session memory, logs, healthcheck, Skill, and Agent loop.
- Do not revive ESP32 local ASR/TTS as the main path.
- Do not move wake word detection to backend streaming ASR.
- Do not expand first-version scope into OTA, multi-device admin, complex long-term memory, Skill marketplace, large MCP integration, backend Cron, user interruption, or TTS playback-time re-listening.

## Protocol Red Lines

Never restore this old protocol:

```json
{"type":"tts","audio":"<base64_opus>"}
```

Required protocol rules:

- TTS audio must be sent as continuous WebSocket binary frames, not base64 JSON.
- Recommended binary frame size is 512-1024 bytes.
- Maximum binary frame size is 2048 bytes.
- Maximum JSON control message size is 4KB.
- Long text must be split by sentence.
- JSON control messages and binary audio frames must be clearly distinguishable.
- Cross-end protocol changes must be documented first, then implemented on backend and ESP32 separately.

Canonical TTS downlink:

```text
Server -> ESP32 JSON:   {"type":"tts_start","codec":"opus","sample_rate":16000}
Server -> ESP32 binary: Opus frame <= 2048 bytes
Server -> ESP32 binary: Opus frame <= 2048 bytes
Server -> ESP32 JSON:   {"type":"tts_end"}
```

## State Machine And Timeouts

First-version state path:

```text
idle -> wakeup_detected -> listening -> uploading_audio -> recognizing -> thinking -> synthesizing -> speaking -> idle
```

Exception states:

```text
error / reconnecting
```

Rules:

- P1-P8 may use BOOT button recording for bring-up and end-to-end testing.
- P9 must implement ESP32 local wake word detection. The product is not closed-loop complete before this.
- Wake-up flow must enter `idle -> wakeup_detected -> listening -> uploading_audio`.
- Reserve or implement a 0.5-1 second audio ring buffer for wake-up so the start of speech is not cut off.
- After user speech stops, wait 5 seconds with no new voice before recognition.
- Audio shorter than 500ms or too low energy is discarded before ASR.
- ASR empty text returns `ASR_EMPTY` immediately with "我没听清，你再说一遍" and must not enter LLM.
- Keep the short-word whitelist: 好、对、嗯、是、否、不、停、停止、继续、不要。
- Playback failure stops the current turn, records the error, and returns to `idle`.
- One dialogue turn must end after 120 seconds (extreme deadlock safeguard) and return to `idle`. Normal per-segment timeouts (ASR 5s, LLM 20s, TTS 8s, tool_call 30s) are the primary anti-hang measures.

Timeouts:

- ASR: 5s, no retry.
- LLM: 20s, retry once.
- TTS: 8s, no retry.
- WS idle: 30s.
- Whole turn: 120s (extreme deadlock safeguard).
- WS reconnects automatically.

## Security And Logs

- Never write real DeepSeek keys, Baidu AppID/AppKey/Secret, device tokens, API keys, or other secrets into source, sdkconfig, logs, or documentation.
- Use only `SET`, `EMPTY`, or `token=SET` style markers in records.
- Never log full Authorization headers.
- Never log full API request or response bodies by default.
- Transcribed user speech should be logged only as a summary or first 50 characters unless debug mode is explicitly enabled by environment variable.
- `.env` must not be committed.
- `.env.example` may contain only empty templates.

## Skill Safety

First-version Skill support is basic loading and invocation only:

- Run Skill scripts in subprocesses.
- Do not directly `import` or `exec` unknown Skill scripts.
- Use timeout, default 5 seconds.
- Use `limited_env`; Skill subprocesses must not receive full secrets from `.env`.
- Restrict the working directory to the Skill directory.
- Limit stdout, recommended 64KB.
- Log stderr internally and do not directly return it to the user.
- Do not load unknown-source Skills in production.

## Workflow Rules

- Make the smallest useful change for one clear task.
- Do not rename core directories, core classes, or startup entries unless asked.
- Do not change backend and ESP32 broadly in one pass. For cross-end protocol changes, update docs first.
- Before deleting old code, confirm it is not referenced by the current main line.
- Do not add large dependencies unless the task requires them and the reason is recorded.
- Do not merge archived old plans back into current code unless the user asks.
- After every completed task, update `项目开发进度_XiaoClawBrain.md` with newest entry first.
- If a new problem was found and solved, update `开发问题解决记录.md`.
- If protocol, phase split, error code, state machine, deployment method, or architecture decision changes, also update `START_HERE_项目速览.md` and the architecture document as appropriate.

## Current Direction Snapshot

As of 2026-06-04, Phase 1 P0-P9 remains complete from stable baseline `XCB-STABLE-P9-complete+P6clean+20260602.123952`.
P6 thin-client cleanup remains closed, and P9 local wake word acceptance passed 10/10 real-device retests on 2026-06-02.
A 2026-06-04 project audit did not find a new functional blocker that reopens Phase 1; the remaining loose ends were stale post-P9 document pointers, which have now been aligned.
Next direction: Phase 2 P1 配置与持久化基线。先明确哪些默认配置必须跨更新保留，以及默认 global skill / cloned Baidu voice / agent persona 的权威存储位置。

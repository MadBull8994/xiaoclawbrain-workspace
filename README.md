# XiaoClawBrain Workspace

This repository is the **workspace-level documentation and coordination repo** for the XiaoClawBrain project.

XiaoClawBrain itself is split across **three Git repositories**:

1. `MadBull8994/xiaoclawbrain-workspace`
   - Workspace docs, architecture notes, roadmap, progress log, and release coordination
2. `MadBull8994/xiaozhi-esp32-server`
   - Backend server line: WebSocket service, auth, ASR/LLM/TTS pipeline, Skill runtime, health checks
3. `MadBull8994/xiaoclaw-esp32-firmware`
   - ESP32 firmware line: audio I/O, wake word entry, display, buttons, WebSocket thin client, local fallback prompts

## Current Project Shape

XiaoClawBrain follows a **thin-device + fat-server** architecture:

- The ESP32 device is responsible for wake-up entry, recording, playback, local state display, and WebSocket transport
- The backend is responsible for auth, ASR, LLM, TTS, memory, logging, and service orchestration
- Cross-end protocol rules and project milestones are coordinated from this workspace repo

## Start Here

- Project overview: `START_HERE_项目速览.md`
- Development roadmap: `项目开发路线图_XiaoClawBrain.md`
- Progress log: `项目开发进度_XiaoClawBrain.md`
- Architecture document: `架构方案_后端服务器_XiaoClawBrain_GPT修订定稿版.md`

## Repository Workflow

- Use this repo for workspace-level docs and planning only
- Use `xiaozhi-esp32-server` for backend code changes
- Use `xiaoclaw-ghproxycom` for firmware code changes
- GitHub remotes are managed separately per repo

## Status

- Workspace repo default branch: `main`
- Backend active development branch: `xiaoclawbrain-v0.1`
- Firmware active branch in the user-owned remote: `xiaoclaw-ghproxycom`

This repo intentionally keeps the code split visible so documentation, backend, and firmware can evolve independently without losing project coordination.

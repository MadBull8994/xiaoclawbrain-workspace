# 项目工作指引

新会话开始时，优先阅读根目录的 `START_HERE_项目速览.md` 和 `CLAUDE.md`，再按任务需要打开具体源码或记录。

项目架构方案文档：`架构方案_后端服务器_XiaoClawBrain_GPT修订定稿版.md`

默认不要扫描或阅读以下目录，除非用户明确要求恢复历史资料、排查构建缓存、对比旧方案或查看归档内容：

- `待移出工作区_低上下文归档_2026-05-14/`
- 任意 `build/`
- 任意 `managed_components/`
- 任意 `releases/`
- `.omx/logs/`

---

## 当前代码主线

本项目包含两条主线。开始任务前，必须先判断当前任务属于后端服务器还是 ESP32 固件。

### 1. 后端服务器主线

- 仓库：`MadBull8994/xiaozhi-esp32-server`
- 分支：`xiaoclawbrain-v0.1`
- 基准 commit：`fb7e2e17a8340232155d35bb2f988522d3b3232b`
- 用途：XiaoClawBrain 后端、WebSocket、Token 鉴权、ASR/LLM/TTS、Opus 小包下发、Skill、SQLite、日志、healthcheck。

### 2. ESP32 固件主线

- 分支：`xiaoclaw-ghproxycom`
- 用途：ESP32-S3 纯终端、音频 I/O、TFT、按键、WS binary 协议、本地 Opus 降级音频。

### 3. 诊断工具

`hardware_bringup` 只作为硬件、API、音频和网络诊断工具保留，不再扩展成正式产品系统。

---

## 协议红线

以下规则不得破坏。任何跨端协议变更都必须先更新文档，再分别实现后端和 ESP32 端。

- TTS 音频不得通过 base64 JSON 下发。
- TTS 音频必须通过 WebSocket binary frame 小包连续下发。
- 单个 binary frame 建议 512-1024 bytes，最大不得超过 2048 bytes。
- 单条 JSON 消息最大不得超过 4KB。
- 长文本必须按句子分段发送。
- JSON 控制消息与 binary 音频帧必须能明确区分。
- ASR_EMPTY 不进入 LLM，第一次空识别就提示“我没听清，你再说一遍。”
- 第一版主链路不处理用户打断 AI，也不处理 TTS 播放中重新拾音。
- 阶段一至阶段八允许按键触发录音，但本地语音唤醒是阶段九必做项，产品验收前不可跳过。

禁止恢复以下旧协议：

```json
{"type":"tts","audio":"<base64_opus>"}
```

---

## 语音唤醒硬性路线

语音唤醒是产品必做能力，但按阶段推进：

- 阶段一至阶段八可以使用 BOOT 按键触发录音，用于优先打通端到端主链路。
- 阶段九必须实现 ESP32 本地语音唤醒。语音唤醒未完成前，项目不能标记为产品级闭环完成。
- 不要把唤醒词检测放到后端持续流式 ASR。
- 唤醒词检测必须在 ESP32 本地完成。
- 实现前必须评估 ESP-SR / WakeNet / 自定义轻量唤醒词与当前音频链路的兼容性。
- 唤醒后状态机必须走 `idle → wakeup_detected → listening → uploading_audio`。
- 需要预留或实现 0.5-1 秒环形音频缓冲，避免唤醒后吞掉用户开头。
- 按键触发可以长期保留为调试/备用入口，但不能替代最终语音唤醒。

---

## 修改范围约束

- 优先做最小可用改动，不做无关重构。
- 不要重命名已有核心目录、核心类、启动入口，除非任务明确要求。
- 不要同时修改后端和 ESP32 大量文件；跨端协议变更必须先更新文档，再分别实现。
- 每次任务只解决一个明确目标。
- 删除旧代码前，必须确认没有被当前主线引用。
- 不要自动引入大型新依赖，除非任务明确需要，并在进度文档记录原因。
- 不要把诊断目录 `hardware_bringup` 扩展成正式产品系统。
- 不要因为阶段一至阶段八使用按键触发，就删除或忽略语音唤醒的阶段九任务。
- 不要把归档目录里的旧方案重新合并进当前主线，除非用户明确要求。

---

## 密钥、日志与隐私约束

严禁把 DeepSeek Key、百度 AppID/AppKey/Secret、token 或其他凭据明文写入源码、sdkconfig、日志或文档；记录时只写 `SET/EMPTY` 或 `token=SET`。

额外规则：

- 日志中不得记录完整 Authorization header。
- 日志中不得记录完整 API 请求体/响应体。
- 日志中不得记录完整 API Key、Secret、Token。
- 用户语音转写文本默认只记录摘要或前 50 字，除非明确处于调试模式。
- 调试模式必须通过环境变量开启，默认关闭。
- `.env` 不得提交到 Git。
- `.env.example` 只能保留空值模板。

---

## 开发工作流要求

### 项目开发进度

- 项目开发进度记录在 `项目开发进度_XiaoClawBrain.md` 中。
- **每次完成一个任务后，必须立即更新该文档**：标记完成项、更新状态、记录当前进度、明确下一步要做的任务。
- 进度文档采用倒序记录（最新更新在最前面）。

### 开发问题记录

- 开发过程中遇到的问题和解决方案记录在 `开发问题解决记录.md` 中。
- **遇到新的（未记录过的）问题并解决后，必须记录到该文档**：问题描述、根因、解决方案、涉及文件。
- 已有记录的问题可直接参考，避免重复踩坑。

### 文档同步

- 修改协议、阶段拆分、错误码、状态机、部署方式时，必须同步更新 `START_HERE_项目速览.md`。
- 修改架构级决策时，必须同步更新架构方案文档。
- 修改完成后，在 `项目开发进度_XiaoClawBrain.md` 记录文档变更。

---

## 第一版实现边界

第一版优先目标是“稳定听、稳定想、稳定说、异常能恢复”。

第一版必须做：

- Token 鉴权。
- WebSocket JSON 控制消息。
- WebSocket binary Opus 小包上传/下发。
- ASR / LLM / TTS 基础闭环。
- API timeout。
- 错误协议与错误码。
- 基础状态机。
- SQLite session 级记忆。
- 日志系统。
- Docker healthcheck。
- 本地 Opus 降级提示音。
- 语音唤醒接入点预留：状态机包含 `wakeup_detected`，按键触发和唤醒触发都能进入 `listening`。

第一版明确不做：

- 不做用户打断 AI。
- 不做 TTS 播放中重新拾音。
- 不做 OTA。
- 不做多设备后台。
- 不做复杂长期记忆。
- 不做 Skill 市场。
- 不做大规模 MCP 工具接入。
- 不做后端 Cron。
- 阶段一至阶段八暂不做本地语音唤醒词，但阶段九必须完成，且不得破坏预留接口。
- 不做 ESP32 本地 ASR/TTS。
- 不做 base64 TTS 音频下发。

---

## 状态机与错误处理规则

第一版基础状态：

```text
idle → wakeup_detected → listening → uploading_audio → recognizing → thinking → synthesizing → speaking → idle

阶段一至阶段八可由按键直接触发 `wakeup_detected` 或 `listening`，阶段九必须由本地唤醒词触发。
```

异常状态：

```text
error / reconnecting
```

第一版规则：

- 用户说话停止后等待 5 秒，无新语音则进入识别流程。
- 声音小于 500ms 或能量过低时丢弃，不进入 ASR。
- ASR 返回空文本时，第一次就返回 `ASR_EMPTY`，提示“我没听清，你再说一遍”，不进入 LLM。
- 单字/短词白名单保留：好、对、嗯、是、否、不、停、停止、继续、不要。
- 播放失败时停止本轮，记录错误，回到 `idle`。
- 单轮对话总耗时超过 120 秒时极端死锁兜底，回到 `idle`。

第一版 timeout：

- ASR timeout：5s
- LLM timeout：20s
- TTS timeout：8s
- WS idle timeout：30s
- 单轮对话总 timeout：120s

重试策略：

- ASR 不重试。
- LLM 可重试 1 次。
- TTS 不重试。
- WS 自动重连。

---

## Skill 子进程隔离规则

Skill 第一版只做基础加载和调用，不做 Skill 市场。

- Skill 脚本通过子进程运行，不直接 `import` 或 `exec` 未知脚本。
- Skill 运行必须带 `timeout`，默认 5 秒。
- Skill 子进程使用 `limited_env`，不得读取 `.env` 中的完整密钥。
- Skill 工作目录限制在对应 skill 目录。
- stdout 最大长度限制，建议 64KB。
- stderr 写入日志，不直接返回给用户。
- 生产期不得加载未知来源 Skill。

---

## 本地 timer 与后端 Cron 边界

第一版提醒/倒计时可由 ESP32 本地 timer 实现。

涉及以下能力的定时任务不放在 ESP32，本期暂缓，后续统一迁移到后端 Cron：

- 联网查询后提醒。
- 跨设备提醒。
- 持久化提醒。
- 记忆整理。
- 日程同步。
- 系统健康检查后的主动通知。

# XiaoClawBrain — 后端服务器重做项目速览

创建时间：2026-05-17  
修订时间：2026-06-02（P6 清理后新稳定版本已创建并完成轮转）  
项目架构方案：[架构方案_后端服务器_XiaoClawBrain_GPT修订定稿版.md](./架构方案_后端服务器_XiaoClawBrain_GPT修订定稿版.md)  
项目开发路线图：[项目开发路线图_XiaoClawBrain.md](./项目开发路线图_XiaoClawBrain.md)  
开发进度：[项目开发进度_XiaoClawBrain.md](./项目开发进度_XiaoClawBrain.md)

---

## 项目背景

ESP32-S3 端 ESP-IDF v5.5.2 的 mbedTLS 在接收 >4KB HTTP 响应时触发 `MBEDTLS_ERR_SSL_INVALID_RECORD`，跨 WebSocket/HTTP、跨 Baidu REST/流式 API 都无法绕过。

**决策**：放弃 ESP32 本地处理 ASR/LLM/TTS 的架构，转向小智生态的 **ESP32 瘦客户端 + 后端服务器胖服务端**。

---

## 硬件定稿（不变）

| 部件 | 型号 | 关键引脚 |
|------|------|----------|
| 主控 | ESP32-S3-N16R8 (16MB Flash + 8MB PSRAM) | - |
| 扩展板 | 小智AI扩展板44P V1.1 | - |
| 麦克风 | INMP441 | WS=GPIO4 / SCK=GPIO5 / SD=GPIO6 / L/R=GND |
| 功放 | MAX98357A | DIN=GPIO7 / BCLK=GPIO15 / LRC=GPIO16 |
| TFT | ST7735 | SCK=GPIO21 / SDA=GPIO47 / RES=GPIO45 / DC=GPIO40 / CS=GPIO41 / BL=GPIO42 |
| 按键 | BOOT=GPIO0 / VOL-=GPIO39 / VOL+=GPIO38(飞线) / RESET=RST |
| RGB | GPIO48 |

---

## 代码主线与仓库边界

本项目包含两条主线，开发时必须先确认当前任务属于哪一条。

另外，根目录 `/Volumes/软件/opencode/ESP32S3语音陪伴设备开发` 自 2026-06-03 起额外初始化为一个**工作区文档元仓库**：它只用于跟踪根目录项目文档、路线图、进度记录和规划材料，不吸收下面两个产品仓库的历史。

## 当前版本与稳定备份规则

- 当前后端运行时版本：`0.9.3`（定义于 `xiaozhi-esp32-server/main/xiaozhi-server/config/logger.py` 的 `SERVER_VERSION`）。
- 当前设备 hello/协议配置版本：`1`（定义于后端 `config.yaml` 的 `xiaozhi.version`，不是项目发布版本号）。
- 当前稳定基底版本：`XCB-STABLE-P9-complete+P6clean+20260602.123952`。
- 当前稳定基底全资产备份：
  - 非敏感全资产：`backup_snapshot_20260602_123952_full`
  - 敏感本机资产：`backup_sensitive_20260602_123952`
  - 版本资产目录：`stable_version_assets/XCB-STABLE-P9-complete+P6clean+20260602.123952`
  - 上一稳定基底：`XCB-STABLE-P9-complete+20260602.115528`（`backup_snapshot_20260602_115528_full` + `backup_sensitive_20260602_115528`）

后续开发只在双方讨论并确认“稳定版本”时创建新的全资产备份；两个稳定版本之间的日常开发迭代不做全资产备份。稳定备份最多保留最新 3 个，创建第 4 个稳定全资产备份时删除最旧的稳定全资产备份。备份和继续开发时必须沿用并记录版本编号，避免把临时快照误当成稳定基底。当前已按规则轮转删除最旧稳定备份 `XCB-STABLE-0.9.3+20260527.193019`。

### 1. 后端服务器主线

- 仓库：`https://github.com/MadBull8994/xiaozhi-esp32-server`
- 基准 commit：`fb7e2e17a8340232155d35bb2f988522d3b3232b`
- 开发分支：`xiaoclawbrain-v0.1`
- 用途：XiaoClawBrain 后端、WebSocket、Token 鉴权、ASR/LLM/TTS、Opus 小包下发、Skill、SQLite、日志、healthcheck。
- 当前语音 provider：ASR/TTS 已切到百度。ASR 使用百度实时语音识别 WebSocket（`BaiduASR`，`dev_pid=15372`，`InterfaceType.STREAM`，录音过程中推帧），TTS 使用百度语音合成（`BaiduTTS`）。

### 2. ESP32 固件主线

- 分支：`xiaoclaw-ghproxycom`
- 用途：ESP32-S3 纯终端、音频 I/O、TFT、按键、WS binary 协议、本地 Opus 降级音频。
- OpenClaw V2.7 调试期可通过设备局域网地址的 `:8080` 本地配置页写入 XiaoClaw WebSocket 运行时地址，例如 `http://192.168.31.128:8080/`。该配置写入 `Settings("websocket")`，优先级高于编译期 `CONFIG_XIAOCLAW_*` 兜底值。

### 3. 诊断工具分支/目录

- `hardware_bringup` 只作为硬件、API、音频和网络诊断工具保留，不再扩展成正式产品系统。

### 4. 根目录元仓库

- 根目录 Git 仓库只管理工作区级文档与规划文件。
- `xiaozhi-esp32-server/` 与 `xiaoclaw-ghproxycom/` 继续保持各自独立 Git 仓库。
- 日常开发与 GitHub 推送默认按仓库分别操作；除非后续单独迁移到 submodule 或 monorepo，不做“一次提交覆盖三处”的工作流。

---

## 架构总览

```text
ESP32-S3（纯终端）                 后端服务器（Python）
─────────────────                 ────────────────────
本地唤醒/按键 → 录音 → Opus上传    WS连接管理 + Token鉴权
WS binary接收 → Opus解码 → 播放    ASR → LLM → TTS
TFT 状态显示                       Opus编码 + binary下发
按键输入 + 本地语音唤醒预留          API timeout + 错误协议
WiFi STA/AP                       基础状态机 + SQLite记忆
本地 Opus 降级提示音                日志系统 + healthcheck
                                   增量：Skill引擎 + Agent循环
```

---

## 当前执行路线（2026-06-04 对齐版）

详细执行顺序现在拆成两段：

- 第一阶段 P0-P9 的历史执行顺序与验收口径：见 [项目开发路线图_XiaoClawBrain.md](./项目开发路线图_XiaoClawBrain.md)
- 第二阶段的正式启动入口与后续任务拆分：见 [第二阶段开发路线图_XiaoClawBrain.md](./第二阶段开发路线图_XiaoClawBrain.md)

第一阶段收口结论：

- P0-P9 核心闭环已完成。
- P6 旧架构清理已收口，P9 本地语音唤醒已于 2026-06-02 完成 10/10 实机稳定验收。
- 2026-06-04 复核未发现需要重新打开第一阶段验收结论的功能性阻塞；剩余尾巴主要是文档里残留的 pre-P9 / pre-Phase-2 旧口径，已同步清理。

第二阶段当前路线：

| 第二阶段 | 名称 | 目标 |
|----------|------|------|
| P1 | 配置与持久化基线 | 明确哪些配置必须跨更新保留，固定默认 persona / voice / global skill 的权威存储位置 |
| P2 | 角色、声音与默认 Skill 自定义 | 让默认角色、默认百度克隆音色、默认全局 Skill 变成可维护的正式能力 |
| P3 | 会话模式与记忆策略升级 | 把旧 P10 候选并入正式路线，设计默认单轮 + 可选连续对话与记忆保存/查询策略 |
| P4 | 交互体验增强 | 收纳播放中打断、TTS 播放中重新拾音等第一阶段刻意暂缓的交互能力 |
| P5 | 系统与平台增强 | 收纳 OTA、多设备后台、后端 Cron、复杂长期记忆、更广泛外部集成等重型能力 |
| P6 | 后台 UI 与配置页对齐评估 | 复核现有后台页面是否真实反映当前系统能力与配置归属 |

当前状态：**第一阶段已完成，第二阶段入口已建立。** 当前推荐从 **第二阶段 P1：配置与持久化基线** 开始，而不是继续沿用旧的 `P10 候选` 口径零散推进。

---

## 通信协议核心原则

| 规则 | 强制 |
|------|------|
| 禁止 base64 大 JSON 下发 TTS 音频 | ✅ 必须用 binary frame |
| 单个 binary frame 建议大小 | 512-1024 bytes |
| 单个 binary frame 最大大小 | ≤2048 bytes |
| 单条 JSON 消息最大大小 | ≤4KB |
| 长文本 | 按句子分段发送 |
| JSON 控制消息与 binary 音频帧 | 必须能明确区分 |
| ASR 空文本 | 第一次就提示“我没听清，你再说一遍”，不进入 LLM |

TTS 音频下发流程固定为：

```text
Server → ESP32 JSON:   {"type":"tts_start","codec":"opus","sample_rate":16000}
Server → ESP32 binary: Opus frame 1 <= 2048 bytes
Server → ESP32 binary: Opus frame 2 <= 2048 bytes
...
Server → ESP32 JSON:   {"type":"tts_end"}
```

禁止恢复以下旧协议：

```json
{"type":"tts","audio":"<base64_opus>"}
```

---

## 交互入口与语音唤醒

语音唤醒是产品必做能力，但不阻塞前期端到端主链路联调。

### 阶段一至阶段八的临时入口

为了优先验证“录音 → ASR → LLM → TTS → 播放”的主链路，阶段一至阶段八允许使用按键触发录音：

- BOOT 按键：开始一次语音输入（若正在播放/synthesizing，则先停止当前播放）。
- 用户说话停止后等待 5 秒，无新语音则进入上传与 ASR。
- ASR_EMPTY 第一次即返回“我没听清，你再说一遍”。
- 按键触发只作为联调入口，不代表最终产品交互完成。
- OpenClaw V2.7 当前已启用 `CONFIG_USE_AFE_WAKE_WORD`，WakeNet 模型通过 `model` 分区烧录并在启动时加载；BOOT 录音仍保留为调试/备用入口。当前可用交互模式为：先说 `你好小智`，等待提示音后再说问题。

### 产品必做语音唤醒

语音唤醒必须在 ESP32 本地实现，不能把唤醒词检测放到后端持续流式处理。

后续必做路线：

- 唤醒词检测在 ESP32 本地完成。
- 后端不负责持续监听唤醒词。
- 唤醒成功后进入 `wakeup_detected`，再进入 `listening`。
- 优先评估 ESP-SR / WakeNet 与当前 INMP441 + I2S + Opus 链路的兼容性。
- 增加 0.5-1 秒环形音频缓冲，避免唤醒后吞掉用户开头。
- 增加误唤醒记录与阈值调优机制。

语音唤醒完成前，项目可以进入技术联调阶段，但不能视为产品级闭环完成。

---

## 状态机第一版

第一版主链路先实现基础状态流；阶段一至阶段八允许按键触发，阶段九必须补齐本地语音唤醒。不处理用户打断 AI，也不处理 TTS 播放中重新拾音。

```text
idle
  ↓ 本地唤醒词命中 / 按键触发
wakeup_detected
  ↓
listening
  ↓
uploading_audio
  ↓
recognizing
  ↓
thinking
  ↓
synthesizing
  ↓
speaking
  ↓
idle
```

异常状态：

```text
error
reconnecting
```

第一版规则：

- 用户说话停止后等待 5 秒，无新语音则进入识别流程。
- 声音小于 500ms 或能量过低时丢弃，不进入 ASR。
- ASR 返回空文本时，第一次就返回 `ASR_EMPTY`，提示“我没听清，你再说一遍”，不进入 LLM。
- 单字/短词白名单保留：好、对、嗯、是、否、不、停、停止、继续、不要。
- 播放失败时停止本轮，记录错误，回到 `idle`。
- 单轮对话生成阶段超过 120 秒时极端死锁兜底（不作为正常轮次预期耗时），回到 `idle`；分段 timeout（ASR 5s / LLM 20s / TTS 8s / tool_call 30s）才是真正的防卡死前锋。TTS 已生成音频的播放发送 drain 使用独立 `playback_drain_timeout`，避免正常播放尾部被误判为整轮超时。
- 网络断开/重连第一版不语音提醒，板子通过指示灯/TFT 显示状态。
- 语音唤醒阶段必须新增 `wakeup_detected` 状态，并保证唤醒后进入 `listening`。

---

## 阶段拆分与任务

本节保留原始阶段拆分，便于查阅验收细节；实际执行顺序以 2026-05-20 新增的 [项目开发路线图_XiaoClawBrain.md](./项目开发路线图_XiaoClawBrain.md) 为准。

### 阶段零：项目初始化

| # | 任务 | 子任务 |
|---|------|--------|
| 0.1 | 克隆基准仓库 | `git clone` + checkout `fb7e2e17a8340232155d35bb2f988522d3b3232b` |
| 0.2 | 创建开发分支 | `xiaoclawbrain-v0.1`，推送到远端 |
| 0.3 | 锁定 Python 依赖 | `requirements.txt` pin 到具体版本号 |
| 0.4 | 建立进度记录 | 创建/更新 `项目开发进度_XiaoClawBrain.md` |
| 0.5 | 建立问题记录 | 创建/更新 `开发问题解决记录.md` |

#### 阶段零验收标准

- 本地仓库来自 `MadBull8994/xiaozhi-esp32-server`。
- 当前分支为 `xiaoclawbrain-v0.1`。
- 基准 commit 已记录在文档中。
- 依赖文件可复现安装。
- 进度文档和问题记录文档存在。

---

### 阶段一：基础 WS 连接与协议骨架

目标：先让后端和 ESP32 通过新协议“握手、认人、收发小包”，不要等到后期才发现两端协议不匹配。

| # | 任务 | 子任务 |
|---|------|--------|
| 1.1 | 后端 WebSocket 连接管理 | WS 服务启动、连接生命周期管理、heartbeat |
| 1.2 | Token 设备鉴权 | 握手阶段 `Authorization: Bearer` 验证，401 拒绝 |
| 1.3 | ESP32 WS client 最小适配 | 能连接后端、携带 Token、处理连接失败 |
| 1.4 | 控制 JSON 协议骨架 | 实现 listen/state/stt/sentence_*/tts_*/error 的基础收发 |
| 1.5 | binary frame 收发 smoke test | ESP32 能发 binary，后端能识别；后端能发 binary，ESP32 能接收 |
| 1.6 | 帧大小保护 | JSON ≤4KB，binary frame ≤2048 bytes，超限拒绝并记录日志 |

#### 阶段一验收标准

- 后端可以启动 WS 服务。
- ESP32 可以携带 `Authorization: Bearer <DEVICE_TOKEN>` 连接成功。
- Token 错误时服务端拒绝连接，返回 401 或立即断开。
- ESP32 可以发送 JSON 控制消息。
- ESP32 可以发送 binary frame。
- 后端可以区分 JSON frame 与 binary frame。
- 后端可以发送 binary frame 给 ESP32。
- 单条 JSON 超过 4KB 时拒绝或截断，并记录日志。
- 单个 binary frame 超过 2048 bytes 时拒绝，并记录日志。

---

### 阶段二：稳定听（录音上传 → ASR）

目标：ESP32 能通过 WS binary 上传 Opus 音频，后端完成 ASR 识别。

| # | 任务 | 子任务 |
|---|------|--------|
| 2.1 | ESP32 Opus 上传 | 录音 → Opus 编码 → WS binary 小包上传 |
| 2.2 | 后端音频接收 | 接收并按会话管理音频帧 |
| 2.3 | ASR 集成 | 对接 ASR provider，完成 audio → text 链路 |
| 2.4 | 用户停顿规则 | 检测语音停止后等待 5 秒，无新语音则进入下一步 |
| 2.5 | ASR_EMPTY 处理 | 空文本第一次就提示“我没听清，你再说一遍”，不进入 LLM |
| 2.6 | 短音频/低能量过滤 | 声音 <500ms 或能量过低时丢弃 |
| 2.7 | 单字白名单 | 好、对、嗯、是、否、不、停、停止、继续、不要 |

#### 阶段二验收标准

- ESP32 录音后能上传 Opus binary frame。
- 后端能接收并处理音频帧。
- 后端能调用 ASR provider。
- ASR 返回文本时，后端发送 `stt` JSON。
- ASR 返回空文本时，后端发送 `ASR_EMPTY`，并且不进入 LLM。
- 声音小于 500ms 或能量过低时丢弃。
- 用户停止说话 5 秒后进入识别流程。

---

### 阶段三：稳定想、稳定说（LLM → TTS → 播放）

目标：后端完成 LLM 推理 + TTS 合成，通过 WS binary 下发 Opus 小包到 ESP32 播放。

| # | 任务 | 子任务 |
|---|------|--------|
| 3.1 | LLM 集成 | 对接 LLM provider，支持按句子输出 |
| 3.2 | sentence_* 分句下发 | 长文本按句子发送 `sentence_start` / `sentence_end` |
| 3.3 | TTS 集成 | 对接 TTS provider，合成文本 → Opus 音频 |
| 3.4 | Opus 编码与小包下发 | TTS 音频按 512-1024 bytes 分帧，最大 ≤2048 bytes |
| 3.5 | ESP32 音频播放链路 | WS binary 接收 → Opus 解码 → I2S → MAX98357 播放 |
| 3.6 | 单轮对话总超时 | 120 秒极端死锁兜底后回 `idle` |

#### 阶段三验收标准

- LLM 能接收 ASR 文本并返回回复。
- 长文本能按句子下发，不发送超大 JSON。
- TTS 音频不通过 base64 JSON 下发。
- TTS 音频通过 WS binary Opus 小包连续下发。
- 单个 binary frame 建议 512-1024 bytes，最大不超过 2048 bytes。
- ESP32 能连续播放下发音频。
- 播放结束后回到 `idle`。
- 单轮对话超过 120 秒时极端死锁兜底，不会卡死。

---

### 阶段四：异常与可靠性（异常能恢复）

| # | 任务 | 子任务 |
|---|------|--------|
| 4.1 | API 超时控制 | ASR 5s / LLM 20s / TTS 8s / round 120s / playback drain 120s / WS idle 30s |
| 4.2 | 重试策略 | ASR 不重试 / LLM 重试 1 次 / TTS 不重试 / WS 自动重连 |
| 4.3 | 错误协议与错误码 | ASR_EMPTY / ASR_TIMEOUT / LLM_TIMEOUT / LLM_ERROR / TTS_TIMEOUT / TTS_ERROR / WS_AUTH_FAILED / WS_DISCONNECTED / AUDIO_DECODE_ERROR |
| 4.4 | 常见异常处理 | 声音太短 / ASR 空 / 单字白名单 / 播放失败 / 总超时 |
| 4.5 | 日志系统 | 统一日志格式、级别、轮转、敏感信息脱敏 |

#### 阶段四验收标准

- ASR 超过 5 秒返回 `ASR_TIMEOUT`。
- LLM 超过 20 秒返回 `LLM_TIMEOUT`，可重试 1 次。
- TTS 生成超过 8 秒返回 `TTS_TIMEOUT`；已生成音频的播放发送队列超过 `playback_drain_timeout` (120s) 仍未 drain 时，同样按 `TTS_TIMEOUT` 收口。
- WS idle 超过 30 秒触发保活或重连逻辑。
- 所有错误都能回到 `idle` 或 `reconnecting`，不让设备卡死。
- 日志不记录完整 Token、Authorization header、API Key、完整请求体/响应体。
- 用户 ASR 文本默认只记录摘要或前 50 字，除非调试模式开启。

---

### 阶段五：持久化与运维

| # | 任务 | 子任务 |
|---|------|--------|
| 5.1 | SQLite session 记忆 | session 级对话记录、最近上下文读取、基础持久化 |
| 5.2 | 记忆边界控制 | 第一版不做长期人格记忆、不做自动总结、不做复杂记忆整理 |
| 5.3 | Docker healthcheck | 健康检查端点、容器自愈 |
| 5.4 | Docker 生产配置 | 生产期可用 `restart: unless-stopped` |

#### 阶段五验收标准

- SQLite 数据库能保存 session 级对话记录。
- 后端能读取最近上下文用于当前会话。
- 不实现长期记忆总结和复杂记忆整理。
- `/health` 或等价健康检查可用。
- Docker healthcheck 能检测服务是否真的可连接。

---

### 阶段六：增量模块

| # | 任务 | 子任务 |
|---|------|--------|
| 6.1 | Skill 加载器 | `skill_loader.py` — 子进程隔离加载、timeout、limited_env、cwd、stdout 限制 |
| 6.2 | Skill 接口 | `skill_plugin.py` — Skill 插件基类与注册机制 |
| 6.3 | Agent 循环 | `agent_loop.py` — ASR → Skill 匹配 → LLM → TTS 循环调度 |
| 6.4 | 自定义 Skill 兼容 | 兼容 OpenClaw / agent 项目风格的 `SKILL.md` 元数据和本地自定义 Skill |
| 6.5 | GitHub Skill 导入 | GitHub Skill 默认导入到 pending 目录，启用策略由安全等级决定 |
| 6.6 | Skill 安全等级 | `high` 默认、`low` 开发/实验、`off` 用户自担 |
| 6.7 | 全局默认 Skill | 支持由后端选择每轮默认运行的 global Skill，用于持续生效的能力 |

#### 阶段六验收标准

- `high` 模式默认开启：子进程运行、timeout 5 秒、limited_env、cwd 限制、stdout 64KB、stderr 写日志。
- `low` 模式可用于用户自定义/实验 Skill：放宽 timeout、env allowlist、stdout 上限和网络访问，但仍记录 `security_level=low`。
- `off` 模式不做主动限制，由用户决定是否运行，也可先调用安全审查类 Skill；必须记录 `security_level=off`。
- GitHub Skill 可导入，但默认不自动启用；启用策略必须受安全等级控制。
- Skill 不在 ESP32 端执行，只在后端执行。
- 全局默认 Skill 必须由后端配置显式选择；未被选中的 Skill 只在显式触发或匹配命中时按需运行。
- 全局默认 Skill 可配置为每轮对话运行，但必须受 timeout、数量上限、失败策略和安全等级约束。
- 按需 Skill 第一版通过显式触发和保守关键词判断是否运行；LLM 自动选择 Skill 通过 `skills.llm_tool_call.enabled` 开关控制，默认关闭。
- Agent 循环能在无 Skill 命中时正常走 LLM 对话。

---

### 阶段七：ESP32 清理与产品化适配

阶段一已经做最小协议适配；本阶段用于清理旧架构，减少长期维护负担。

| # | 任务 | 子任务 |
|---|------|--------|
| 7.1 | 移除旧 base64 TTS 格式 | 确认固件只接受控制 JSON + binary Opus |
| 7.2 | 移除本地 ASR/TTS provider | provider 层改为纯 WS 传输，不调用本地 API |
| 7.3 | 移除 bridge/mimiclaw | 删除 bridge 层、mimiclaw Agent、context_builder |
| 7.4 | 本地 Opus 降级提示音 | 网络断开 / 后端不可用时播放本地 Opus 音频 |
| 7.5 | 状态同步到 TFT | 实时显示 idle/listening/thinking/speaking/error/reconnecting |
| 7.6 | ESP32 本地 timer | 第一版只做简单倒计时/提醒，不做复杂联网定时任务 |

#### 阶段七验收标准

- ESP32 不再调用本地 ASR/TTS API。
- ESP32 不再依赖旧 bridge/mimiclaw Agent。
- ESP32 能播放本地 Opus 降级提示音。
- TFT 能显示关键状态。
- 本地 timer 只用于简单提醒/倒计时。
- 涉及联网、跨设备、持久化、记忆整理、日程同步的定时任务，后续统一迁移到后端 Cron。

当前收口结果（2026-06-02）：

- `application.cc` 中旧 `tts_response.audio` base64 播放路径已禁用，固件主链路保持为 JSON 控制消息 + binary Opus TTS。
- `main/providers/baidu_*`、`main/bridge/`、`main/mimi/` 当前不在顶层 `main/CMakeLists.txt` 的编译主链路中；它们不是当前稳定版运行依赖。

---

### 阶段八：联调与闭环验证

| # | 任务 | 子任务 |
|---|------|--------|
| 8.1 | 端到端闭环测试 | ESP32 按键 → 录音 → WS → ASR → LLM → TTS → WS → ESP32 播放 → idle |
| 8.2 | 异常场景验证 | 断线重连 / ASR 空 / LLM 超时 / TTS 失败 / 声音太短 / 总超时 |
| 8.3 | 长时间稳定性测试 | 连续多轮对话，监控内存泄漏、连接泄漏、音频漂移 |
| 8.4 | 帧大小回归测试 | 验证 JSON ≤4KB、binary ≤2048 bytes |

#### 阶段八验收标准

- 端到端完整对话链路可连续运行。
- 连续多轮对话不出现明显内存泄漏或连接泄漏。
- ASR 空、LLM 超时、TTS 失败、音频解码失败都能恢复。
- 长回复不会产生大 JSON 或大 binary frame。
- ESP32 断线重连后能重新进入 `idle`。

---

### 阶段九：本地语音唤醒，产品必做

阶段九是产品级交互闭环的必做阶段。阶段一至阶段八可以用按键触发完成链路联调，但语音唤醒未完成前，不能认为产品形态完成。

| # | 任务 | 子任务 |
|---|------|--------|
| 9.1 | 唤醒方案评估 | 评估 ESP-SR / WakeNet / 自定义轻量唤醒词，与 ESP32-S3-N16R8、INMP441、I2S 音频链路的兼容性 |
| 9.2 | 本地唤醒词检测 | ESP32 本地持续低成本监听唤醒词，不把唤醒检测放到后端 |
| 9.3 | 唤醒状态机 | `idle → wakeup_detected → listening → uploading_audio` |
| 9.4 | 环形音频缓冲 | 保留唤醒前 0.5-1 秒音频，避免吞掉用户开头 |
| 9.5 | 误唤醒控制 | 置信度阈值、连续误唤醒记录、TFT/日志可观测 |
| 9.6 | 唤醒后主链路复用 | 唤醒成功后复用已有 Opus 上传、ASR、LLM、TTS、播放链路 |

#### 阶段九验收标准

- ESP32 本地可以识别预设唤醒词。
- 唤醒词检测不依赖后端持续流式 ASR。
- 唤醒命中后进入 `wakeup_detected`，随后进入 `listening`。
- 唤醒后用户第一句话开头不被明显吞掉。
- 低置信度唤醒不会进入正式对话链路。
- 误唤醒事件能在日志或 TFT 状态中被观察。
- 按键触发仍可保留为调试/备用入口。

---

## 基准与版本

- 基准仓库：`https://github.com/MadBull8994/xiaozhi-esp32-server`
- Fork 来源：`https://github.com/xinnan-tech/xiaozhi-esp32-server`
- 锁定 commit：`fb7e2e17a8340232155d35bb2f988522d3b3232b`
- 锁定日期：`2026-05-16`
- 后端开发分支：`xiaoclawbrain-v0.1`
- ESP32 固件主线：`xiaoclaw-ghproxycom`

推荐初始化命令：

```bash
git clone https://github.com/MadBull8994/xiaozhi-esp32-server.git
cd xiaozhi-esp32-server
git checkout fb7e2e17a8340232155d35bb2f988522d3b3232b
git checkout -b xiaoclawbrain-v0.1
git push -u origin xiaoclawbrain-v0.1
```

---

## 第一版暂缓项

| 能力 | 原因 |
|------|------|
| 后端 Cron 调度 | 第一版提醒/倒计时可由 ESP32 本地 timer 实现；联网、跨设备、持久化、记忆整理、日程同步后续迁移到后端 Cron |
| 本地语音唤醒 | 阶段一至阶段八先用按键触发完成主链路联调；阶段九必须实现本地语音唤醒，产品验收前不可跳过 |
| 复杂 Skill 市场 | 第一版只做本地/导入 Skill 的后端兼容层，不做市场、评分、自动分发 |
| 用户打断 AI / TTS 播放中重新拾音 | 音频链路复杂化，第一版不处理 |
| OTA | 不进入第一版 |
| 多设备管理后台 | 不进入第一版 |
| 复杂长期记忆整理 | 不进入第一版 |
| 大规模 MCP 工具接入 | 不进入第一版 |

---

## 第一版明确不做

- 不做用户打断 AI。
- 不做 TTS 播放中重新拾音。
- 不做 OTA。
- 不做多设备后台。
- 不做复杂长期记忆。
- 不做 Skill 市场；P8 只支持本地自定义 Skill 和用户确认后的 GitHub Skill 导入。
- 不做大规模 MCP 工具接入。
- 不做后端 Cron。
- 阶段一至阶段八不做本地语音唤醒词，但必须保留后续接入点；阶段九必须完成。
- 不做 ESP32 本地 ASR/TTS。
- 不做 base64 TTS 音频下发。
- 不做超过 4KB 的 JSON 消息。
- 不做超过 2048 bytes 的 WS binary 音频帧。

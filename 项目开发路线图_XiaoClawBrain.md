# XiaoClawBrain 项目开发路线图

创建时间：2026-05-20
用途：作为推倒重做后的执行顺序总表。实际完成状态以 `项目开发进度_XiaoClawBrain.md` 为准。
最近对齐：2026-06-01

---

## 当前对齐结论

项目方向已经确定为：

```text
ESP32-S3 瘦客户端 + xiaozhi-esp32-server 胖后端
```

第一版目标只做“稳定听、稳定想、稳定说、异常能恢复”。阶段一至阶段八允许用 BOOT 按键触发录音；阶段九必须补齐 ESP32 本地语音唤醒，产品验收前不能跳过。

当前已知状态（2026-05-27）：

- 当前后续开发基础版本为 `XCB-STABLE-0.9.3+20260527.193019`。
- 后端 Docker + ASR/LLM/TTS + WebSocket binary Opus 下发已完成基础链路验证。
- 后端已切换到百度 ASR/TTS；ASR 已改为百度实时语音识别 WebSocket `InterfaceType.STREAM`，TTS 使用百度语音合成并保持 binary Opus 小包下发协议。
- P0-P4 后端基础线已完成；P5 ESP32 瘦客户端主链路已基本跑通。
- P7 端到端异常恢复与连续多轮稳定性仍需完成，尤其要复测百度 STREAM 后是否消除 `ASR_TIMEOUT`、误触发 timeout 和状态残留。
- ESP32 固件还保留旧 MimicLaw / 本地 provider / base64 TTS 兼容逻辑，P6 仍需要清理成纯终端。
- 跨端协议红线不变：禁止 base64 TTS，下发音频只走 WebSocket binary Opus 小包。
- P8 Skill/Agent 循环已完成；P9 ESP32 本地语音唤醒核心链路已跑通，但还需多轮实机稳定性收尾。
- “后台可选连续对话 + 记忆策略升级”已记录为 P10 候选升级项，不插入当前第一版 P0-P9 收尾。

当前下一步：

```text
P9 多轮实机复测与稳定性收尾
```

先验证真实多轮场景中的 WakeNet 命中率、首词完整性、状态收口、误唤醒、WS 稳定性与 BOOT 备用入口；P9 稳定验收后，再决定是否进入 P10 可选连续对话与记忆策略升级。

---

## 总体执行顺序

### P0：资料、仓库与现状对齐

目标：开工前先把“我们正在做哪条主线、从哪里开始、下一步是什么”固定下来。

任务：

- 更新 `START_HERE_项目速览.md`，指向本路线图。
- 更新 `项目开发进度_XiaoClawBrain.md`，把当前完成项和未完成项重新归类。
- 更新架构方案实施里程碑，避免旧 MVP 顺序与当前执行顺序冲突。
- 确认 `开发问题解决记录.md` 只记录已解决问题，不把计划写进去。

验收：

- 三份主文档口径一致。
- 下一步明确指向 P1，不直接跳到固件大改。

### P1：仓库基线与开发环境固定

目标：先把后端和 ESP32 两条主线放到可追踪、可回滚、可验证的状态。

后端任务：

- 将 `xiaozhi-esp32-server/` 恢复为真正 Git 仓库。
- checkout 基准 commit `fb7e2e17a8340232155d35bb2f988522d3b3232b`。
- 创建或切换到 `xiaoclawbrain-v0.1` 分支。
- 整理 `.env.example`、Docker 配置、依赖版本锁定。
- 保留当前已验证的 oMLX provider 配置记录，但不把密钥写入源码或文档。

ESP32 任务：

- 确认固件工作区目标分支。
- 梳理现有未提交改动，按“保留/废弃/待迁移”分类。
- 不在本阶段删除旧代码，只做基线确认。

验收：

- 后端 `git status --short --branch` 可用。
- ESP32 当前分支、未提交改动、目标分支在进度文档中明确。
- 本地开发环境可以复现启动后端容器。

### P2：后端协议骨架与鉴权硬化

目标：把已经验证过的 WebSocket 链路变成可依赖的第一版协议入口。

任务：

- 启用单设备 Token 鉴权，握手阶段校验 `Authorization: Bearer <DEVICE_TOKEN>`。
- 保留或兼容 `device-id` / `client-id` header，日志中只记录脱敏值。
- 增加 JSON 消息大小限制，单条最大 4KB。
- 增加 binary frame 大小限制，单帧最大 2048 bytes。
- 明确 JSON 控制消息和 binary 音频帧的路由边界。
- 增加最小 smoke test：鉴权成功、鉴权失败、JSON 超限、binary 超限、binary echo/下发。

验收：

- Token 错误时连接被拒绝或立即关闭。
- 超限 JSON / binary 会被拒绝并记录脱敏日志。
- 不出现 `{"type":"tts","audio":"<base64_opus>"}` 下发路径。

### P3：后端稳定听、稳定想、稳定说

目标：后端主链路满足第一版“能对话且异常可恢复”的基本产品要求。

任务：

- 稳定听：Opus 接收、ASR 调用、空识别处理、短音频/低能量过滤。
- 稳定想：LLM provider timeout 20 秒，失败重试 1 次。
- 稳定说：TTS timeout 8 秒，输出按句子分段，音频只走 binary Opus 小包。
- 实现单轮总 timeout 35 秒。
- 明确状态流：`idle → wakeup_detected → listening → uploading_audio → recognizing → thinking → synthesizing → speaking → idle`。
- ASR_EMPTY 第一次即提示“我没听清，你再说一遍”，不进入 LLM。

验收：

- ASR/LLM/TTS 正常链路可完成一轮对话。
- ASR_EMPTY、ASR_TIMEOUT、LLM_TIMEOUT、LLM_ERROR、TTS_TIMEOUT、TTS_ERROR 都能回到 `idle`。
- 长文本不会产生超过 4KB 的 JSON。
- TTS binary frame 不超过 2048 bytes。

### P4：后端持久化、日志与运维

目标：让后端从“能跑”变成“可观察、可恢复、可部署”。

任务：

- 增加 SQLite session 级记忆，不做长期复杂记忆。
- 日志统一格式、级别、轮转策略。
- 日志脱敏：不记录完整 Authorization、API Key、Secret、Token、完整请求体/响应体。
- 用户 ASR 文本默认只记录摘要或前 50 字，完整调试文本只能由环境变量开启。
- 增加 `/health` 或等价 healthcheck。
- Docker healthcheck 检查服务可连接性。

验收：

- Docker healthcheck 能判断服务是否可用。
- 日志中只出现 `token=SET/EMPTY` 或脱敏片段。
- 会话级历史可写入并读取最近上下文。

### P5：ESP32 瘦客户端协议适配

目标：让 ESP32 只承担音频 I/O、WebSocket、TFT、按键、本地降级，不再承担云端 AI 能力。

任务：

- WebSocket 连接携带 Token、device-id、client-id。
- 发送 `hello` / `listen` / 状态类 JSON 控制消息。
- 上传 Opus binary frame。
- 接收服务端 binary Opus frame 并播放。
- 处理 `stt`、`sentence_*`、`tts_*`、`error` JSON。
- BOOT 按键进入 `wakeup_detected` 或 `listening`，作为阶段一至八联调入口。

验收：

- ESP32 可连接后端并完成 hello。
- BOOT 触发录音，音频能上传到后端。
- 后端 TTS binary Opus 能在 MAX98357A 播放。
- TFT 能显示 idle/listening/thinking/speaking/error/reconnecting。

### P6：ESP32 旧架构清理与本地降级

目标：减少长期维护负担，移除会把项目拖回旧方案的路径。

任务：

- 移除或彻底禁用旧 base64 TTS audio JSON 解析。
- 移除或弱化本地 Baidu ASR/TTS provider。
- 移除 bridge / MimicLaw Agent / context_builder 对主链路的依赖。
- 保留必要的音频、显示、按键、WiFi、WebSocket、硬件抽象。
- 实现网络断开或后端不可用时的本地 Opus 降级提示音。

验收：

- 固件主链路不调用本地 ASR/TTS API。
- 固件主链路不依赖 MimicLaw Agent。
- 搜索不到可被主链路触发的 base64 TTS 下发播放路径。
- 断网或后端不可用时能本地提示并回到可重连状态。

### P7：端到端联调与异常恢复

目标：把后端和 ESP32 作为一个系统验证，而不是分别“看起来能跑”。

任务：

- ESP32 BOOT → 录音 → WS binary → ASR → LLM → TTS → WS binary → 播放 → idle。
- 验证断线重连。
- 验证 ASR 空文本。
- 验证 LLM 超时和重试。
- 验证 TTS 失败。
- 验证声音太短或能量过低。
- 验证单轮总 timeout。
- 连续多轮对话观察内存、连接、音频漂移。

验收：

- 至少连续 10 轮对话不出现卡死。
- 每个异常场景都能回到 `idle` 或 `reconnecting`。
- 进度文档记录测试日期、设备端口、后端配置摘要和结果。

### P8：第一版增量模块：自定义 Skill 与 Agent 循环

目标：在主链路稳定后，再加可控但可选择放宽限制的自定义 Skill 能力。Skill 只运行在后端，不运行在 ESP32。

设计原则：

- 支持本地自定义 Skill。
- 支持 OpenClaw / agent 项目风格的 `SKILL.md` 元数据兼容。
- 支持 GitHub Skill 导入，但默认先进入 pending 目录。
- 不直接移植完整第三方 agent runtime，只做 XiaoClawBrain 后端 Skill 兼容层。
- Skill 安全等级可选：`high`、`low`、`off`。
- 支持全局默认 Skill，可由后端配置选择每轮默认运行，用于持续生效的能力；未被选中的 Skill 只按需运行。
- 按需 Skill 第一版通过显式触发和保守关键词匹配判断是否运行；LLM 自动选择 Skill 默认关闭。

任务：

- `skill_loader.py`：Skill 子进程隔离加载。
- `skill_plugin.py`：Skill 接口与注册。
- `agent_loop.py`：ASR → Skill 匹配 → LLM → TTS 调度。
- `skill_security.py` 或等价配置层：实现 `SKILL_SECURITY_LEVEL=high|low|off`。
- `skill_global.py` 或等价调度层：实现后端 `global_defaults.selected` 选择的全局默认 Skill。
- `high` 默认：timeout 5 秒、limited_env、cwd 限制、stdout 64KB、stderr 写日志、不暴露完整密钥。
- `low` 开发/实验：timeout/env/stdout/network 可按配置放宽，允许用户自己的实验 Skill 运行。
- `off` 用户自担：不主动限制 Skill 执行，可由用户决定是否运行或先调用安全审查类 Skill。
- GitHub Skill 导入：默认下载到 pending，按安全等级和用户确认启用。

验收：

- 未命中 Skill 时正常走 LLM 对话。
- 命中简单 Skill 时可返回结果并进入 TTS。
- 后端可选择哪些 Skill 全局默认运行，哪些只按需运行；全局默认 Skill 能在普通 LLM 前运行并注入上下文，失败时默认不打断对话。
- 按需 Skill 能通过 `explicit_only` / `explicit_or_keywords` 触发；`llm_tool_call` 仅保留字段。
- `high` 模式下，恶意或超时 Skill 不影响主服务稳定性。
- `low` / `off` 模式下，日志明确记录安全等级，用户能确认当前风险。
- GitHub Skill 不会在未确认时自动启用。

### P9：ESP32 本地语音唤醒，产品闭环必做

目标：从技术联调形态进入产品交互形态。

任务：

- 评估 ESP-SR / WakeNet / 自定义轻量唤醒词与当前音频链路兼容性。
- ESP32 本地持续检测唤醒词，不把唤醒检测放到后端持续 ASR。
- 唤醒后进入 `idle → wakeup_detected → listening → uploading_audio`。
- 实现或预留 0.5-1 秒环形音频缓冲。
- 记录误唤醒、低置信度、阈值调优信息。
- 保留 BOOT 按键作为调试/备用入口。

验收：

- 本地唤醒词可触发对话链路。
- 唤醒后用户第一句话开头不被明显吞掉。
- 低置信度唤醒不会进入正式对话。
- 未完成 P9 前，项目不能标记为产品级闭环完成。

### P10：后续升级候选，可选连续对话与记忆策略升级

目标：在 P9 稳定验收之后，再为系统增加“默认单轮、后台可选连续对话”的可配置交互模式，并同步收口短期/长期记忆在长连接会话下的保存与查询策略。

任务：

- 增加后端会话模式配置：`single_turn` / `continuous_followup`。
- 默认模式保持当前单轮收口：`tts_end -> idle`。
- 连续对话模式下，在 `tts_end` 后进入 follow-up 等待窗口，而不是立刻回 `idle`。
- 定义连续对话结束条件，例如：
  - follow-up 超时无人说话
  - 用户说出“结束对话 / 不用了 / 先这样”
  - 极端超时兜底
- 补齐设备端状态表现，必要时新增 `followup_wait` 或等价状态显示。
- 处理连续对话下的 TTS 尾音误触发、VAD 误判、wake word 与 follow-up 模式切换边界。
- 明确记忆保存策略：
  - 每轮结束是否增量保存
  - 连续会话结束时是否强制保存
  - 长连接会话是否需要定期 flush
- 明确记忆查询策略：
  - 单轮模式如何取短期/长期记忆
  - 连续模式如何复用会话上下文与外部记忆

验收：

- 单轮模式保持当前行为，不因新增连续模式而回归。
- 连续对话模式下，至少连续 5 轮 follow-up 不需要重复唤醒词。
- 连续模式在超时、静音、结束词和异常场景下都能稳定回到 `idle`。
- 连续模式下记忆保存与查询行为可解释、可验证，不出现“长聊后完全未落记忆”的情况。

---

## 推荐开工顺序

1. 先完成当前 P9 多轮实机复测与稳定性收尾。
2. 确认第一版稳定版本口径并完成验收记录。
3. 如需继续增强交互体验，再启动 P10 可选连续对话与记忆策略升级。
2. 再执行 P2 和 P3，补齐后端协议硬化、鉴权、timeout、错误协议。
3. 然后执行 P5 和 P6，把 ESP32 改成真正瘦客户端。
4. 之后执行 P7，做端到端和异常恢复验证。
5. P8 在主链路稳定后做。
6. P9 在产品验收前必须做，不得用按键触发替代。

---

## 每阶段文档要求

- 每完成一个任务，立即倒序更新 `项目开发进度_XiaoClawBrain.md`。
- 修改协议、阶段拆分、错误码、状态机、部署方式时，同步更新 `START_HERE_项目速览.md`。
- 修改架构级决策时，同步更新架构方案文档。
- 遇到新问题并解决后，记录到 `开发问题解决记录.md`。
- 不记录完整密钥、Token、Authorization header、API 请求体/响应体。

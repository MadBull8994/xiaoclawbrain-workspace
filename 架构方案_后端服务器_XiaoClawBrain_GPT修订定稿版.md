# 后端服务器方案：XiaoClawBrain

创建时间：2026-05-16  
最后更新：2026-06-01  
状态：修订定稿版，按 P0-P9 路线实施  
基准仓库：`https://github.com/MadBull8994/xiaozhi-esp32-server`  
锁定 commit：`fb7e2e17a8340232155d35bb2f988522d3b3232b`

---

## 0. 本次修订摘要

本版在原方案基础上做以下关键修订：

1. **TTS 音频下发改为 WebSocket 二进制小包**，禁止通过 base64 大 JSON 下发音频。
2. **明确单帧大小限制**：Opus binary frame 建议 512 到 1024 bytes，最大不超过 2048 bytes。
3. **以用户 fork 仓库为项目基准**：`MadBull8994/xiaozhi-esp32-server`。
4. **锁定具体 commit**，不再使用“最新版本”这种漂浮表述。
5. **Skill 安全描述调整为“可配置安全等级 + 子进程隔离机制”**，默认高安全，但允许用户选择低安全或关闭限制来运行自定义/实验 Skill。
6. **第一版暂缓后端 Cron**，简单提醒可先使用 ESP32 本地 timer。
7. **API timeout 和错误协议进入第一版**，不再放到后续迭代。
8. **补充基础状态机和常见异常处理**。
9. **日志系统、Docker healthcheck、本地 Opus 降级音频、SQLite 记忆进入第一版**。
10. **ASR 空文本第一次即返回“没听清”**，不进入 LLM。
11. **补充语音唤醒路线**：阶段一至阶段八允许按键触发联调，阶段九必须实现 ESP32 本地语音唤醒。

---

## 1. 决策背景

ESP32-S3 端经过大量诊断发现，ESP-IDF v5.5.2 的 mbedTLS 层在接收大 HTTP 响应体（>4KB）时会触发 `MBEDTLS_ERR_SSL_INVALID_RECORD` 错误。此问题跨 WebSocket 和 HTTP POST 两种模式、跨 Baidu REST API 和流式 API，属于底层通信稳定性风险，应用层不宜继续硬扛。

因此采用“小智生态”的上游架构：**ESP32 瘦客户端 + 后端服务器胖服务端**。

ESP32 只做：

- 麦克风采集
- Opus 编码
- WebSocket 小包传输
- Opus 解码
- 功放播放
- TFT 状态显示
- 按键输入
- 本地语音唤醒，阶段九必做
- WiFi 与连接状态指示
- 本地降级提示音

后端服务器负责：

- WebSocket 连接管理
- 设备鉴权
- ASR
- LLM
- TTS
- Skill 引擎
- Agent 循环
- 记忆系统
- 日志系统
- 错误处理
- 超时控制

---

## 2. 为什么选择 xiaozhi-esp32-server 做底座

`xiaozhi-esp32-server` 是 xiaozhi-esp32 生态中成熟度较高的 Python 后端，支持多种 ASR、TTS、LLM、MCP、插件、设备管理等能力。

本项目不重写底座，而是在其上做最小增量改造。

### 2.1 项目基准仓库

后续开发以用户 fork 仓库为准：

```text
本项目基准仓库：
https://github.com/MadBull8994/xiaozhi-esp32-server

Fork 来源：
https://github.com/xinnan-tech/xiaozhi-esp32-server

锁定 commit：
fb7e2e17a8340232155d35bb2f988522d3b3232b

锁定日期：
2026-05-16

开发分支：
xiaoclawbrain-v0.1
```

### 2.2 初始化命令

```bash
git clone https://github.com/MadBull8994/xiaozhi-esp32-server.git
cd xiaozhi-esp32-server
git checkout fb7e2e17a8340232155d35bb2f988522d3b3232b
git checkout -b xiaoclawbrain-v0.1
git push -u origin xiaoclawbrain-v0.1
```

### 2.3 版本锁定策略

- 上游代码锁定到具体 commit。
- 所有 Python 依赖在 `requirements.txt` 中 pin 到具体版本号。
- 后续所有改造都在 `xiaoclawbrain-v0.1` 分支进行。
- 不直接追随上游 `main`。
- 如果未来需要合并上游更新，必须单独开分支测试，确认 WebSocket 协议、音频链路、Skill 插件、配置项未被破坏后再合并。

---

## 3. 第一版目标与边界

### 3.1 第一版必须完成

第一版目标是先把“稳定听、稳定想、稳定说、异常能恢复”做出来。

```text
ESP32-S3 只负责：
- 录音
- Opus 编码
- WebSocket 小包上传
- Opus 小包接收
- Opus 解码播放
- TFT 状态显示
- 按键输入
- 本地 Opus 降级提示音

后端服务器负责：
- WebSocket 连接管理
- Token 鉴权
- ASR
- LLM
- TTS
- Opus 小包下发
- API timeout
- 错误协议
- 基础状态机
- SQLite 记忆
- 日志系统
- Docker healthcheck
```

### 3.2 第一版暂缓

以下能力不进入第一版，避免一次性复杂度过高：

```text
- 后端 Cron 调度
- 本地语音唤醒词，阶段一至阶段八暂缓，但阶段九/产品验收前必须完成
- 后台可选连续对话模式（默认单轮；连续对话作为 P10 后续升级候选）
- 复杂 Skill 市场，P8 只做后端 Skill 兼容层和用户确认后的导入
- 用户打断 AI
- TTS 播放中重新拾音
- OTA
- 多设备管理后台
- 复杂长期记忆整理
- 大规模 MCP 工具接入
```

连续对话与记忆策略升级记录为后续候选方向：

```text
P10 候选升级：
- 默认保持单轮对话
- 后台可选连续对话模式
- 连续模式下补齐 follow-up 超时、结束词、状态机收口
- 同步补齐短期/长期记忆在长连接会话中的保存与查询策略
```

---

## 4. 总体架构

```text
┌──────────── ESP32-S3：纯终端 ────────────┐      ┌──────── 后端服务器：Python ────────┐
│                                          │      │                                    │
│  INMP441 → I2S → PCM16 16kHz             │ Opus │  底座：xiaozhi-esp32-server         │
│  Opus 编码 → WS binary 小包上传           │──WS─→│  ├── WebSocket 连接管理             │
│  WS binary 小包接收 → Opus 解码           │←WS──│  ├── Token 鉴权                     │
│  PCM → I2S → MAX98357 播放                │      │  ├── ASR                            │
│  ST7735 TFT 显示                         │ JSON │  ├── LLM                            │
│  BOOT / VOL+ / VOL- 按键                  │←WS─→│  ├── TTS                            │
│  本地语音唤醒，阶段九必做                  │      │  ├── Opus 编码与小包下发
│  WiFi STA/AP                             │      │            │
│  本地 Opus 降级提示音                     │      │  ├── SQLite 记忆                    │
│                                          │      │  ├── 日志系统                       │
│  ESP32 保留：                            │      │  ├── timeout 与错误协议             │
│  - audio/ 编解码管线                      │      │  └── healthcheck                    │
│  - display/ TFT 驱动                     │      │                                    │
│  - protocols/ WS 连接                    │      │  增量模块：                         │
│  - boards/ 硬件抽象                      │      │  ├── skill_loader.py                │
│  - fallback/ 本地降级音频
- wakeup/ 本地语音唤醒，阶段九新增或接入                 │      │  ├── skill_plugin.py                │
│                                          │      │  └── agent_loop.py                  │
│  ESP32 删除或弱化：                       │      │                                    │
│  - 本地 ASR/TTS provider                  │      │  后续模块：                         │
│  - mimiclaw Agent                         │      │  └── cron_scheduler.py              │
│  - bridge 层                              │      │                                    │
└──────────────────────────────────────────┘      └────────────────────────────────────┘
```

---

## 5. WebSocket 通信协议与鉴权

### 5.1 设备鉴权

第一版采用单设备 Token 鉴权。ESP32 固件中写入设备 Token，连接时放在 WebSocket 握手 HTTP Header 中：

```http
GET /ws HTTP/1.1
Authorization: Bearer <DEVICE_TOKEN>
```

服务端在握手阶段验证 Token，不匹配则拒绝连接并返回 401。

`.env` 中配置：

```dotenv
DEVICE_AUTH_MODE=single
DEVICE_TOKEN=xxx
```

### 5.2 后续设备鉴权演进

第一版可以硬编码 Token，但文档中提前预留第二版升级路径：

```text
第一版：
- 单设备 DEVICE_TOKEN
- 固件编译时写入
- 如需更换，重新编译烧录

第二版：
- 每台设备独立 device_id
- 每台设备独立 token
- 服务端存 token_hash，不存明文 token
- 支持禁用设备
- 支持 last_seen
```

建议后续数据库表：

```sql
CREATE TABLE devices (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    device_id TEXT UNIQUE NOT NULL,
    token_hash TEXT NOT NULL,
    name TEXT,
    enabled INTEGER DEFAULT 1,
    last_seen_at TEXT,
    created_at TEXT NOT NULL
);
```

---

## 6. 通信协议：控制 JSON + 二进制 Opus 小包

### 6.1 核心原则

为规避 ESP32 大包稳定性问题，第一版强制执行：

```text
- 禁止通过 base64 大 JSON 下发 TTS 音频
- TTS 音频必须通过 WebSocket binary frame 连续小包下发
- 单个 binary frame 建议 512 到 1024 bytes
- 单个 binary frame 最大不超过 2048 bytes
- 禁止发送超过 4KB 的 JSON 消息
- 长文本回复按句子分段发送
```

### 6.2 ESP32 → Server

开始监听：

```json
{"type":"listen","state":"start","mode":"auto"}
```

停止监听：

```json
{"type":"listen","state":"stop"}
```

音频上传：

```text
WebSocket Binary Frame:
[Opus audio frame <= 1024 bytes]
```

### 6.3 Server → ESP32

ASR 识别结果：

```json
{"type":"stt","text":"你好"}
```

LLM 开始回复：

```json
{"type":"sentence_start","text":"我来想一下。"}
```

LLM 分句回复：

```json
{"type":"sentence_delta","text":"今天可以先做语音链路联调。"}
```

LLM 回复结束：

```json
{"type":"sentence_end"}
```

状态同步：

```json
{"type":"state","state":"thinking"}
```

TTS 开始：

```json
{"type":"tts_start","codec":"opus","sample_rate":16000}
```

TTS 音频：

```text
WebSocket Binary Frame:
[Opus audio frame <= 1024 bytes]
```

TTS 结束：

```json
{"type":"tts_end"}
```

错误：

```json
{
  "type": "error",
  "code": "LLM_TIMEOUT",
  "message": "我这边有点卡住了，稍后再试试。"
}
```

### 6.4 明确废弃的旧格式

以下格式禁止在第一版使用：

```json
{"type":"tts","audio":"<base64_opus>"}
```

原因：

- base64 会膨胀音频体积。
- 大 JSON 容易超过 4KB。
- JSON 解析和内存分配压力更高。
- 不利于边生成边播放。

---

## 7. 交互入口与语音唤醒策略

语音唤醒是产品必做能力，但为了降低第一轮开发风险，采用分阶段策略。

### 7.1 阶段一至阶段八：按键触发作为联调入口

阶段一至阶段八优先验证完整主链路：

```text
按键触发 → 录音 → Opus 上传 → ASR → LLM → TTS → Opus 下发 → 播放
```

规则：

```text
- BOOT 按键可触发一次语音输入
- 用户说话停止后等待 5 秒，无新语音则进入 ASR
- ASR_EMPTY 第一次就提示“我没听清，你再说一遍”
- 按键触发只作为联调和备用入口，不代表最终产品交互完成
```

### 7.2 阶段九：本地语音唤醒，产品必做

语音唤醒必须在 ESP32 本地完成，后端不负责持续监听唤醒词。

原因：

```text
- 后端持续流式唤醒会增加网络依赖和延迟
- 网络断开时无法唤醒
- 持续上传环境音有隐私风险
- ESP32-S3 + 本地唤醒模型更符合终端产品形态
```

后续候选方案：

```text
- ESP-SR / WakeNet
- 自定义轻量唤醒词模型
- 先用官方示例验证 INMP441 + I2S 输入链路，再接入当前工程
```

### 7.3 语音唤醒状态流

阶段九必须支持：

```text
idle
→ wakeup_detected
→ listening
→ uploading_audio
→ recognizing
→ thinking
→ synthesizing
→ speaking
→ idle
```

### 7.4 环形音频缓冲

为避免唤醒词命中后吞掉用户第一句话开头，ESP32 端需要预留或实现环形音频缓冲：

```text
- 缓存唤醒前 0.5-1 秒 PCM 音频
- 唤醒成功后，把缓冲区尾部与后续录音拼接
- 再进入 Opus 编码与上传流程
```

### 7.5 误唤醒处理

第一版语音唤醒增强阶段至少需要：

```text
- 唤醒置信度阈值
- 低置信度丢弃
- 连续误唤醒记录
- TFT 或日志可观察唤醒事件
```

### 7.6 明确禁止

```text
- 禁止把唤醒词检测放到后端持续流式 ASR
- 禁止用后端 Cron 或轮询模拟语音唤醒
- 禁止把按键触发当成最终产品交互完成标准
```

---

## 8. 基础状态机

### 7.1 状态列表

```text
idle                空闲
wakeup_detected     本地唤醒词命中或按键触发后进入
listening           正在监听
uploading_audio     正在上传音频
recognizing         后端 ASR 识别中
thinking            后端 LLM 思考中
synthesizing        后端 TTS 合成中
speaking            ESP32 播放中
error               错误状态
reconnecting        重连中
```

### 7.2 第一版状态流

```text
idle
→ wakeup_detected
→ listening
→ uploading_audio
→ recognizing
→ thinking
→ synthesizing
→ speaking
→ idle
```

异常时：

```text
任意状态
→ error
→ idle
```

网络断开时：

```text
任意状态
→ reconnecting
→ idle 或 listening
```

### 7.3 用户停顿规则

用户说话过程中，如果检测到声音停止：

```text
声音停止后等待 5 秒
如果 5 秒内没有新语音，进入下一步
如果 5 秒内继续说话，继续录音
```

### 7.4 第一版不处理的交互

以下情况在主链路第一轮先不处理，避免音频链路复杂化。语音唤醒按阶段九单独实现：

```text
- 用户打断 AI
- 后端已经开始 TTS 时用户又说话
- TTS 播放中重新拾音
```

第一版策略：

```text
speaking 状态下不处理用户新语音
播放结束后回到 idle
```

---

## 9. 常见异常处理规则

### 8.1 用户声音太短

```text
录音小于 500ms 或音频能量过低：
- 丢弃
- 不进入 ASR
- 回 idle
```

### 8.2 ASR 识别为空

用户要求：第一次空文本就提示“没听清”。

```text
ASR 返回空文本：
- 第一次即返回“没听清”
- 不进入 LLM
- 播放或显示提示后回 idle
```

建议错误协议：

```json
{
  "type": "error",
  "code": "ASR_EMPTY",
  "message": "我没听清，你再说一遍。"
}
```

建议处理：

```text
在线状态：
- 优先由后端 TTS 播放“我没听清，你再说一遍。”

离线状态：
- 不走后端
- 由 ESP32 本地 Opus 降级音频处理网络/后端断开类提示
```

### 8.3 ASR 识别太短但可能有效

例如：

```text
好、对、嗯、是、否、不、停、停止、继续、不要
```

规则：

```text
小于 2 个字但在白名单中：保留
小于 2 个字且不在白名单中：丢弃或提示没听清
```

### 8.4 后端处理完但 ESP32 播放失败

```text
ESP32 播放失败：
- 停止本轮
- 记录错误
- 回 idle
```

### 8.5 单轮对话总耗时过长

```text
单轮对话总 timeout：35 秒
超过后强制结束
ESP32 回 idle
后端记录 timeout
```

---

## 10. API 超时与最小重试机制

API timeout 进入第一版，不放到后续迭代。

### 9.1 timeout 建议

```text
ASR timeout：5 秒
LLM timeout：20 秒
TTS timeout：8 秒
WebSocket idle timeout：30 秒
单轮总 timeout：35 秒
```

### 9.2 重试策略

第一版保持克制：

```text
ASR：不重试
LLM：失败可重试 1 次
TTS：不重试
WebSocket：断线自动重连
```

### 9.3 错误码建议

```text
ASR_EMPTY           ASR 识别为空
ASR_TIMEOUT         ASR 超时
LLM_TIMEOUT         LLM 超时
LLM_ERROR           LLM 调用失败
TTS_TIMEOUT         TTS 超时
TTS_ERROR           TTS 调用失败
WS_AUTH_FAILED      Token 鉴权失败
WS_DISCONNECTED     WebSocket 断开
AUDIO_DECODE_ERROR  ESP32 音频解码失败
UNKNOWN_ERROR       未知错误
```

错误 JSON：

```json
{
  "type": "error",
  "code": "ASR_TIMEOUT",
  "message": "我这边没听清，你再说一遍。"
}
```

---

## 11. 本地降级音频

ESP32 保留一条最小降级路径：在 Flash 中预存短 Opus 音频，不依赖网络。

### 10.1 降级音频格式

优先使用：

```text
预编码 Opus
```

不建议优先使用 PCM，原因：

- PCM 体积更大。
- 占用 Flash 更多。
- 项目本身已有 Opus 解码链路。

### 10.2 触发场景

| 触发场景 | 音频内容 | 触发条件 |
|---|---|---|
| WiFi 未连接 / 连接断开 | 网络已断开 | `WIFI_EVENT_STA_DISCONNECTED` |
| 后端 WebSocket 连接失败 | 远程服务已断开 | WS 握手失败 / 连接超时 / 收到 401 |

### 10.3 不做语音提醒的情况

用户确认：网络断开后重连不需要语音提醒，板子上有指示灯即可。

```text
网络断开：
- 指示灯提示
- 必要时播放一次本地降级音频

网络恢复：
- 指示灯恢复
- 不需要语音提醒
```

---

## 12. Skill 系统

Skill 系统放在后端，不放在 ESP32。ESP32 只负责采集、播放、显示和连接；ASR 文本进入后端后，由 Agent 循环判断是否调用 Skill。

本项目支持后续接入 OpenClaw / agent 项目风格的 Skill 思路，但不直接搬入完整第三方 runtime。第一版做“后端 Skill 兼容层”：

```text
ASR text
→ Agent loop
→ 全局默认 Skill（可选，每轮运行）
→ Skill matcher
→ skill_loader
→ 子进程执行本地或导入的 Skill
→ 结构化结果
→ LLM 组织回复
→ TTS
→ WebSocket binary Opus 下发
```

第一版优先支持：

```text
- 本地自定义 Skill
- 手动放入 skills/<skill_name>/ 的 Skill
- OpenClaw 风格 SKILL.md 元数据兼容
- Python 子进程 Skill
- 全局默认 Skill，可配置为每轮对话运行
- GitHub Skill 导入到 pending 目录，默认不自动启用
```

后续再考虑：

```text
- JS / TS / Node 子进程 Skill
- 远程 Skill 源管理
- Skill 版本、签名、依赖隔离
- Skill 市场
```

### 11.1 目录结构

```text
skills/
├── weather/
│   └── SKILL.md
├── reminder/
│   ├── SKILL.md
│   └── scripts/set_reminder.py
├── memory/
│   └── SKILL.md
├── chat/
│   └── SKILL.md
└── custom/
    └── SKILL.md
```

### 11.2 SKILL.md 格式

```yaml
---
name: weather
description: 查询天气信息
tools: [get_weather, get_forecast]
triggers: [天气, 温度, 下雨]
---
# 天气查询技能
当用户询问天气时，调用工具获取数据并用自然语言回复。
```

### 11.3 Skill 安全等级

Skill 安全不是单一开关，而是用户可选等级。默认使用 `high`，但允许为自定义或实验 Skill 选择 `low` 或 `off`。

推荐配置：

```dotenv
SKILL_SECURITY_LEVEL=high
SKILL_ENABLE_GITHUB_IMPORT=false
SKILL_REQUIRE_REVIEW=true
```

#### high：高安全，默认

用于生产环境和未知来源 Skill。

```text
- 子进程执行，不直接 import / exec 未知脚本
- timeout 默认 5 秒
- cwd 限制到当前 skill 目录
- limited_env，不传完整系统环境
- 不向 Skill 传递 API Key、Secret、Token
- 不向 Skill 暴露 .env
- stdout 最大 64KB
- stderr 记录到 skill.log，不直接返回给用户
- 非 0 退出码视为 Skill 调用失败
- GitHub Skill 只能导入到 pending 目录
- GitHub Skill 需要人工审查或 allowlist 后才能启用
```

#### low：低安全，开发/实验

用于用户自己写的 Skill、实验 Skill、需要访问本机资源的 Skill。此模式放宽限制，但仍保留最小的进程边界和可观测性。

```text
- 仍通过子进程执行
- timeout 可配置，默认 30 秒，可按 Skill 单独放宽
- cwd 默认仍为 skill 目录，但允许配置扩展工作目录
- env 可选择继承部分系统环境
- 可按 allowlist 传入指定环境变量
- stdout 上限可配置，默认 256KB
- 允许网络访问
- GitHub Skill 可导入后由用户手动启用
- 启用时明确记录：security_level=low
```

low 模式仍不建议默认传递完整 `.env`。如果某个 Skill 必须使用密钥，应通过显式 allowlist 传入指定变量，而不是整包暴露。

#### off：关闭限制，用户自担

用于完全信任的本地实验。此模式不做运行时限制，是否执行由用户决定，也可以先调用安全审查类 Skill 做检查。

```text
- 不强制 timeout
- 不强制 limited_env
- 不强制 cwd 限制
- 不强制 stdout / stderr 限制
- 可直接运行用户指定 Skill 入口
- GitHub Skill 可由用户确认后直接启用
- 系统必须明确记录：security_level=off
```

off 模式只表示 XiaoClawBrain 不主动限制 Skill；操作系统、容器、文件权限和网络环境仍可能存在外部限制。

### 11.4 Skill 子进程隔离机制

在 `high` 和 `low` 模式下，不再称为“安全沙箱”，改称为：

```text
Skill 子进程隔离机制
```

因为 subprocess 隔离比 `import` / `exec()` 更安全，但不是严格沙箱。Skill 脚本仍可能访问文件、环境变量、网络或消耗 CPU，因此默认使用 `high`。

执行方式：

```python
subprocess.run(
    ["python", script_path],
    input=json.dumps(payload),
    capture_output=True,
    text=True,
    timeout=5,
    cwd=skill_dir,
    env=limited_env,
)
```

### 11.5 Skill 执行限制

第一版必须实现安全等级配置，并让每次调用日志能看出当前等级。

`high` 模式必须实现：

```text
- timeout = 5 秒
- cwd 限制到当前 skill 目录
- limited_env，不传完整系统环境
- 不向 Skill 传递 API Key
- 不向 Skill 暴露 .env
- stdout 最大 64KB
- stderr 记录到 skill.log，不直接暴露给用户
- 非 0 退出码视为 Skill 调用失败
```

`low` 模式必须实现：

```text
- timeout 可配置，默认 30 秒
- env allowlist 可配置
- stdout 上限可配置，默认 256KB
- GitHub Skill 需要用户手动启用
- 日志记录 security_level=low
```

`off` 模式必须实现：

```text
- 不主动限制 Skill 执行
- 用户确认后运行
- 日志记录 security_level=off
- UI / CLI / 配置中必须明确提示风险
```

生产期建议：

```text
- 生产环境默认 high
- 未知来源 Skill 默认不能自动启用
- 自定义 Skill 可选择 high / low / off
- 可增加安全审查 Skill，对 GitHub Skill 做人工启用前检查
- 后续可考虑 Docker / firejail / nsjail 级别隔离
```

---

## 13. Agent 循环

第一版 Agent 循环只做必要编排，不做过重的自主规划。

### 12.1 第一版能力

```text
- 接收 ASR 文本
- 判断是否普通对话
- 判断是否需要调用 Skill
- 调用 LLM
- 生成回复文本
- 交给 TTS
```

### 12.2 第一版不做

```text
- 多轮复杂任务拆解
- 后台长期任务
- 多工具并行调用
- 深度自我反思循环
```

### 12.2.1 全局默认 Skill

P8 增加全局默认 Skill 能力，用于每轮对话都需要持续生效的技能，例如固定人格上下文、设备状态摘要、用户偏好整理、输入规范化或业务规则注入。

最终是否全局默认运行必须由后端配置显式选择。`xiaoclaw.skill.json` 里的 `auto_run.enabled=true` 只表示该 Skill 允许被配置为全局默认运行；如果后端没有选中，它仍然只能按需运行。

全局默认 Skill 不是后台常驻进程，而是在每轮有效 ASR 文本进入 Agent loop 后，按配置阶段运行一次。第一版优先支持 `before_llm` 阶段：全局 Skill 返回 `context`，由 Agent loop 注入后续 LLM 输入；默认不影响 fast path，也不直接抢答。

```text
auto_run.enabled=true
scope=global
phase=before_llm
failure_policy=continue
max_runs_per_turn=1
```

后端选择示例：

```yaml
skills:
  global_defaults:
    enabled: true
    selected:
      - name: persona-context
        phase: before_llm
        order: 10
        failure_policy: continue
  on_demand:
    enabled: true
    allow_all_enabled: false
    selected:
      - name: echo
        trigger_mode: explicit_only
      - name: device-status
        trigger_mode: explicit_or_keywords
```

约束：

```text
- 只有 global_defaults.selected 中列出的 Skill 会每轮默认运行
- 未被选中的 Skill 即使声明 auto_run.enabled=true，也只按需运行
- 按需 Skill 第一版支持 explicit_only 和 explicit_or_keywords
- llm_tool_call 只保留字段，默认关闭
- 默认 high 安全等级
- 单个全局 Skill timeout 默认 3 秒
- 单轮所有全局 Skill 总预算默认 5 秒
- 单轮全局 Skill 数量默认最多 3 个
- 默认失败策略为 continue，失败只写日志，不打断普通对话
- 默认 include_fast_path=false
```

### 12.3 建议流程

```text
ASR text
→ intent detect
→ run global before_llm skills if configured
→ if explicit skill matched: call skill
→ else if keyword skill matched: call skill
→ else: call LLM
→ response text
→ TTS
→ Opus small frames
→ ESP32 play
```

---

## 14. Cron 调度策略

### 13.1 第一版暂缓后端 Cron

用户确认：ESP32 自己有定时功能，第一版可先用 ESP32 本地 timer。

第一版：

```text
简单本地提醒：ESP32 timer
后端 cron_scheduler.py：暂缓
```

### 13.2 ESP32 本地 timer 适合

```text
- 倒计时
- 简单闹钟
- 简单提醒提示音
```

### 13.3 后端 Cron 后续适合

```text
- 记忆整理
- 系统检查
- 联网查询后提醒
- 跨设备同步
- 长期任务
- 日程类复杂提醒
```

### 13.4 注意事项

ESP32 本地 timer 的限制：

```text
- 断电后可能丢失
- 重启后可能丢失
- 跨设备不同步
- 无法做复杂联网条件判断
```

因此第一版可用，产品化时再迁移到后端持久化提醒。

---

## 14. 记忆系统

第一版直接采用 SQLite，不使用 JSON 作为主记忆存储。

### 14.1 为什么用 SQLite

```text
- 支持并发读写
- 不容易因写入中断损坏整份数据
- 查询方便
- 后续迁移方便
- 适合做用户偏好、历史对话摘要、设备状态
```

### 14.2 建议数据表

```sql
CREATE TABLE memories (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    memory_type TEXT NOT NULL,
    content TEXT NOT NULL,
    importance INTEGER DEFAULT 1,
    source TEXT,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL
);
```

### 14.3 第一版记忆范围

```text
- 用户偏好
- 设备配置
- 简短对话摘要
- Skill 调用结果摘要
```

第一版不做复杂记忆整理，后续由后端 Cron 或独立任务处理。

---

## 15. 日志系统

日志系统进入第一版。没有日志，后期联调就像在黑箱里听海螺。

### 15.1 日志分类

```text
logs/
├── device.log          设备连接、断开、鉴权失败、重连
├── conversation.log    ASR 文本、LLM 摘要、TTS 状态
├── error.log           API 错误、timeout、异常栈
├── skill.log           Skill 调用、返回、耗时
└── system.log          启动、配置加载、healthcheck 状态
```

### 15.2 日志原则

```text
- 不记录完整 API Key
- 不记录 DEVICE_TOKEN 明文
- 不记录完整敏感用户隐私
- conversation.log 优先记录摘要
- error.log 可记录异常栈，但注意脱敏
- 日志文件需要轮转，避免撑满磁盘
```

### 15.3 日志轮转建议

Python 可使用：

```python
logging.handlers.RotatingFileHandler(
    filename="logs/error.log",
    maxBytes=10 * 1024 * 1024,
    backupCount=5
)
```

---

## 16. Docker 环境一致性

使用 Docker 消除 Mac Mini（ARM）和云服务器（x86 Linux）之间的环境差异，避免 `pyaudio`、`opuslib` 等音频库在不同平台行为不一致。

### 16.1 Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

RUN apt-get update && apt-get install -y \
    libopus-dev \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "main.py"]
```

### 16.2 docker-compose.yml

开发期：

```yaml
version: "3.9"

services:
  brain:
    build: .
    ports:
      - "8888:8888"
    env_file:
      - .env
    restart: "no"
    volumes:
      - ./data:/app/data
      - ./logs:/app/logs
    healthcheck:
      test: ["CMD", "python", "-c", "import socket; s=socket.create_connection(('127.0.0.1',8888),2); s.close()"]
      interval: 30s
      timeout: 5s
      retries: 3
```

生产期可改为：

```yaml
restart: unless-stopped
```

### 16.3 healthcheck 的作用

Docker 默认只能知道“进程是否还活着”，但进程活着不代表服务可用。

healthcheck 用来检测：

```text
- WebSocket 端口是否可连接
- 服务是否启动完成
- 主循环是否卡死
- 容器是否处于假 running 状态
```

后续更好的方式是增加 HTTP 健康接口：

```text
GET /health
```

返回：

```json
{"status":"ok"}
```

第一版先用端口连接检测即可。

---

## 17. API Key 与密钥管理

所有密钥通过环境变量管理，不写入代码或 `config.yaml`。

### 17.1 .env

```dotenv
# 百度云
BAIDU_ASR_APP_ID=xxx
BAIDU_ASR_API_KEY=xxx
BAIDU_ASR_SECRET_KEY=xxx
BAIDU_TTS_APP_ID=xxx
BAIDU_TTS_API_KEY=xxx
BAIDU_TTS_SECRET_KEY=xxx

# DeepSeek
DEEPSEEK_API_KEY=xxx

# 设备鉴权
DEVICE_AUTH_MODE=single
DEVICE_TOKEN=xxx
```

### 17.2 .env.example

```dotenv
BAIDU_ASR_APP_ID=
BAIDU_ASR_API_KEY=
BAIDU_ASR_SECRET_KEY=
BAIDU_TTS_APP_ID=
BAIDU_TTS_API_KEY=
BAIDU_TTS_SECRET_KEY=
DEEPSEEK_API_KEY=
DEVICE_AUTH_MODE=single
DEVICE_TOKEN=
```

### 17.3 .gitignore

```gitignore
.env
venv/
__pycache__/
*.pyc
config.yaml
data/
logs/
```

注意：如果需要保留空目录，可添加：

```text
data/.gitkeep
logs/.gitkeep
```

---

## 18. 部署方式

### 18.1 开发期：Mac Mini M4

```bash
git clone https://github.com/MadBull8994/xiaozhi-esp32-server.git
cd xiaozhi-esp32-server
git checkout fb7e2e17a8340232155d35bb2f988522d3b3232b
git checkout -b xiaoclawbrain-v0.1

cp .env.example .env
vim .env

docker compose build
docker compose up
```

### 18.2 生产期：云服务器

```bash
git clone https://github.com/MadBull8994/xiaozhi-esp32-server.git
cd xiaozhi-esp32-server
git checkout xiaoclawbrain-v0.1

cp .env.example .env
vim .env

docker compose build
docker compose up -d
```

ESP32 的 `websocket.url` 指向云服务器 IP 或域名。

---

## 19. 和 ESP32 端的关系

| ESP32 保留 | ESP32 删除或弱化 | 迁移到后端 |
|---|---|---|
| audio/ 编解码 | providers/baidu_asr_* | ASR |
| display/ 显示 | providers/baidu_tts_* | TTS |
| protocols/ WS 连接 | mimi/ mimiclaw | Agent |
| boards/ 硬件抽象 | bridge/ | Skill loader |
| 按键 / 音量 / WiFi | 本地复杂 AI 逻辑 | Memory |
| fallback/ 本地 Opus 音频 | 本地 ASR/TTS provider | Logs |
| ESP32 本地 timer | | 后续 Cron |

### 19.1 ESP32 核心改动

```text
- 保留 Opus 编码上传链路
- 保留 Opus 解码播放链路
- 保留 WebSocket 连接
- 保留 TFT 状态显示
- 增加 binary Opus 小包接收逻辑
- 增加 tts_start / tts_end 控制帧处理
- 增加 error JSON 处理
- 增加 ASR_EMPTY 显示或播放提示
- 增加本地 Opus 降级音频
```

### 19.2 ESP32 不做

```text
- 不直接调用百度 ASR
- 不直接调用百度 TTS
- 不直接调用 LLM
- 不执行复杂 Agent
- 不执行复杂 Skill
```

---

## 20. 实施里程碑

2026-05-20 起，具体执行顺序以 `项目开发路线图_XiaoClawBrain.md` 为准。本节只保留架构级里程碑，避免和进度文档重复。

### P0：资料、仓库与现状对齐

```text
- 更新项目速览、路线图、进度文档
- 明确当前后端与 ESP32 两条主线
- 确认下一步从仓库基线开始
```

### P1：仓库基线与开发环境固定

```text
- 后端恢复为正式 Git 仓库
- checkout fb7e2e17a8340232155d35bb2f988522d3b3232b
- 创建或切换 xiaoclawbrain-v0.1 分支
- 锁定依赖和 Docker 开发配置
- ESP32 梳理当前分支与未提交改动
```

### P2：后端协议骨架与鉴权硬化

```text
- 单设备 Token 鉴权
- device-id / client-id header 兼容
- JSON <= 4KB
- binary frame <= 2048 bytes
- JSON 控制消息与 binary 音频帧明确分流
```

### P3：后端稳定听、稳定想、稳定说

```text
- Opus 接收与 ASR 调用
- ASR_EMPTY 第一次即提示“我没听清，你再说一遍”
- LLM timeout 20 秒，失败重试 1 次
- TTS timeout 8 秒
- 单轮总 timeout 35 秒
- TTS 只通过 binary Opus 小包下发
```

### P4：后端持久化、日志与运维

```text
- SQLite session 级记忆
- 日志脱敏、级别、轮转
- Docker healthcheck
- 不记录完整 Token、Authorization header、API Key、请求体或响应体
```

### P5：ESP32 瘦客户端协议适配

```text
- WebSocket 鉴权连接
- hello / listen / stt / sentence_* / tts_* / error JSON
- Opus binary 上传
- Opus binary 接收与播放
- TFT 状态同步
```

### P6：ESP32 旧架构清理与本地降级

```text
- 移除旧 base64 TTS 播放路径
- 移除或弱化本地 ASR/TTS provider
- 移除 bridge / MimicLaw Agent / context_builder 主链路依赖
- 网络断开或后端不可用时播放本地 Opus 降级提示音
```

### P7：端到端联调与异常恢复

```text
- ESP32 BOOT → 录音 → WS → ASR → LLM → TTS → WS → 播放 → idle
- 验证断线重连、ASR 空、LLM 超时、TTS 失败、声音太短、总超时
- 连续多轮对话稳定性验证
```

### P8：自定义 Skill 与 Agent 循环

```text
- OpenClaw / agent 项目风格 SKILL.md 兼容
- 本地自定义 Skill
- GitHub Skill 导入到 pending 目录
- SKILL_SECURITY_LEVEL=high|low|off
- Agent 循环调度
- 未命中 Skill 时正常走 LLM
```

### P9：ESP32 本地语音唤醒

```text
- 评估 ESP-SR / WakeNet / 自定义轻量唤醒词
- ESP32 本地唤醒词检测，不放到后端持续 ASR
- idle → wakeup_detected → listening → uploading_audio
- 预留或实现 0.5-1 秒环形音频缓冲
- 未完成 P9 前，不能标记为产品级闭环完成
```

---

## 21. 最终第一版范围确认

### 第一版要做

```text
- 使用 MadBull8994/xiaozhi-esp32-server fork
- 锁定 commit fb7e2e17a8340232155d35bb2f988522d3b3232b
- 建立 xiaoclawbrain-v0.1 分支
- ESP32 瘦客户端
- 后端 ASR / LLM / TTS
- WebSocket Token 鉴权
- 控制 JSON + binary Opus 小包协议
- 禁止 base64 大音频 JSON
- 基础状态机
- 用户停顿 5 秒后进入下一步
- ASR 空文本第一次即提示“没听清”
- API timeout
- 最小错误协议
- 本地 Opus 降级提示音
- SQLite 记忆
- 日志系统
- Docker healthcheck
- Skill 可配置安全等级与子进程隔离
```

### 第一版不做

```text
- 用户打断 AI
- TTS 播放中拾音
- 后端 Cron
- OTA
- 多设备后台
- Skill 市场，P8 只做本地自定义 Skill 和用户确认后的 GitHub Skill 导入
- 复杂长期记忆
- 复杂主动任务
```

---

## 22. 结论

本方案不再把 ESP32 当成“全能小脑袋”，而是让它成为稳定、低负担、可恢复的语音终端。后端负责重计算和复杂逻辑，ESP32 负责音频 I/O 和状态展示。

第一版的工程核心不是“功能最多”，而是：

```text
能稳定连接
能稳定上传
能稳定识别
能稳定回复
能稳定播放
出错能回 idle
断网有本地提示
日志能定位问题
```

先把这条语音链路打成稳定主干，再往上跑 Agent、Skill、记忆和 Cron。这样系统不会一开始就被复杂能力拖散，后续每个增量也能被单独验证。

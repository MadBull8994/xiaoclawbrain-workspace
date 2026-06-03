# XiaoClawBrain 项目开发进度

创建时间：2026-05-17
最近更新：2026-06-03（GitHub 三仓远程结构已建立）

更新规则：每次完成任务后立即更新，最新条目在上方。每次更新需包含：标记完成项、更新当前状态、记录当前进度、明确下一步要做的任务。

路线图：[项目开发路线图_XiaoClawBrain.md](./项目开发路线图_XiaoClawBrain.md)

---

## GitHub 三仓远程结构已建立（2026-06-03）：根目录已推送，后端与固件远程边界已对齐

目标：为 XiaoClawBrain 当前三仓结构建立清晰、可持续管理的 GitHub 远程布局，并尽量不打断现有后端/固件代码主线。

### 完成项

- [x] **创建根目录工作区文档仓库**
  - 新建 GitHub 仓库：
    - `MadBull8994/xiaoclawbrain-workspace`
  - URL：
    - `https://github.com/MadBull8994/xiaoclawbrain-workspace`

- [x] **创建固件 GitHub 仓库**
  - 新建 GitHub 仓库：
    - `MadBull8994/xiaoclaw-esp32-firmware`
  - URL：
    - `https://github.com/MadBull8994/xiaoclaw-esp32-firmware`

- [x] **确认后端仓库直接复用现有 GitHub 仓库**
  - 继续使用：
    - `MadBull8994/xiaozhi-esp32-server`
  - URL：
    - `https://github.com/MadBull8994/xiaozhi-esp32-server`

- [x] **完成根目录仓库首次远程接入与推送**
  - 根目录 `origin` 已设置为：
    - `git@github.com:MadBull8994/xiaoclawbrain-workspace.git`
  - 已完成首次推送：
    - `main -> origin/main`

- [x] **对齐后端与固件远程策略**
  - 后端仓库 `origin` 已切换为 SSH：
    - `git@github.com:MadBull8994/xiaozhi-esp32-server.git`
  - 固件仓库保留现有上游 `origin`：
    - `https://gh-proxy.com/https://github.com/beancookie/xiaoclaw.git`
  - 同时新增用户 GitHub 远程 `madbull`：
    - `git@github.com:MadBull8994/xiaoclaw-esp32-firmware.git`

### 当前状态

- 根目录文档仓库已上线 GitHub，可直接继续提交与推送
- 后端仓库已对齐到你自己的 GitHub SSH 远程
- 固件仓库当前采用“双远程”模式：
  - `origin` 保留上游代理源
  - `madbull` 指向你的 GitHub 固件仓库

### 下一步

- 需要时可把固件当前分支首推到 `madbull`
- 如后续确认不再依赖原上游代理源，再评估是否把固件仓库的 `madbull` 升级为 `origin`
- 后续 GitHub 仓库管理可按三仓分别推进：文档、后端、固件

---

## 根目录元仓库首次提交完成（2026-06-03）：工作区文档版控可直接使用

目标：在已完成根目录元仓库初始化的基础上，补齐首次 Git 提交，使工作区级文档和规划材料可以立即进入正常版本管理流程。

### 完成项

- [x] **确认首次提交范围**
  - 根目录元仓库只纳入工作区级文档、路线图、进度记录、规划材料和说明文件
  - 两个产品子仓库仍由根目录 `.gitignore` 排除：
    - `xiaozhi-esp32-server/`
    - `xiaoclaw-ghproxycom/`

- [x] **完成根目录元仓库首次提交**
  - 为根目录仓库设置本地 Git 身份（仅作用于该仓库）
  - 完成首次 `git add` 与 `git commit`
  - 根目录文档仓库后续已可直接继续增量提交

- [x] **确认三仓工作流不变**
  - 根目录仓库：只管项目文档与规划材料
  - 后端仓库：继续独立管理 `xiaozhi-esp32-server`
  - 固件仓库：继续独立管理 `xiaoclaw-ghproxycom`
  - GitHub 推送默认仍按三个仓库分别进行

### 当前状态

- 根目录元仓库已完成初始化和首次提交
- 工作区级文档后续可以独立查看历史、提交变更、配置远程
- 三仓结构已经固定清晰，暂不需要把两个产品仓库并入根目录

### 下一步

- 如需上传 GitHub，为根目录元仓库单独创建一个文档/协调仓库并添加 `origin`
- 后端与固件继续各自连接自己的 GitHub 远程仓库
- 后续若要统一展示三仓关系，再单独评估 submodule 或 monorepo 迁移

---

## 根目录元仓库初始化完成（2026-06-03）：工作区文档已可独立 Git 管理

目标：把根目录 `/Volumes/软件/opencode/ESP32S3语音陪伴设备开发` 初始化为一个轻量文档元仓库，使根目录项目文档后续可以单独 `git commit`，同时不吸收后端仓库和 ESP32 固件仓库的历史。

### 完成项

- [x] **初始化根目录 Git 仓库**
  - 执行 `git init`
  - 根目录现在已存在独立 `.git/`
  - 当前根目录 `git rev-parse --show-toplevel` 返回：
    - `/Volumes/软件/opencode/ESP32S3语音陪伴设备开发`

- [x] **建立根目录边界忽略规则**
  - 新增根目录 `.gitignore`
  - 明确排除：
    - `xiaozhi-esp32-server/`
    - `xiaoclaw-ghproxycom/`
    - `backup_snapshot_*`
    - `backup_sensitive_*`
    - `stable_version_assets/`
    - `data/`
    - `tmp/`
    - `hardware_bringup/`
    - 任意 `build/`、`managed_components/`、`releases/`
  - 目的：根目录仓库只跟踪工作区级文档和规划材料，不把两个子仓库或本地大体积资产收进去

- [x] **验证子仓库仍保持独立**
  - `xiaozhi-esp32-server` 仍返回：
    - `/Volumes/软件/opencode/ESP32S3语音陪伴设备开发/xiaozhi-esp32-server`
  - `xiaoclaw-ghproxycom` 仍返回：
    - `/Volumes/软件/opencode/ESP32S3语音陪伴设备开发/xiaoclaw-ghproxycom`
  - 结论：当前工作区已形成 3 个相互独立的 Git 仓库

- [x] **同步文档口径**
  - 更新 `START_HERE_项目速览.md`
  - 更新 `CLAUDE.md`
  - 在本进度文档记录根目录元仓库初始化结果

### 当前状态

- 根目录文档与规划文件后续可以独立进行 `git status` / `git add` / `git commit`
- 后端与固件仍各自保留独立提交历史和远程仓库边界
- 本轮只完成仓库初始化与忽略规则配置，**未自动创建初始 commit**

### 下一步

- 需要提交根目录文档时，在根目录单独执行 Git 提交
- 后端代码改动继续在 `xiaozhi-esp32-server/` 仓库内提交
- 固件代码改动继续在 `xiaoclaw-ghproxycom/` 仓库内提交

---

## 运行版本同步完成（2026-06-03）：设备固件已是最新，后端已更新到当前工作区版本

目标：确认当前连接设备与本机运行中的后端服务是否都处于当前主线最新可用版本；若不是，则完成更新并验证。

### 完成项

- [x] **核对设备当前固件**
  - 通过串口监视读取实机启动日志，确认设备当前运行：
    - `App version: 2.2.4`
    - `Compile time: Jun  1 2026 12:46:54`
  - 读取设备 `ota_0` 应用分区并计算 SHA256：
    - `/tmp/device_xiaozhi.bin` → `b2b9bec694962bac5603ffed1cfc519e16177ac4d1865cb0fc0d34724864fb2c`
  - 与当前稳定固件产物及当前 `build/xiaozhi.bin` 对比一致：
    - `stable_version_assets/XCB-STABLE-P9-complete+P6clean+20260602.123952/firmware/xiaozhi.bin`
    - `xiaoclaw-ghproxycom/build/xiaozhi.bin`
  - 结论：**设备已刷写最新稳定固件，无需重新刷机**

- [x] **核对后端运行版本**
  - 检查运行容器 `xiaozhi-esp32-server`，发现容器内 `core/handle/fastPath.py` 与当前工作区文件 SHA256 不一致
  - 其余校验文件 `config/logger.py`、`core/health.py`、`core/protocol.py` 已一致
  - 结论：**后端容器不是当前工作区最新版本，需要重新部署**

- [x] **更新后端到当前工作区版本**
  - 执行：
    - `docker compose -f main/xiaozhi-server/docker-compose_all.yml up -d --build xiaozhi-esp32-server`
  - 新容器镜像：
    - `xiaoclawbrain-server:local`
  - 更新后再次校验容器内 `config/logger.py`、`core/health.py`、`core/protocol.py`、`core/handle/fastPath.py`，已全部与当前工作区一致

- [x] **完成运行态验证**
  - `docker ps` 显示：
    - `xiaozhi-esp32-server    xiaoclawbrain-server:local    Up ... (healthy)`
  - 容器内健康检查通过：
    - `{"status": "ok", "service": "xiaoclawbrain", "time": ...}`

### 当前状态

- 设备当前运行固件已与最新稳定固件产物一致
- 后端当前运行容器已切换到当前工作区代码，并通过健康检查
- 当前可直接进入下一步联调、验证或新任务

### 下一步

- 按新的任务目标继续推进；本轮无需重复刷机或重复重建后端，除非后续又产生新的代码变更

---

## 稳定版本备份完成（2026-06-02）：P6 清理后稳定基底 XCB-STABLE-P9-complete+P6clean+20260602.123952

目标：将“P9 稳定验收通过 + P6 主链路清理已收口 + 新固件已刷机并完成最小实机验证”的当前版本按既定规则落成新的稳定基底，并按版本号归档固件、日志和文档。

### 完成项

- [x] **确定新稳定版本号**
  - `XCB-STABLE-P9-complete+P6clean+20260602.123952`

- [x] **创建非敏感全资产稳定备份**
  - `backup_snapshot_20260602_123952_full`
  - 包含根目录关键文档、P9 验收材料、P6 实机验证说明、后端主线非敏感工作树、固件主线当前工作树与可烧录产物
  - 新增 `BACKUP_README.md` 记录版本口径和固件 SHA256

- [x] **创建敏感补充备份**
  - `backup_sensitive_20260602_123952`
  - 包含 `.config.yaml`、数据库、日志、管理端 `.env`、`sdkconfig*` 等敏感本机资产
  - 新增 `SENSITIVE_BACKUP_README.md`

- [x] **创建按稳定版本号命名的版本资产目录**
  - `stable_version_assets/XCB-STABLE-P9-complete+P6clean+20260602.123952`
  - `firmware/`：`xiaozhi.bin`、`bootloader.bin`、`partition-table.bin`、`ota_data_initial.bin`、`srmodels.bin`、`flasher_args.json`、`flash_args`
  - `logs/`：`P9_实机复测日志_20260602.txt`、`P9_验收补充说明_20260602.md`、`P6_实机验证补充说明_20260602.md`
  - `docs/`：当前稳定版关键文档快照

- [x] **完成稳定备份轮转**
  - 删除最旧稳定全资产备份：`backup_snapshot_20260527_193019_full`
  - 删除最旧稳定敏感备份：`backup_sensitive_20260527_194242`
  - 保留最新 3 个稳定版本，符合项目规则

- [x] **同步文档口径**
  - 更新 `START_HERE_项目速览.md`
  - 更新 `CLAUDE.md`
  - 在本进度文档记录本次稳定备份

### 当前状态

- 当前稳定基底已切换为：`XCB-STABLE-P9-complete+P6clean+20260602.123952`
- P9 稳定验收仍保持通过
- P6 主链路清理已收口，并已完成最小实机验证

### 下一步

- 后续开发默认从 `XCB-STABLE-P9-complete+P6clean+20260602.123952` 继续
- 若未来再确认新的稳定版本，再按同样规则创建第 4 个稳定备份并轮转最旧稳定版本

---

## P6 主链路清理收口完成（2026-06-02）：禁用旧 base64 TTS 入口

目标：按 P6 边界继续精简 ESP32 固件主链路，移除仍可能误触发旧方案的 `tts_response.audio` base64 播放路径，并明确当前稳定版是否仍依赖本地 provider、bridge 或 MimicLaw 相关代码。

### 完成项

- [x] **禁用旧 `tts_response.audio` base64 播放路径**
  - `xiaoclaw-ghproxycom/main/application.cc`
  - 删除 `mbedtls/base64` 依赖和旧 `tts_response.audio` 解码播放逻辑
  - 若仍收到废弃 `tts_response`，仅打印告警，不再尝试播放 base64 音频

- [x] **静态确认本地 provider / bridge / MimicLaw 已不在当前编译主链路**
  - `xiaoclaw-ghproxycom/main/CMakeLists.txt` 顶层 `SOURCES` 未包含：
    - `main/providers/baidu_asr_provider.cc`
    - `main/providers/baidu_tts_provider.cc`
    - `main/bridge/*`
    - `main/mimi/*`
  - 当前稳定版主链路仍以 `xiaoclaw_ws_client + binary Opus TTS` 为准

- [x] **同步文档口径**
  - 更新 `START_HERE_项目速览.md`
  - 在本进度文档记录 P6 收口结论

### 当前状态

- P6 主链路清理已收口
- 当前稳定版不再保留可被主链路触发的旧 base64 TTS 播放入口
- `main/providers/baidu_*`、`main/bridge/`、`main/mimi/` 历史目录仍在工作树中，但不是当前稳定版编译/运行依赖

### 下一步

- 继续开发时默认从 `XCB-STABLE-P9-complete+20260602.115528` 往前推进
- 如需进一步删除历史目录本体，建议单独开一个“归档/删目录”任务，避免和当前稳定主线混改

---

## 稳定版本备份完成（2026-06-02）：P9 稳定版基底 XCB-STABLE-P9-complete+20260602.115528

目标：将当前“P9 稳定验收通过 + 短回复 TTS 首字问题已修补并复测通过”的版本按既定规则落成新的稳定基底，并把固件、日志和文档按稳定版本号归档。

### 完成项

- [x] **确定新稳定版本号**
  - `XCB-STABLE-P9-complete+20260602.115528`

- [x] **创建非敏感全资产稳定备份**
  - `backup_snapshot_20260602_115528_full`
  - 包含根目录关键文档、P9 验收材料、后端主线非敏感工作树、固件主线当前工作树与可烧录产物
  - 新增 `BACKUP_README.md` 记录版本口径和固件 SHA256

- [x] **创建敏感补充备份**
  - `backup_sensitive_20260602_115528`
  - 包含 `.config.yaml`、数据库、日志、管理端 `.env`、`sdkconfig*` 等敏感本机资产
  - 新增 `SENSITIVE_BACKUP_README.md`

- [x] **创建按稳定版本号命名的版本资产目录**
  - `stable_version_assets/XCB-STABLE-P9-complete+20260602.115528`
  - `firmware/`：`xiaozhi.bin`、`bootloader.bin`、`partition-table.bin`、`ota_data_initial.bin`、`srmodels.bin`、`flasher_args.json`、`flash_args`
  - `logs/`：`P9_实机复测日志_20260602.txt`、`P9_验收补充说明_20260602.md`
  - `docs/`：当前稳定版关键文档快照

- [x] **同步文档口径**
  - 更新 `START_HERE_项目速览.md`
  - 更新 `CLAUDE.md`
  - 在本进度文档记录本次稳定备份

### 备份轮转状态

- 当前稳定基底数量：3
- 当前保留：
  - `XCB-STABLE-0.9.3+20260527.193019`
  - `XCB-STABLE-P7-complete+20260528.142020`
  - `XCB-STABLE-P9-complete+20260602.115528`
- 未超过“最多保留最新 3 个稳定版本”的上限，因此本次不删除旧稳定备份

### 当前状态

- 当前稳定基底已切换为：`XCB-STABLE-P9-complete+20260602.115528`
- P9 稳定验收通过
- 短回复 TTS 首字偶发丢失已修补并通过针对性复测

### 下一步

- 后续开发默认从 `XCB-STABLE-P9-complete+20260602.115528` 继续
- 若未来再确认新的稳定版本，再按同样规则创建第 4 个稳定备份并轮转最旧稳定版本

---

## P9 修补完成（2026-06-02）：短回复 TTS 首字偶发丢失

目标：修复本地唤醒闭环中“短回复语音播放时首字偶发被吞、长回复不明显”的体验问题；仅处理 TTS 起播时序，不改协议、不改连续对话、不引入无关重构。

### 完成项

- [x] **定位根因**
  - 通过最小日志点确认：问题不在后端文本，也不是主因来自功放冷启动
  - 根因是 ESP32 客户端在第一帧 TTS binary 到达后切换到 `speaking`，而 `kDeviceStateSpeaking` 入口又调用 `ResetDecoder()`
  - 结果是首帧或首段已入队的 Opus / PCM 被清掉，短回复更容易表现成首字缺失

- [x] **完成最小修补**
  - `xiaoclaw-ghproxycom/main/application.cc`
    - 将 TTS 解码器/播放队列清理前移到 `OnTtsStart()`
    - 删除 `kDeviceStateSpeaking` 入口里对新一轮 TTS 的二次 `ResetDecoder()`
  - 保留原有状态机与 binary TTS 协议，不改 wake word、ASR、后端 TTS 发送方式

- [x] **清理临时调试日志**
  - 删除本轮为定位问题临时加入的 `TTSDBG` 诊断字段、接口与日志
  - 保留真正修复逻辑，不把调试噪音留在稳定版中

- [x] **验证结果**
  - 本地编译通过：`source /Users/mashiyue/esp/esp-idf-v5.5.2/export.sh && idf.py build`
  - 实机刷写通过：`idf.py -p /dev/cu.usbmodem5B5E1028821 flash`
  - 针对性复测 2 轮：
    - `现在几点`
    - `今天星期几`
  - 用户反馈：**两轮都正常了**

### 当前状态

- P9 主链路稳定验收仍保持通过
- 短回复 TTS 首字偶发丢失已完成最小修补并通过针对性复测
- 当前未启动 P10，也未引入播放中打断的新行为

### 下一步

- 如需更稳妥，可后续再做 5-10 轮短回复专项回归，确认该体验问题不再偶发
- 当前优先保留这一修补作为 P9 稳定版的一部分

---

## P9 调试定位（2026-06-02）：补充 TTS 起播首字偶发丢失的最小日志点

目标：不修改功能逻辑，只在 ESP32 端补充 TTS 起播链路的关键时序日志，用于定位“短回复首字偶发被吞、长回复不明显”的问题究竟发生在首帧被清、解码/队列阶段，还是输出链路冷启动阶段。

### 完成项

- [x] **在 TTS 起播关键路径补充最小定位日志**
  - `xiaoclaw-ghproxycom/main/application.cc`
    - `tts_start` 记录 `round id / ts / 初始 state`
    - 第一帧 TTS binary 到达时记录 `delta_from_start_ms / len / state`
    - `synthesizing -> speaking` 触发前后记录时序
    - `kDeviceStateSpeaking` 状态处理里在 `ResetDecoder()` 前后记录快照
  - `xiaoclaw-ghproxycom/main/audio/audio_service.cc`
    - 第一帧 TTS enqueue
    - 第一帧 decode 成功
    - 第一次 `OutputData()` 真正写扬声器
    - `ResetDecoder()` 内部队列清空前的实际队列大小

- [x] **补充调试辅助接口**
  - `xiaoclaw-ghproxycom/main/audio/audio_service.h/cc`
    - 新增 `SetDebugTtsRoundId()`
    - 新增 `LogPlaybackDebugSnapshot()`
  - 仅服务于日志归因，不改变原有行为

- [x] **本地编译自检通过**
  - `source /Users/mashiyue/esp/esp-idf-v5.5.2/export.sh && idf.py build`
  - 构建完成，生成新的 `build/xiaozhi.bin`

### 当前判断

- 当前最可疑链路仍是：第一帧 TTS binary 到达后切换到 `speaking`，而 `speaking` 状态处理又触发 `ResetDecoder()`，存在把首帧或首段 PCM 清掉的风险
- 次级怀疑点仍包括：
  - 输出通道懒开启导致功放 / I2S 起播暖机
  - 首帧承担 24k -> 16k 解码 / 重采样初始化开销

### 下一步

- 将调试固件烧录到 OpenClaw V2.7
- 复现 1-2 轮“短回复首字偶发丢失”场景
- 从 `TTSDBG` 日志判断是：
  - 首帧被 `ResetDecoder()` 清掉
  - 还是首帧已输出、但输出链路冷启动吞头

---

## P9 多轮实机复测完成，10/10 全部通过，稳定验收通过（2026-06-02）

目标：完成 P9 本地语音唤醒的多轮实机稳定性复测，收集 10 轮覆盖不同场景的测试数据，确认 P9 能否通过稳定验收。

### 测试环境

- 设备：OpenClaw V2.7（ESP32-S3-N16R8）
- 固件分支：`xiaoclaw-ghproxycom`
- 后端容器：`xiaozhi-esp32-server`（健康运行中）
- 固件基线：P8-9 最后烧录版本（2026-06-01 烧录，含 WakeNet + 唤醒后音频边界修补 + 短句 ASR 兜底）

### 测试分组与结果

| 分组 | 轮次 | 问题 | 结果 | 关键观察 |
|------|------|------|------|---------|
| A 短问题 | A1 | 现在几点 | ✅ | 唤醒命中→提示音→正确回答时间→回 idle，状态机完整 |
| A 短问题 | A2 | 今天星期几 | ✅ | 唤醒命中→正确回答→回 idle |
| A 短问题 | A3 | 你是谁 | ✅ | 唤醒命中→125 帧自我介绍→回 idle |
| A 短问题 | A4 | 讲个笑话 | ✅ | 唤醒命中→525 帧完整笑话→回 idle，长回复未被截断 |
| B 稍长问题 | B1 | 现在几点，顺便告诉我今天星期几 | ✅ | 唤醒命中→两个信息都正确播报→回 idle |
| B 稍长问题 | B2 | 请用一句话介绍你自己 | ✅ | 唤醒命中→一句话简介→回 idle |
| B 稍长问题 | B3 | 今天天气怎么样如果不知道就说不知道 | ✅ | 唤醒命中→正常回答/诚实说不知道→回 idle |
| C 连续相邻 | C1 | (第一轮) 现在几点 | ✅ | 正常播报→回 idle |
| C 连续相邻 | C2 | (第二轮，立即唤醒) 今天星期几 | ✅ | 播报刚结束后立即唤醒，无状态残留，第二轮干净走通 |
| D BOOT 备用 | D1 | BOOT 键 + 现在几点 | ✅ | BOOT 触发→录音→识别→675 帧回复→回 idle，备用入口可用 |

### 验收统计

- **总轮数**：10
- **成功**：10（100%）
- **失败**：0
- **MQTT fallback**：0 次
- **唤醒词送 ASR**：0 次（唤醒后清理逻辑正常工作）
- **Invalid state transition**：0 次
- **首词丢失**：0 次
- **误唤醒**：0 次
- **状态残留**：0 次（包括连续相邻测试）
- **BOOT 备用入口**：可用
- **死机/卡死**：0 次

### 失败分类统计

- 唤醒未命中：0
- 唤醒命中但未进入 listening：0
- 首词丢失：0
- 识别不完整：0
- 后端回复异常：0
- 状态机异常：0
- 无法回 idle：0
- BOOT 备用入口异常：0
- 其他：0

### 日志留存与证据边界

- 串口原始日志已保存：`P9_实机复测日志_20260602.txt`
- 后端抓取文件：`/tmp/backend_log_p9_test.log`
- 本轮验收以串口原始日志和实机观察为主证据；`/tmp/backend_log_p9_test.log` 可作为辅助留存，但其尾部包含容器历史日志与旧时间戳，不作为本轮通过/不通过的唯一判据
- 进一步补充说明已单独整理到：`P9_验收补充说明_20260602.md`

### 验收结论

**P9 稳定验收通过。** 10 轮全部通过，无功能性阻塞，无 MQTT fallback，无状态异常。本地 WakeNet 唤醒可稳定完成 `idle → wakeup_detected → listening → uploading_audio → recognizing → synthesizing → speaking → idle` 完整闭环。连续相邻播报结束后立即唤醒无状态残留。BOOT 备用入口正常可用。

### 当前状态

- P0-P8：已完成
- **P9：稳定验收通过** ✅
- P10：候选升级项（可选连续对话与记忆策略），不插入当前版本

### 下一步建议

1. 创建稳定版本备份：XCB-STABLE-P9-complete+20260602.xxxxxx
2. 记录稳定版本编号，继续开发前在进度文档和备份笔记中同步
3. 如需继续增强交互体验，可启动 P10 候选升级讨论（可选连续对话模式 + 记忆策略升级）
4. 评估后续优化方向：自然语气连说「你好小智现在几点」的一体化体验

---

## 记录后续升级项：可选连续对话与记忆策略，当前仍以 P8-9 稳定收尾为目标（2026-06-01）

目标：把“后台可选连续对话模式”和“短期/长期记忆策略”正式记录为后续系统升级项，避免与当前 P8-9 实机稳定性收尾混淆；当前阶段继续以本地唤醒链路的多轮实测和稳定版本验收为主。

### 完成项

- [x] **确认当前第一版目标不变**
  - 当前仍以 P8-9 的稳定版本为目标
  - 现阶段优先完成本地唤醒后的连续多轮实测、命中率观察和体验收尾
  - 不在本轮收尾中直接插入“连续对话模式”功能开发

- [x] **记录连续对话能力的定位**
  - 连续对话不属于当前 P0-P9 第一版必做项
  - 该能力记录为 **P10 后续升级候选：可选连续对话与记忆策略升级**
  - 目标形态为：
    - 默认单轮对话
    - 后台可选连续对话
    - 两种模式都可接入短期/长期记忆策略

- [x] **记录当前记忆策略现状**
  - 当前后端具备记忆 provider 框架
  - 记忆内容默认由记忆模块/LLM 做筛选与总结，不是简单把每轮全文原样硬塞
  - 连续对话若后续启用，还需要补齐“每轮保存 / 会话结束保存 / 长连接定期保存”的策略设计

### 当前状态

当前产品验收口径不变：
- P8 已完成
- P9 核心功能已跑通，但还差连续多轮实测与体验收尾
- “可选连续对话 + 记忆策略升级”已记录为 P10 候选，不插入当前稳定版收尾

### 下一步

- 继续执行 P8-9 多轮实机复测
- 记录多轮测试中的命中率、首词完整性、误唤醒与状态残留表现
- 在 P9 稳定验收完成后，再决定是否启动 P10 连续对话与记忆策略升级

---

## P8-9 本地唤醒问答链路实机跑通，进入体验收尾（2026-06-01）

目标：把 `你好小智` 唤醒后的真实实机行为收口到可用模式，避免把唤醒词本身当成提问内容，并确认“先唤醒、再提问”的非 BOOT 路径可以完成时间问答闭环。

### 完成项

- [x] **定位“聆听阶段很短”的真实原因**
  - 问题并不是固定倒计时过短
  - 实机日志显示，旧逻辑会把唤醒词自身和 pre-wake 音频一起送进下一轮 STREAM ASR
  - 因此用户在提示音后还没开始正式提问时，后端就已经把 `你好小智` 识别成 `你好小哥哥` / `你好小猪` / `你好，小智老师讲话` 等文本并提前进入应答

- [x] **调整固件唤醒后音频边界**
  - `xiaoclaw-ghproxycom/main/audio/audio_service.h/cc`
    - 新增 `DiscardPreWakeAudioOnNextFlush()`
    - 在下一轮 voice processing 启动前丢弃 pre-wake ring buffer 中的唤醒前/唤醒词残留
  - `xiaoclaw-ghproxycom/main/application.cc`
    - XiaoClaw WS 唤醒路径下不再把 wake word opus 包主动上传给后端
    - 唤醒后改为“只发 `detect` 控制消息，等待提示音后再接正式问题”

- [x] **重新编译并实机烧录**
  - `idf.py build`：通过
  - `idf.py -p /dev/cu.usbmodem5B5E1028821 flash`：通过

- [x] **完成非 BOOT 路径实机验证**
  - 测试方式：
    - 先说 `你好小智`
    - 等提示音
    - 再说 `现在几点`
  - 最新后端日志显示：
    - `listen detect: 你好小智`
    - `listen start mode=auto`
    - `识别文本: 现在几点了？`
  - 设备成功播报：
    - `现在是 17:34，今天是 2026-06-01，星期一`

### 验证状态

- [x] `idf.py build`
- [x] `idf.py -p /dev/cu.usbmodem5B5E1028821 flash`
- [x] 本地唤醒实机复测：`你好小智` → 提示音 → `现在几点`

### 当前状态

P8-9 的核心功能闭环已经跑通：
- 本地 WakeNet 唤醒工作正常
- 状态机可走通 `idle -> wakeup_detected -> listening -> uploading_audio -> recognizing -> synthesizing -> speaking -> idle`
- 唤醒词本身不再被当成问题主体送进后端
- “先唤醒、再提问”的当前交互模式已可稳定完成时间问答

下一步不再是“功能是否存在”，而是体验收尾：
- 继续观察连续多轮下的命中率与稳定性
- 评估是否还要优化“一口气连说 `你好小智，现在几点`”的自然体验

---

## P8-9 修补短句误识别兜底并完成 BOOT 实机时间问答验证（2026-06-01）

目标：解决 OpenClaw BOOT 手动录音在 ASR 只识别到半句时仍直接进入 LLM、导致回复跑偏讲故事的问题，并完成最小闭环实机验证。

### 完成项

- [x] **定位主因**
  - 实机日志确认本轮并非主链路断开，而是后端实际只收到 `现在。`
  - 旧逻辑中，Baidu STREAM ASR 仅对“空识别”返回 `ASR_EMPTY`
  - 像 `现在。` 这类非空但明显不完整的短句，仍会直接 `startToChat()`，导致模型自由发挥

- [x] **补齐后端短句兜底**
  - `xiaozhi-esp32-server/main/xiaozhi-server/core/utils/util.py`
    - 新增 `ASR_SHORT_TEXT_WHITELIST`
    - 新增 `should_accept_asr_text()`
  - `core/providers/asr/base.py`
  - `core/providers/asr/baidu.py`
    - 将“是否进入 LLM”从“非空即可”改为“满足最小长度或命中白名单”
    - 不满足时统一返回 `ASR_EMPTY: 我没听清，你再说一遍`

- [x] **补充自动化测试**
  - 新增 `tests/test_asr_short_text_guard.py`
  - 覆盖：
    - `现在。` 被拦截为 `ASR_EMPTY`
    - `继续` 等白名单短词仍可正常进入主链路

- [x] **完成容器热修与实机复测**
  - 将修补同步到运行中的 `xiaozhi-esp32-server` 容器并重启
  - 设备重新连回 WS 后，用 BOOT 手动录音复测 `现在几点`
  - 后端最终识别为：`现在几点了？`
  - 设备成功播报：`现在是 16:59，今天是 2026-06-01，星期一`

### 验证状态

- [x] `python3 -m unittest tests.test_asr_short_text_guard -v`
- [x] `python3 -m unittest tests.test_listen_message_handler -v`
- [x] BOOT 实机复测：`现在几点` 成功闭环

### 当前状态

P8-9 当前已确认：
- XiaoClaw 唤醒链路不再错误回退 MQTT
- 后端不会再把半截短识别直接送进 LLM 造成答非所问
- BOOT 手动入口的时间问答闭环已通过实机验证

下一步：继续实测本地唤醒词 `你好小智，现在几点`，重点验证 WakeNet 命中率、首词完整性，以及非 BOOT 路径下的完整 `idle -> wakeup_detected -> listening -> uploading_audio -> ...` 闭环。

---

## P8-9 修正 XiaoClaw 唤醒链路误走 MQTT（2026-06-01）

目标：修复 OpenClaw V2.7 在本地唤醒词命中后没有进入 XiaoClaw WebSocket 录音上传，而是错误回退到旧 `protocol_`/MQTT 路径的问题。

### 完成项

- [x] **定位实机根因**
  - 实机日志显示：
    - `XiaoClawWS: ws connected`
    - 唤醒后却出现 `MQTT is not connected, try to connect now`
    - 随后 `错误: 正在寻找可用服务`
  - 证明 `XiaoClawWsClient` 已连通，但 `HandleWakeWordDetectedEvent()` / `ContinueWakeWordInvoke()` / `kDeviceStateListening` 仍在走旧 `protocol_` 分支

- [x] **补齐 XiaoClaw WS 唤醒控制消息**
  - `main/xiaoclaw_ws_client.h/cc`
  - 新增 `SendWakeWordDetectedJson(wake_word)`，发送 `{"type":"listen","state":"detect","text":"..."}` 
  - `SendListenStartJson()` 支持按 `ListeningMode` 发送 `manual/auto/realtime`

- [x] **切换 OpenClaw 唤醒/监听入口到 XiaoClaw WS**
  - `main/application.h/cc`
  - 新增 `IsXiaoClawWsReady()` / `XiaoClawSendWakeWordDetected()` / `XiaoClawBeginListening()`
  - `HandleWakeWordDetectedEvent()` 和 `ContinueWakeWordInvoke()` 在 XiaoClaw WS 已连接时：
    - 先发 wake word `detect`
    - 再进入 `listening`
    - 再通过 `XiaoClawSendListenStart()` + `XiaoClawStartAudioUpload()` 开始上传
  - `HandleToggleChatEvent()` / `HandleStartListeningEvent()` / `HandleStopListeningEvent()` / `ContinueOpenAudioChannel()` / `WakeWordInvoke()` 同步避免回退到旧 MQTT 路径

### 验证状态

- [x] `idf.py build`
- [x] `idf.py -p /dev/cu.usbmodem5B5E1028821 flash`
- [ ] 实机复测唤醒闭环：待用户复测最新固件

### 当前状态

最新固件已重新烧录到设备，唤醒词触发入口已改为优先走 XiaoClaw WebSocket。下一步：复测“你好小智，现在几点”，确认日志不再出现 MQTT fallback，而是出现 `listen detect/start`、`uploading_audio` 与后续服务器状态推进。

---

## P8-9 已完成实机烧录与启动验证（2026-06-01）

目标：基于修正后的 WakeNet 分区重新完成板级构建、烧录设备，并确认模型分区、AFE 唤醒链路与设备启动日志都正常。

### 完成项

- [x] **确认板级构建产物有效**
  - `build/srmodels/srmodels.bin` 实际大小为 **291042 bytes**
  - 模型包头部已包含 `wn9_nihaoxiaozhi_tts`
  - `build/flash_args` 已确认将模型刷写到 `0x820000`

- [x] **完成实机烧录**
  - 执行 `idf.py -p /dev/cu.usbmodem5B5E1028821 flash`
  - 日志确认：
    - `Wrote 291044 bytes ... at 0x00820000`
    - `Hash of data verified`
    - `Hard resetting via RTS pin`

- [x] **完成启动日志核对**
  - Boot 分区表显示：
    - `model  00820000 00050000`
    - `fatfs  00870000 00560000`
  - 启动后设备正常连网、启动本地配置页并进入 `idle`
  - XiaoClaw WS 正常连上后，AFE/WakeNet 成功初始化：
    - `MODEL_LOADER: Successfully load srmodels`
    - `AfeWakeWord: Model 0: wn9_nihaoxiaozhi_tts`
    - `AFE Pipeline: [input] -> ... -> |WakeNet(wn9_nihaoxiaozhi_tts,)| -> [output]`

### 验证状态

- [x] `tests/test_openclaw_wake_word_policy.sh`
- [x] `idf.py -B build-openclaw-verify build`
- [x] `python3 scripts/release.py openclaw-v2.7 --name openclaw-v2.7`
- [x] `idf.py -p /dev/cu.usbmodem5B5E1028821 flash`
- [x] `idf.py -p /dev/cu.usbmodem5B5E1028821 monitor`

### 当前状态

P8-9 已完成实机烧录与启动验证，WakeNet 模型已成功写入 `model` 分区并被设备启动时正确加载。下一步：按实机测试清单验证“你好小智”唤醒闭环、首词完整性、2 秒冷却和 BOOT 备用入口。

---

## P8-9 实机烧录前修正 WakeNet 模型分区（2026-06-01）

目标：完成实机烧录前的最终构建验收，确认 WakeNet 模型已真实打包进 `srmodels.bin`，并修正 `model` 分区不足导致的烧录风险。

### 完成项

- [x] **定位空模型包根因**
  - 直接运行 `idf.py build` 时使用的是仓库根目录 `sdkconfig`，其中 `CONFIG_SR_WN_WN9_NIHAOXIAOZHI_TTS` 未启用
  - 因此 `build-openclaw-verify/srmodels/srmodels.bin` 只生成了 4 字节空包，不能用于实机唤醒验证

- [x] **切换到板级构建链验证**
  - 使用 `python3 scripts/release.py openclaw-v2.7 --name openclaw-v2.7`，确保 `main/boards/openclaw-v2.7/config.json` 的 `sdkconfig_append` 真正生效
  - 构建后确认：
    - `CONFIG_USE_AFE_WAKE_WORD=y`
    - `CONFIG_SEND_WAKE_WORD_DATA=y`
    - `CONFIG_SR_WN_WN9_NIHAOXIAOZHI_TTS=y`

- [x] **修正模型分区不足**
  - 板级构建日志给出 `Recommended model partition size: 285K`
  - 实际生成的 `build/srmodels/srmodels.bin` 为 **291042 bytes**
  - 原 `model` 分区仅 `0x40000`（256KB），会在烧录时覆盖后续 `fatfs`
  - 已将 `partitions/openclaw_v2_7_16m.csv` 调整为：
    - `model`: `0x820000, 0x50000`（320KB）
    - `fatfs`: 起始偏移 `0x870000`

### 验证状态

- [x] `tests/test_openclaw_wake_word_policy.sh`
- [x] `idf.py -B build-openclaw-verify build`
- [x] 已完成后续板级构建、实机烧录与启动验证

### 当前状态

WakeNet 模型分区不足问题已修正，并已被后续实机烧录验证覆盖。下一步聚焦纯功能验证：本地唤醒闭环、首词完整性、冷却行为和 BOOT 备用入口。

---

## P8-9 ESP32 本地语音唤醒接入完成（2026-06-01）

目标：在 ESP32-S3 上实现 WakeNet9 本地语音唤醒（你好小智），完成 `idle → wakeup_detected → listening → uploading_audio` 产品闭环，替代 P1-P8 的 BOOT 按键触发方案。

### 完成项

- [x] **唤醒方案选型**
  - 选用 **USE_AFE_WAKE_WORD**（WakeNet9 + AFE），理由：上游默认路径，ESP32-S3+8MB PSRAM 满足 `depends on`，状态机/事件处理已完备
  - 唤醒词：`CONFIG_SR_WN_WN9_NIHAOXIAOZHI_TTS`（你好小智），ESP-SR 组件内已含，无需额外下载
  - 明确排除 `CustomWakeWord`（无 AFE 处理，需自行实现噪声/回声消除）

- [x] **分区表调整**：`partitions/openclaw_v2_7_16m.csv`
  - 移除 `assets` 分区（2.5MB，`CONFIG_FLASH_NONE_ASSETS` 已启用，完全浪费）
  - 新增 `model` 分区（`data, undefined, 0x820000, 0x40000` = 256KB），存 WakeNet9 模型
  - `fatfs` 起始偏移从 `0xAA0000` 调整为 `0x860000`

- [x] **config.json 唤醒配置**：`main/boards/openclaw-v2.7/config.json`
  - `CONFIG_WAKE_WORD_DISABLED=y` → `# CONFIG_WAKE_WORD_DISABLED is not set`
  - 新增 `CONFIG_USE_AFE_WAKE_WORD=y`
  - 新增 `CONFIG_SR_WN_WN9_NIHAOXIAOZHI_TTS=y`
  - 新增 `CONFIG_SEND_WAKE_WORD_DATA=y`

- [x] **1 秒 PCM 环形缓冲区**（gap capture）
  - `main/audio/audio_service.h`：新增 `pre_wake_ring_buffer_`（16000 samples = 1s @ 16kHz 16bit）
  - `main/audio/audio_service.cc`：`WritePreWakeRingBuffer()` 在 `AudioInputTask()` 中捕获唤醒检测停止到音频处理器启动间的音频
  - `PopPreWakeRingBuffer()` 和 `FlushPreWakeRingBuffer()` 在音频处理器启动时自动刷入编码队列

- [x] **2 秒检测冷却**：`main/application.cc`
  - `HandleWakeWordDetectedEvent()` 检查 `last_wake_word_time_ms_`，2 秒内再次触发则忽略并记录日志
  - 防止 AFE 残留缓冲区导致的二次误唤醒

- [x] **kDeviceStateWakeupDetected TFT 显示**：`HandleStateChangedEvent()` 新增 `wakeup` 状态显示

- [x] **测试脚本更新**：`tests/test_openclaw_wake_word_policy.sh` 断言切换为新配置

### 验证状态

- [ ] **实机验证**：尚未进行

### 当前状态

P8-9 唤醒词工程配置、ring buffer、冷却、TFT 显示代码改动完成。下一步：实机编译烧录 → 验证唤醒链路闭环。

---

## P8-8 pending 启用流程验收通过（2026-06-01）

目标：实现 `pending/<name>` → `enabled/<name>` 的审核启用流程，让 pending skill 经过校验后启用到 enabled，能被现有 discovery / registry / matcher / runner 正常识别和执行。

### 完成项

- [x] **新增 `core/skills/skill_enabler.py`**
  - 类 `SkillEnabler`，遵循 `SkillImporter` 的返回字典模式（`ok` / `error_code` / `message`）
  - 核心方法 `enable_pending(skill_name, *, confirmed=False) → dict`
  - 从 `xiaoclaw.import.json` 读取 source 元信息（持久化到磁盘，非参数传递），仅允许 `local` 来源
  - 启用前重新校验 `SKILL.md` 和 `xiaoclaw.skill.json`，沿用 `parse_skill_md()` / `parse_xiaoclaw_skill_json()` / `validate_name_consistency()`
  - 安全等级限制：只允许 `high`，`low` / `off` 返回 `UNSUPPORTED_SECURITY_LEVEL`
  - 确认语义：`confirmed=False` 返回 `REVIEW_CONFIRMATION_REQUIRED`，`confirmed=True` 才执行
  - 移动（非复制）pending → enabled，避免同时存在两份造成歧义
  - 启用后 manifest `enabled=true` 写回 `xiaoclaw.skill.json`
  - 写入 sidecar 元信息 `xiaoclaw.enable.json`，含 `name` / `source` / `security_level` / `enabled_at`（UTC ISO8601）
  - 启用失败时三阶段完整回滚（带 manifest 内容恢复和 sidecar 清理）
  - 错误码：`PENDING_SKILL_NOT_FOUND` / `REVIEW_CONFIRMATION_REQUIRED` / `SKILL_ALREADY_ENABLED` / `INVALID_MANIFEST` / `MISSING_MANIFEST` / `MISSING_SKILL_MD` / `UNSUPPORTED_SECURITY_LEVEL` / `ENABLE_FAILED` / `MISSING_PENDING_METADATA` / `UNSUPPORTED_SOURCE`

- [x] **更新 `skill_importer.py`**
  - 新增 `_write_import_metadata()`，导入后写入 `xiaoclaw.import.json`（含 `source`、`imported_at`）
  - 本地和远程导入路径均写入

- [x] **更新 `__init__.py`**
  - 导出 `SkillEnabler`

- [x] **新增 `tests/test_skill_enabler.py`（42 项）**
  - 覆盖：成功启用（移动、manifest、sidecar）、未确认拒绝、pending 不存在、缺 SKILL.md、缺 manifest、manifest 非法、名称不一致、enabled 冲突、security_level=low/off 拒绝、source=unknown/remote/github 拒绝、启用后 discovery/registry/matcher/runner 可识别、三阶段回滚（manifest 写入失败、sidecar 写入失败→内容恢复+sidecar清理）

- [x] **新增 `tests/test_skill_importer.py` 导入元信息测试（3 项）**
  - 验证本地/远程导入后写入 `xiaoclaw.import.json` 且字段正确

- [x] **修复测试 `finally` 块侧车文件 chmod 问题（4 项）**
  - 4 个侧车写入失败测试的 `finally` 块原先直接 `sidecar_path.chmod(0o644)`，但回滚已删除侧车文件，导致 `FileNotFoundError`
  - 修复：`finally` 块加 `if sidecar_path.exists():` 保护

### 验证结果

```
python3 -m pytest tests/test_skill_enabler -v
42/42 通过

python3 -m pytest tests/test_skill_importer -v
42/42 通过

python3 -m pytest tests/test_skill_discovery -v
19/19 通过

python3 -m pytest tests/test_skill_runner -v
21/21 通过

python3 -m pytest tests/test_skill_matcher -v
37/37 通过

python3 -m pytest tests/ -v
324/324 通过
```

### 当前状态

P8-8 审核启用闭环已验收通过。当前不默认进入 P8-9，下一步待讨论后续阶段安排。

---

## P8-7 环境噪声清理完成（2026-06-01）

目标：清理 SkillRunner 受限子进程环境在 macOS 上触发的 `python3: warning: confstr() failed with code 5: couldn't get path of DARWIN_USER_TEMP_DIR; using /tmp instead`，确保后续 P8-8 及其后的开发/回归输出保持干净。

### 完成项

- [x] **SkillRunner limited env 补齐基础临时目录变量**
  - `core/skills/skill_runner.py` 的 `_build_limited_env()` 在保留最小权限面的前提下，新增：
    - `TMPDIR`
    - `TMP`
    - `TEMP`
  - 取值优先使用宿主环境 `TMPDIR`，否则回退 `tempfile.gettempdir()`
  - 未新增任何敏感环境透传，仅修复 Python 子进程在 Darwin 上查询用户临时目录失败的 warning

- [x] **新增回归测试**
  - `tests/test_skill_runner.py` 新增 `test_limited_env_includes_temp_vars`
  - `test_skill_runner.py` 用例总数更新为 **21/21**

### 验证结果

```
python3 -m unittest tests.test_skill_runner -v
21/21 通过

python3 -W error::ResourceWarning -m unittest tests.test_skill_global -v
17/17 通过

python3 -W error::ResourceWarning -m unittest discover -s tests -v
279/279 通过
```

### 下一步

进入 **P8-8 pending 启用流程**。不做 P9。

---

## P8-7 验收收尾完成（2026-05-29）

目标：收口 P8-7 review/验收尾巴，补齐本地导入 symlink 边角、清理相关测试中的未关闭 event loop 警告，并把最终验收口径写实后准备进入 P8-8。

### 完成项

- [x] **SkillImporter 本地导入 symlink 规则收紧**
  - `core/skills/skill_importer.py` 的 `_check_unsafe_symlinks()` 改为发现任意 symlink 就拒绝导入，不再只拦“指向目录外”的链接
  - 解决本地 skill 包内 `selflink -> .` / 指向包内目录的 symlink 被 `shutil.copytree(..., symlinks=False)` 递归跟随，最终触发 `Too many levels of symbolic links` 的问题
  - 当前导入口径统一为：
    - 本地目录导入：任意 symlink → `UNSAFE_SYMLINK`
    - tar 远程归档导入：任意 symlink/hardlink → `EXTRACT_FAILED`

- [x] **P8-7 测试补齐到最终态**
  - `tests/test_skill_importer.py` 新增 `test_local_import_rejects_internal_symlink_loop`
  - importer 用例总数更新为 **39/39**

- [x] **P8 相关测试尾项清理**
  - `tests/test_skill_matcher.py`
  - `tests/test_skill_global.py`
  - 为 `setUpClass()` 创建的 import loop 增加 `tearDownClass()` 关闭
  - 为 `_make_conn()` 创建的 `conn.loop` 增加 `addCleanup(loop.close)`
  - 清除全量回归末尾的 `ResourceWarning: unclosed event loop` / `unclosed socket`

### 验证结果

```
python3 -m unittest tests.test_skill_importer -v
39/39 通过

python3 -W error::ResourceWarning -m unittest tests.test_skill_matcher -v
37/37 通过

python3 -W error::ResourceWarning -m unittest tests.test_skill_global -v
17/17 通过

python3 -W error::ResourceWarning -m unittest discover -s tests -v
278/278 通过
```

### 下一步

进入 **P8-8 pending 启用流程**。不做 P9。

---

## P8-7 远程归档导入完成（2026-05-29）

目标：实现已有 skill 包导入到 `data/skills/pending` 的能力，支持本地目录导入和远程归档导入，兼容 GitHub / 镜像 / 直接 archive URL，导入后默认不启用、不可执行。

### 完成项

- [x] **SkillImporter 完整实现**（`core/skills/skill_importer.py` — 从骨架补成最小可用版本）
  - `import_from_local(source_path)` — 本地目录导入：校验目录存在 + `xiaoclaw.skill.json` 有效性，复制到 pending，不自动启用；任意 symlink 拒绝导入
  - `import_from_archive_url(url)` — 远程归档导入：下载 → 解包 → 找 skill 根目录 → 校验 → 复制到 pending
  - `import_from_github(repo_url, ref)` — 薄封装：repo URL → archive URL 转换后委托 `import_from_archive_url`
  - URL 规范化：`.zip` / `.tar.gz` 直用，repo URL 自动补 `/archive/HEAD.tar.gz`，`.git` 后缀自动去除
  - `_normalize_archive_url()` — 不写死 `github.com`，兼容任何镜像 URL（`gitmirror.example.com/user/repo` 也能转换）
  - `_download_file()` — urllib 下载，Content-Type 自动判断 zip/tar.gz
  - `_extract_archive()` — 同时支持 zip 和 tar.gz；tar 内任意 symlink/hardlink 直接拒绝解包
  - `_find_skill_root()` — 处理单层外层 wrapper 目录（如 GitHub 自动生成的 repo 包裹层）
  - 路径逃逸保护：`_extract_archive()` 逐文件检查 `Path.resolve()`，防止 `../` 逃逸
  - 冲突检测：pending 已存在同名 skill 时返回 `SKILL_ALREADY_EXISTS`，不静默覆盖
  - 结构化返回：`{"ok": True, "source": "local"|"remote", "name": "...", "target_path": "...", "status": "pending"}`

- [x] **pending 隔离验证**
  - `SkillDiscovery.scan_all()` 正确将 pending 中的 skill 放到 `result["pending"]`，不进入 `registered`
  - `SkillRegistry.load_from_discovery()` 将 pending skill 存入 `_pending` dict，`get()` 不返回
  - `SkillRunner._validate_skill()` 对 `is_pending=True` 返回 `SKILL_NOT_EXECUTABLE`
  - `SkillMatcher._get_pkg()` 通过 `registry.get()` 不会命中 pending
  - 以上隔离在 P8-7 实机集成测试中逐项验证通过（见 `TestSkillImporterPendingIsolation`）

- [x] **无 manifest 约束**
  - 本地目录缺少 `xiaoclaw.skill.json` → `MISSING_MANIFEST` 错误
  - `xiaoclaw.skill.json` 格式无效 → `INVALID_MANIFEST` 错误
  - 远程归档解包后找不到合法 skill 包 → `SKILL_NOT_FOUND` 错误

### 新增测试

- **`tests/test_skill_importer.py`**（39 项测试，100% 通过）
  - `TestSkillImporterLocalImport`（11 项）：成功、源不存在、缺 manifest、同名冲突、无效 JSON、无 scripts 目录、不自动启用、目标路径在 pending 下、状态返回、外部 symlink 拒绝、内部 symlink 循环拒绝
  - `TestSkillImporterArchiveUrlNormalization`（6 项）：zip/tar.gz 保持、GitHub repo 转换、`.git` 后缀处理、镜像 URL 转换、尾部斜杠处理
  - `TestSkillImporterRemoteArchiveZip`（12 项）：zip 导入成功、tar.gz 导入成功、下载失败、解包失败、不合法 skill 包、外层 wrapper 包裹目录、GitHub wrapper 导入、zip 根目录 skill 包、tar 根目录 skill 包、tar symlink 拒绝、不自动启用、tar symlink+普通文件链式逃逸阻止
  - `TestSkillImporterPathTraversal`（2 项）：zip 路径逃逸阻止、tar 路径逃逸阻止
  - `TestSkillImporterPendingIsolation`（4 项）：pending 不在 registered、不在 registry._skills、runner 不可执行、matcher 不匹配
  - `TestSkillImporterGithubWrapper`（4 项）：委托到 archive、`.git` 后缀净化、自定义 ref、镜像 URL 兼容

- **已有测试无回归验证**
  - `test_skill_discovery.py` 19/19 通过（含 pending 隔离与 registry 加载）
  - `test_skill_runner.py` 21/21 通过（含 `test_pending_skill` 返回 `SKILL_NOT_EXECUTABLE` 与 limited env temp vars）
  - `test_skill_matcher.py` 37/37 通过（含 matcher 不命中 pending）
  - `test_skill_tool_adapter.py` 51/51 通过（含 `test_pending_skill_not_registered`）
  - `test_sample_skills_p8_3.py` 12/12 通过
  - `test_skill_global.py` 17/17 通过

### 验收结果

```
test_skill_importer: 39/39 通过（新增，P8-7）
test_skill_discovery: 19/19 通过（无回归）
test_skill_runner: 21/21 通过（无回归）
test_skill_matcher: 37/37 通过（无回归）
test_skill_tool_adapter: 51/51 通过（无回归）
test_skill_global: 17/17 通过（无回归）
test_skill_manifest: 24/24 通过（无回归）
test_sample_skills_p8_3: 12/12 通过（无回归）
test_fast_path: 34/34 通过（无回归）
tests/ 全量合计: 279/279 通过
```

### 不改

- 不改 ESP32 侧
- 不做 pending → enabled 启用流程（那是 P8-8）
- 不做复杂 Web API / UI / 命令行入口
- 不做 low/off 安全等级扩展（那是 P8-9）
- 不做无关重构
- 远程下载测试全部 mock，不依赖真实联网
- 下载逻辑不绑定 `github.com`

### 改动文件

| 文件 | 改动 |
|------|------|
| `core/skills/skill_importer.py` | **完整重写**：从骨架补成最小可用版本，新增 local/archive/github 导入、URL 规范化、zip/tar.gz 提取、路径逃逸保护 |
| `tests/test_skill_importer.py` | **新增** P8-7 测试（39 项：本地导入 11 项 + URL 规范化 6 项 + 远程归档 12 项 + 路径逃逸 2 项 + pending 隔离 4 项 + GitHub 封装 4 项） |
| `项目开发进度_XiaoClawBrain.md` | 本条记录 |

### 下一步

进入 **P8-8 pending 启用流程**。不做 P9。

---

## P8-6 验收修补完成（2026-05-29）

目标：修补 P8-6 验收缺口。修复 `_execute_skill_tool()` JSON 类型边界（`[]` / `null` / `"abc"` 非 dict JSON 的降级），将自写 helper 测试替换为真实 `ConnectionHandler._execute_skill_tool()` 测试，新增 `ConnectionHandler.chat()` 真实注册测试，清理源码字符串检查类低价值测试。

### 完成项

- [x] 修复 `core/connection.py` 的 `_execute_skill_tool()` JSON 类型边界
  - `json.loads()` 成功后增加 `isinstance(args, dict)` 校验
  - 非 dict 时返回 `Action.REQLLM` + `SKILL_INVALID_ARGS`，不打断整轮
  - 覆盖 `arguments=[]` / `null` / `"abc"` / 非法 JSON / `text` 非字符串
- [x] 替换为真实接入点测试（`tests/test_skill_tool_adapter.py`）
  - `TestConnectionHandlerExecuteSkillTool`：7 项真实 `_execute_skill_tool()` 测试
    - 非法 JSON → `Action.REQLLM` + `SKILL_INVALID_ARGS`
    - `[]` → `Action.REQLLM` + `SKILL_INVALID_ARGS`
    - `null` → `Action.REQLLM` + `SKILL_INVALID_ARGS`
    - `"abc"` → `Action.REQLLM` + `SKILL_INVALID_ARGS`
    - `{"text":123}` → `Action.REQLLM` + `SKILL_INVALID_ARGS`
    - `_skill_runner=None` → `Action.REQLLM` + `SKILL_NOT_FOUND`
    - `{}` valid → `Action.RESPONSE`
  - `TestConnectionHandlerChatRegistration`：1 项真实 `chat()` 注册测试
    - 开启 `llm_tool_call=true`，断言 `skill__device-status-tool-mock` 注册到 LLM functions
  - 补齐最小依赖桩（`plugins_func.loadplugins` / `UnifiedToolHandler` / `opuslib_next` / `openai`），让 `ConnectionHandler` 在当前测试环境可真实导入，避免关键测试被整组 skip
- [x] 清理低价值测试
  - 删除 3 项源码字符串检查测试
  - 删除自写 `_execute_skill_tool()` helper（替换为真实方法测试）
  - 删除自写 `_execute_skill_tool_runner_none()` helper（替换为真实方法测试）
- [x] 回归验证
  - `python3 -m unittest tests.test_skill_tool_adapter -v`：51/51 通过（8 项 ConnectionHandler 真实测试全部执行）
  - P8-5 skill_matcher: 37/37 通过（无回归）
  - skill_runner: 20/20 通过
  - skill_global: 17/17 通过
  - skill_manifest: 24/24 通过
  - skill_discovery: 19/19 通过
  - fast_path: 34/34 通过
  - `python3 -m unittest discover -s tests -v`：239/239 通过
  - **合计 239/239 通过**

### 不改

- 不改 ESP32 侧
- 不改协议
- 不做 P8-7
- 不做无关重构

### 改动文件

| 文件 | 改动 |
|------|------|
| `core/connection.py` | `_execute_skill_tool()` 增加 `isinstance(args, dict)` 校验 |
| `tests/test_skill_tool_adapter.py` | 替换源码检查/自写 helper 为真实方法测试；新增 8 项 ConnectionHandler 测试 |
| `项目开发进度_XiaoClawBrain.md` | 本条记录 |

### 下一步

进入 **P8-7 本地/GitHub 导入到 pending**。

---

## P8-6 Agent Loop 完成（2026-05-29）

目标：实现 `llm_tool_call` 型 Skill 接入 LLM tool/function 调用路径，复用现有 `chat()` 工具链，不改 P8-5 的显式/关键词 matcher，不做多步 agent 编排。

### 完成项

- [x] 新增 `core/skills/skill_tool_adapter.py`（薄注册层）
  - `get_llm_skill_tools(config, registry)` → 只筛选 `trigger_mode=llm_tool_call` 的 skill，映射为 OpenAI function schema
  - tool 名统一使用 `skill__<skill-name>` 前缀，便于分发时区分
  - 统一单参数 `text` schema，不扩展复杂 JSON schema 生成器
  - 过滤 metadata-only / pending / disabled / security-blocked 的 skill
  - `is_skill_tool_call()` / `extract_skill_name()` 工具函数
  - `format_skill_tool_result()` / `format_skill_tool_error()` 结果格式化
  - `is_llm_tool_call_enabled()` 配置开关查询
- [x] `explicit_only` / `explicit_or_keywords` 的 skill 不注册到 LLM tools
- [x] 修改 `core/connection.py` 的 `chat()` 方法：
  - 在现有 `functions` 列表（`func_handler.get_functions()` + `DIRECT_ANSWER_TOOL`）之后追加 skill tools
  - 工具调用分发处检测 `is_skill_tool_call()`，走 `SkillRunner.run(..., phase="llm_tool_call")`
  - 新增 `_execute_skill_tool()` 异步方法处理 skill tool 执行与结果转换
- [x] Skill 结果回灌策略：
  - 有 `speak` → `Action.RESPONSE`，直接播报并写入对话历史
  - 无 `speak` 但有 `data`/`context` → `Action.REQLLM`，回灌给 LLM 继续生成
  - 失败/timeout/invalid → `Action.REQLLM`，给 LLM 简短 tool error，不打断整轮
- [x] 新增 `skills/device-status-tool-mock/` 样例 skill：
  - `trigger_mode=llm_tool_call`，无 `explicit_names` 和 `keywords`
  - 返回 `speak=""` + 结构化 `data`（电量、WiFi、运行时间等），覆盖 tool 结果回灌 LLM 路径
- [x] `config.yaml` 新增：
  - `skills.llm_tool_call.enabled: false`（默认关闭）
  - `on_demand.selected` 新增 `device-status-tool-mock` 条目
- [x] 新增 `tests/test_skill_tool_adapter.py`（40 项测试）
  - 配置开关：默认关闭、打开、关闭时不注册工具
  - 过滤：`llm_tool_call` 注册 / `explicit_only` 不注册 / `explicit_or_keywords` 不注册
  - 安全：metadata-only / disabled / pending / security-off 不注册
  - 工具 schema 格式正确
  - `is_skill_tool_call` / `extract_skill_name` / 格式化函数
  - SkillRunner 调用时 `phase="llm_tool_call"` 通过 stdin 传入
  - Skill 成功（有 speak / 无 speak）均可处理
  - Skill 失败：timeout / exec-error / not-found 均可恢复，不打断
  - P8-5 回归：`llm_tool_call` skill 不出现在 explicit/keyword 匹配路径
  - device-status-tool-mock 可被发现、可执行、返回 data 无 speak

### 不改

- `match_explicit()` / `match_keywords()` 未改动
- `llm_tool_call` 不回灌到 P8-5 matcher
- 不做多步 agent 编排
- 不做 GitHub/pending 导入
- 不做复杂 planner/reasoner

### 验收结果

- `python3 -m unittest tests.test_skill_matcher -v`：37/37 通过（P8-5 无回归）
- `python3 -m unittest tests.test_skill_tool_adapter -v`：40/40 通过（P8-6）
- `python3 -m unittest tests.test_skill_runner -v`：20/20 通过
- `python3 -m unittest tests.test_skill_global -v`：17/17 通过
- `python3 -m unittest discover -s tests -v`：228/228 通过

### 改动文件

| 文件 | 改动 |
|------|------|
| `core/skills/skill_tool_adapter.py` | **新增** P8-6 薄注册层：筛选 `llm_tool_call` skill → OpenAI tool schema |
| `core/skills/__init__.py` | 导出 P8-6 新模块 |
| `core/connection.py` | `chat()` 追加 skill tools + 分发；新增 `_execute_skill_tool()` 方法 |
| `config.yaml` | 新增 `skills.llm_tool_call.enabled` 开关 + `device-status-tool-mock` 条目 |
| `skills/device-status-tool-mock/` | **新增** `llm_tool_call` 样例 skill（返回 data 无 speak） |
| `tests/test_skill_tool_adapter.py` | **新增** P8-6 测试（40 项） |
| `项目开发进度_XiaoClawBrain.md` | 本条记录 |
| `START_HERE_项目速览.md` | 同步当前进度 |
| `P8完整方案_自定义Skill与Agent循环_XiaoClawBrain.md` | P8-6 节更新 |

### 下一步

进入 **P8-7 本地/GitHub 导入到 pending**，或继续 P8 后续阶段。不提前启动 P9。

---

## P8-5 收尾完成（2026-05-29）

目标：修补 P8-5 最后一个验收缺口，保证显式前缀命中后传给 skill 的 `args_text` 保留用户原始大小写与内容，不被匹配阶段的归一化逻辑污染；同步收口文档与问题记录。

### 完成项

- [x] 修复 `core/skills/skill_matcher.py` 的显式前缀参数保真问题
  - `_try_prefix_parse()` 改为分离“匹配用归一化字符串”和“透传用原始字符串”
  - skill 名仍使用不区分大小写匹配
  - `args_text` 改为从原始 `utterance.strip()` 中切分，保留原始大小写
- [x] 补充 P8-5 回归测试
  - 新增 `test_prefix_args_preserve_original_case`
  - 新增 `test_run_echo_skill_preserves_original_case_args`
  - 覆盖 `运行技能 echo Hello JSON` 这类英文/大小写敏感参数场景
- [x] 收口文档
  - `开发问题解决记录.md` 新增问题 Q 记录
  - `P8完整方案_自定义Skill与Agent循环_XiaoClawBrain.md` 补充“显式触发 args 原样透传”的约束
  - `START_HERE_项目速览.md` 同步当前 P8-5 已完成收尾、下一步保持 P8-6

### 验收结果

- `python3 -m unittest tests.test_skill_matcher.TestSkillMatcherExplicitPrefix.test_prefix_args_preserve_original_case -v`：1/1 通过（修复前先红灯）
- `python3 -m unittest tests.test_skill_matcher -v`：37/37 通过
- `python3 -m unittest discover -s tests -v`：188/188 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `xiaozhi-esp32-server/main/xiaozhi-server/core/skills/skill_matcher.py` | 修复显式前缀命中后 `args_text` 被错误 lower-case 的问题 |
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_skill_matcher.py` | 新增 2 条大小写保真回归测试，总数 35 → 37 |
| `开发问题解决记录.md` | 新增问题 Q |
| `项目开发进度_XiaoClawBrain.md` | 新增本条 P8-5 收尾记录 |
| `START_HERE_项目速览.md` | 同步当前阶段收尾状态 |
| `P8完整方案_自定义Skill与Agent循环_XiaoClawBrain.md` | 补充显式触发参数原样透传说明 |

### 下一步

进入 **P8-6 Agent Loop**：
- 在 `chat()` 中将 `llm_tool_call` Skill 以 tool/function 形式注册给 LLM
- LLM 主动选择后由 Agent 循环调度 `SkillRunner` 执行
- 保持 `llm_tool_call` 只在 P8-6 路径生效，不回灌到 P8-5 的显式/关键词匹配

---

## P8-5 SkillMatcher 完成（2026-05-29）

目标：实现 `SkillMatcher` 的 `match_explicit()`（显式触发语法 + 裸别名）和 `match_keywords()`（保守短语关键词 + 去泛词），将 on_demand skill 接入 startToChat() 主链路，实现 `allow_all_enabled`，关闭 `llm_tool_call` 提前匹配，结构化匹配结果传参。

### 完成项

- [x] 实现 `core/skills/skill_matcher.py`
  - **显式触发语法**：`运行技能 <name> [args]` / `使用技能 <name> [args]` / `调用技能 <name> [args]`；args 传给 runner 作为 utterance
  - **裸别名保留**：用户说 `echo` / `回声` 等直接命中 explicit_names，args_text 为空
  - `match_explicit()` 先尝试前缀语法，再尝试裸别名
  - **保守关键词**：`match_keywords()` 使用完整短语匹配（如 `查询设备状态` / `电量还有多少`），拒绝泛词
  - `llm_tool_call` **完全排除**：`match_explicit()` 不匹配 `llm_tool_call`，`match_keywords()` 不匹配 `llm_tool_call`；该模式等 P8-6 再接入
  - **`allow_all_enabled` 完整实现**：`true` 时所有 enabled executable skill 都可匹配；`false` 时只匹配 `selected` 列表
  - **结构化匹配结果**：返回 `{"name", "args_text", "mode", "matched_by"}` 字典，runner 只接收 args_text
  - 遵守 `on_demand.enabled`、`selected` 列表、`trigger_mode`、`allow_all_enabled`
  - 跳过 registry 中不存在的 skill 和 `enabled=false` 的 skill
  - 纯匹配操作是同步的，不触发 I/O
- [x] 更新 `device-status-mock/xiaoclaw.skill.json`
  - 删除泛词 `状态`，替换为保守短语列表：`设备状态` / `检查设备状态` / `查询设备状态` / `查询电量` / `电量还有多少` / `运行状况`
- [x] 接入主链路 `receiveAudioHandle.py`
  - 在 fast path 之后、intent 之前，按 `match_explicit → match_keywords` 顺序匹配
  - 匹配成功后，将结构化结果（含 args_text）传给 `run_matched()`
  - `result.ok && result.speak` 时通过 `_speak_fast_path_reply` 发送语音
  - skill 无 speak 或失败时降级到普通意图+LLM 对话
  - 不破坏 fast path、intent、GlobalSkillManager、ASR_EMPTY、binary TTS 行为
- [x] 新增 `tests/test_skill_matcher.py`（35 项测试）
  - 显式触发前缀：3 种 prefix、中英文别名、带 args、无 skill 名时跳过、不存在的 skill
  - 裸别名：英文 `echo`、中文 `回声`、`设备状态`、无匹配
  - 保守关键词：`查询设备状态`、`检查设备状态`、`电量还有多少`、`运行状况`
  - 假阳性过滤：`这个状态不错`、`我现在状态很好` 不命中
  - `llm_tool_call` 排除：explicit 和 keyword 路径都不命中
  - `allow_all_enabled`：`true` 时未 selected 的 skill 也可匹配；`false` 时严格 selected-only
  - disabled 控制：`enabled=false`、空 selected 均返回空
  - runner：echo 传 args 正确、device-status-mock 返回 data
  - startToChat 集成：explicit/prefix/keyword 触发不走 chat、无匹配走 chat、假阳性句子走 chat、fast path 优先

### 验收结果

- `python3 -m unittest tests.test_skill_matcher -v`：35/35 通过
- `python3 -m unittest discover -s tests -v`：186/186 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `xiaozhi-esp32-server/main/xiaozhi-server/core/skills/skill_matcher.py` | 骨架（9 行）→ 完整实现（含 prefix/bare alias/keyword/allow_all/llm_tool_call 排除/结构化结果） |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/device-status-mock/xiaoclaw.skill.json` | 删除泛词 `状态`，替换为保守短语 |
| `xiaozhi-esp32-server/main/xiaozhi-server/core/handle/receiveAudioHandle.py` | add import SkillMatcher；fast path 后、intent 前插入匹配+执行，传结构化 match dict |
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_skill_matcher.py` | 新增，35 项测试 |
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_skill_global.py` | `_make_conn()` 补 `conn._skill_matcher = None` |
| `项目开发进度_XiaoClawBrain.md` | 更新本条 P8-5 完成记录 |
| `START_HERE_项目速览.md` | 同步当前 P8 进度 |

### 下一步

进入 **P8-6 Agent Loop**：
- 在 `chat()` 中将 `llm_tool_call` Skill 以 tool/function 形式注册给 LLM
- LLM 主动选择后由 Agent 循环调度 `SkillRunner` 执行
- 不提前做 P8-7 复杂 Agent 编排

---

## P8-4 全局默认 Skill 完成（2026-05-29）

目标：实现 `GlobalSkillManager`，让 `config.yaml` 中 `skills.global_defaults.selected` 的 `persona-context-mock` 在普通 LLM 前执行，返回的 context 注入主链路；失败时默认 `continue`，不影响正常对话；fast path 默认不执行 global skill。

### 完成项

- [x] 实现 `core/skills/skill_global.py`
  - 读取 `config.yaml` 的 `skills.global_defaults`
  - 只处理 `enabled=true` 且 `selected` 中声明的 skill
  - 支持 `phase=before_llm`
  - 只执行 `auto_run.enabled=true` 的 skill
  - 按 `order` 排序，遵守 `max_skills_per_turn` / `total_timeout` / `context_char_limit`
  - 遵守 `include_fast_path=false`（fast path 不执行）
  - 复用现有 `SkillDiscovery` / `SkillRegistry` / `SkillRunner`
  - 聚合多个 skill 返回的 context，失败时 `failure_policy=continue` 不打断对话
- [x] 接入主链路 `receiveAudioHandle.py`
  - 在普通 LLM 路径（fast path 和 intent 之后）调用 `GlobalSkillManager.run_before_llm()`
  - 返回的 context 注入到 `conn.chat()` 输入
  - 不破坏 `ASR_EMPTY`、`binary TTS`、fast path、timeout 行为
- [x] 新增 `tests/test_skill_global.py`（11 项测试）
  - `persona-context-mock` 在普通 LLM 前执行并返回 context
  - context 注入 LLM
  - `include_fast_path=false` 时 fast path 不执行 global skill
  - `selected` 为空 / `enabled=false` 时返回空
  - unknown skill 不会打断对话（`failure_policy=continue`）
  - `max_skills_per_turn=0` / `total_timeout=0` 返回空
  - `context_char_limit` 生效

### 验收结果

- `python3 -m unittest tests.test_sample_skills_p8_3 -v`：11/11 通过（0 回归）
- `python3 -m unittest tests.test_skill_global -v`：11/11 通过
- `python3 -m unittest discover -s tests -v`：145/145 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `xiaozhi-esp32-server/main/xiaozhi-server/core/skills/skill_global.py` | 完整实现 `GlobalSkillManager` |
| `xiaozhi-esp32-server/main/xiaozhi-server/core/handle/receiveAudioHandle.py` | 普通 LLM 路径接入 `GlobalSkillManager` |
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_skill_global.py` | 新增 11 项集成测试 |
| `项目开发进度_XiaoClawBrain.md` | 新增本条 P8-4 完成记录 |
| `START_HERE_项目速览.md` | 同步 P8 进度为 P8-5 |

### 下一步

进入 **P8-5 SkillMatcher**：
- 实现 `core/skills/skill_matcher.py` 的 `match_explicit()` 和 `match_keywords()`
- 接入显式触发和关键词触发的 on_demand skill 路由
- 不提前做 P8-6 Agent Loop

---

## P8-3 测试清理收口完成（2026-05-29）

目标：删除已被真实文件测试覆盖的临时样例测试，统一 P8-3 验收口径到“真实 `skills/` 目录 + 真实 manifest + 真实 runner 链路”，并明确下一阶段入口。

### 完成项

- [x] 精简 `tests/test_sample_skills_p8_3.py`
  - 删除 tempfile 造样例、手工 `_register_skill()`、测试内嵌 manifest/script 的冗余测试与辅助代码
  - 保留真实仓库样例的 discovery / registry / runner / `auto_run` / `config.yaml` 一致性验收
  - 测试文件从 25 项收敛为 11 项高信号真实文件测试
- [x] 统一 P8-3 当前验收口径
  - 样例 Skill 验收只以仓库真实 `skills/echo`、`skills/device-status-mock`、`skills/persona-context-mock` 为准
  - 不再混用“临时造样例通过”和“真实目录通过”两套重复结论
- [x] 明确下一阶段
  - P8-3 样例与验收已收口完成
  - 下一步固定进入 **P8-4 全局默认 Skill**

### 验收结果

- `python3 -m unittest tests.test_sample_skills_p8_3 -v`：11/11 通过
- `python3 -m unittest discover -s tests`：134/134 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_sample_skills_p8_3.py` | 删除冗余临时样例测试，收敛为 11 项真实文件验收 |
| `项目开发进度_XiaoClawBrain.md` | 新增本条 P8-3 收口记录 |
| `START_HERE_项目速览.md` | 同步当前 P8 进度与下一步入口 |

### 下一步

进入 **P8-4 全局默认 Skill**：

- 实现 `core/skills/skill_global.py`
- 读取 `global_defaults.selected`
- 在普通 LLM 前运行 `persona-context-mock`
- 注入 context，默认不影响 fast path，不因失败打断对话

---

## P8-3 验收补丁完成（2026-05-29）

目标：修正 config.yaml 中 selected skill 名称与真实 skill 名称不一致，补强测试覆盖真实文件。

### 完成项

- [x] 修正 `config.yaml`
  - `global_defaults.selected`：`persona-context` → `persona-context-mock`
  - `on_demand.selected`：`device-status` → `device-status-mock`
- [x] 补强 `tests/test_sample_skills_p8_3.py` 新增 12 项测试
  - `TestRealSampleSkillsDiscovery`：从仓库真实 `skills/` 目录发现 3 个样例 skill
  - `TestRealSampleSkillsRunner`：真实 discovery → registry → runner 执行验证 echo / device-status-mock / persona-context-mock
  - `TestPersonaContextMockRealAutoRun`：从真实 `skills/persona-context-mock/xiaoclaw.skill.json` 读取并断言 auto_run 字段
  - `TestConfigSelectedSkillNames`：`config.yaml` 中 `global_defaults.selected` 和 `on_demand.selected` 的 name 必须在 discovery 中存在

### 验收结果

- `python3 -m unittest tests.test_sample_skills_p8_3 -v`：25/25 通过
- `python3 -m unittest discover -s tests`：148/148 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `xiaozhi-esp32-server/main/xiaozhi-server/config.yaml` | `persona-context` → `persona-context-mock`，`device-status` → `device-status-mock` |
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_sample_skills_p8_3.py` | 新增 12 项真实文件测试（discovery + runner + auto_run 断言 + config 一致性） |
| `项目开发进度_XiaoClawBrain.md` | 新增本条验收补丁记录 |

### 下一步

按计划进入 **P8-4 全局默认 Skill** 或 **P8-3 主链路接入**。

---

## P8-3 内置样例 Skill 完成（2026-05-29）

目标：实现 3 个内置样例 Skill（echo、device-status-mock、persona-context-mock），基于 P8-1/P8-2 能力完成"可发现 + 可执行"的最小闭环。

### 完成项

- [x] 创建 echo skill
  - `skills/echo/SKILL.md`：合法 YAML frontmatter
  - `skills/echo/xiaoclaw.skill.json`：显式触发型，auto_run.enabled=false
  - `skills/echo/scripts/run.py`：输入文本原样返回为 speak
- [x] 创建 device-status-mock skill
  - `skills/device-status-mock/SKILL.md`：合法 YAML frontmatter
  - `skills/device-status-mock/xiaoclaw.skill.json`：显式触发型，返回固定 mock 设备状态
  - `skills/device-status-mock/scripts/run.py`：返回 speak + 结构化 data，不依赖真实硬件或网络
- [x] 创建 persona-context-mock skill
  - `skills/persona-context-mock/SKILL.md`：合法 YAML frontmatter
  - `skills/persona-context-mock/xiaoclaw.skill.json`：auto_run.enabled=true, scope=global, phase=before_llm, failure_policy=continue, include_fast_path=false, max_runs_per_turn=1
  - `skills/persona-context-mock/scripts/run.py`：返回 context 和 data，speak 为空
- [x] 新增 13 项测试 `tests/test_sample_skills_p8_3.py`：
  - 3 个样例 skill 能被 discovery 发现
  - 目录名 / SKILL.md / xiaoclaw.skill.json name 一致
  - echo 可 runner 执行并返回正确 speak
  - device-status-mock 可执行并返回固定 speak/data
  - device-status-mock 不依赖网络和硬件
  - persona-context-mock 可执行并返回 context/data
  - persona-context-mock auto_run 声明字段正确（scope=global, phase=before_llm 等）
  - echo / device-status-mock auto_run 声明为 disabled
  - 所有样例 skill name 符合 `^[a-z0-9]+(-[a-z0-9]+)*$`
- [x] 同步文档命名口径
  - P8 方案文档中 `device_status_mock` / `persona_context_mock` → `device-status-mock` / `persona-context-mock`

### 验收结果

- `python3 -m unittest tests.test_sample_skills_p8_3 -v`：13/13 通过
- `python3 -m unittest discover -s tests`：136/136 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/echo/SKILL.md` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/echo/xiaoclaw.skill.json` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/echo/scripts/run.py` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/device-status-mock/SKILL.md` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/device-status-mock/xiaoclaw.skill.json` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/device-status-mock/scripts/run.py` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/persona-context-mock/SKILL.md` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/persona-context-mock/xiaoclaw.skill.json` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/skills/persona-context-mock/scripts/run.py` | 新增 |
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_sample_skills_p8_3.py` | 新增（13 项测试） |
| `P8完整方案_自定义Skill与Agent循环_XiaoClawBrain.md` | 修正样例 skill 命名口径 |
| `项目开发进度_XiaoClawBrain.md` | 新增本条 P8-3 记录 |

### 下一步

进入 **P8-3 主链路接入**（Agent Cycle 整合）或按计划进入 **P8-4 全局默认 Skill**：

- 将样例 skill 接入 startToChat() 主链路调度
- 实现 SkillMatcher 的 on_demand 匹配
- 实现 GlobalSkillManager 的全局默认 Skill 循环
- 不提前展开 importer / GitHub 导入 / Skill 市场

---

## P8-2 验收补丁收口完成（2026-05-29）

目标：修复 `SkillRunner` 在 skill 大量输出 `stderr` 时被误判为 `SKILL_TIMEOUT` 的验收缺口，并把 P8-2 文档口径收口到最新测试结果。

### 完成项

- [x] 修复 `SkillRunner` 的 `stderr` 假超时问题
  - 新增 `_STDERR_CAPTURE_LIMIT = 65536`
  - 新增 `_read_stdout_with_limit()`，继续负责 stdout 限流
  - 新增 `_drain_stderr()`，并发持续 drain stderr，避免 pipe 背压卡死子进程
  - `_communicate_with_limit()` 改为 `asyncio.create_task(...) + asyncio.gather(...)` 并发读取 stdout / stderr
  - `asyncio.TimeoutError` 分支增加 `try/except ProcessLookupError`，避免超时和子进程自杀/已退出时的双 kill 竞争
- [x] 新增回归测试
  - `STDERR_SPAM_SCRIPT`：模拟 skill 向 stderr 写入大量日志后仍正常返回 JSON
  - `test_large_stderr_does_not_fake_timeout`：验证大量 stderr 不会再被误判为 `SKILL_TIMEOUT`
- [x] 同步修正文档口径
  - `tests/test_skill_runner.py` 从 19 项更新为 20 项
  - 全量 discover 从 122/122 更新为 123/123
  - 当前下一步继续明确为 **P8-3 Agent 循环接入**

### 验收结果

- `python3 -m unittest tests.test_skill_runner -v`：20/20 通过
- `python3 -m unittest discover -s tests`：123/123 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `xiaozhi-esp32-server/main/xiaozhi-server/core/skills/skill_runner.py` | 并发 drain stdout/stderr，修复 stderr 背压导致的假超时 |
| `xiaozhi-esp32-server/main/xiaozhi-server/tests/test_skill_runner.py` | 新增大 stderr 回归测试 |
| `项目开发进度_XiaoClawBrain.md` | 新增本条收口记录并更新口径 |
| `开发问题解决记录.md` | 补记 stderr 假超时问题与修复 |
| `START_HERE_项目速览.md` | 同步当前 P8 进度与下一步 |

### 下一步

已由 **P8-3 内置样例 Skill** 接续完成。下一步见 P8-3 记录。

---

## P8-2 Skill Runner high 模式完成（2026-05-29）

目标：实现高安全默认的 Skill 子进程执行器 `SkillRunner`，支持子进程隔离、stdin JSON 输入、stdout JSON 解析、timeout、cwd 限制、limited_env、stdout 大小限制、stderr 日志。

### 完成项

- [x] `SkillRunner` 完整实现：
  - 子进程运行，不直接 `import` / `exec` skill 脚本
  - stdin JSON 输入（包含 `version`、`utterance`、`args`、`session_id`、`device_id`、`phase`、`metadata`）
  - stdout JSON 解析（`ok: true` → 提取 speak/context/data；`ok: false` → 提取 error_code/message）
  - 非 JSON stdout 当作纯文本 speak
  - `asyncio.wait_for` 超时控制，超时返回 `SKILL_TIMEOUT`
  - `process.kill()` + `process.wait()` 清理超时子进程
  - stdout 按块读取 + 大小限制，超限返回 `SKILL_OUTPUT_TOO_LARGE`
  - 非 0 退出码返回 `SKILL_EXEC_ERROR`
  - stderr 写入日志，不直接返回给用户
  - cwd 限制到 skill 目录
  - limited_env（仅 `PATH` + `env_allowlist` 中的变量）
  - entrypoint 路径遍历检测（`relative_to` 检查）
  - 仅支持 `python` runtime，其余返回 `SKILL_UNSUPPORTED_RUNTIME`
- [x] 安全等级处理：
  - 读取 `skills.security_level` 配置（默认 `high`）
  - `high` 模式下拒绝 `security_level=off` 的 skill，返回 `SKILL_SECURITY_BLOCKED`
  - `off` 模式拒绝执行，返回 `SKILL_SECURITY_BLOCKED`
  - `low` / `off` 识别配置字段，不展开完整能力
- [x] 前置校验：
  - skill 不存在 → `SKILL_NOT_FOUND`
  - metadata_only → `SKILL_NOT_EXECUTABLE`
  - invalid → `SKILL_NOT_FOUND`（registry 将其放入 `_invalid`，`get()` 返回 None）
  - disabled → `SKILL_DISABLED`
  - pending → `SKILL_NOT_EXECUTABLE`
  - entrypoint 为空 → `SKILL_ENTRYPOINT_NOT_FOUND`
  - entrypoint 文件不存在 → `SKILL_ENTRYPOINT_NOT_FOUND`
  - entrypoint 路径超出 skill 目录 → `SKILL_ENTRYPOINT_NOT_FOUND`
- [x] 配置读取：
  - `skills.security_level`
  - `skills.runner.high_timeout`（默认 5s）
  - `skills.runner.high_stdout_limit`（默认 65536）
  - `skills.runner.env_allowlist`（默认空）
- [x] `tests/test_skill_runner.py` — 20 项测试，覆盖全部验收口径：
  1. `test_success_json` — 正常脚本返回 `speak`/`data`
  2. `test_plain_text_stdout` — stdout 为纯文本时当 `speak`
  3. `test_timeout` — 超时返回 `SKILL_TIMEOUT`
  4. `test_nonzero_exit` — 非 0 返回 `SKILL_EXEC_ERROR`
  5. `test_stdout_overflow` — stdout 超限返回 `SKILL_OUTPUT_TOO_LARGE`
  6. `test_empty_stdout` — 空白/空输出返回 `SKILL_INVALID_OUTPUT`
  7. `test_skill_not_found` — registry 中不存在时返回 `SKILL_NOT_FOUND`
  8. `test_metadata_only` — metadata_only 返回 `SKILL_NOT_EXECUTABLE`
  9. `test_invalid_status` — invalid 返回 `SKILL_NOT_FOUND`
  10. `test_entrypoint_not_found` — entrypoint 不存在返回 `SKILL_ENTRYPOINT_NOT_FOUND`
  11. `test_env_allowlist` — allowlist 中的 env 生效
  12. `test_env_allowlist_excludes_secrets` — 空 allowlist 不泄露敏感 env
  13. `test_cwd_is_skill_directory` — cwd 是 skill 目录
  14. `test_security_off_blocked` — `security_level=off` 在 high 下被阻止
  15. `test_disabled_skill` — `enabled=false` 返回 `SKILL_DISABLED`
  16. `test_unsupported_runtime` — 非 python runtime 返回 `SKILL_UNSUPPORTED_RUNTIME`
  17. `test_pending_skill` — pending 返回 `SKILL_NOT_EXECUTABLE`
  18. `test_skill_returns_error_json` — skill 返回 `ok: false` JSON
  19. `test_empty_entrypoint_config` — entrypoint 为空字符串
  20. `test_large_stderr_does_not_fake_timeout` — 大量 stderr 不再误判超时

### 未做的事（按约束）

- 未接入 ASR / LLM / TTS 主链路
- 未做 SkillMatcher
- 未做 GlobalSkillManager
- 未做 Importer / GitHub 导入
- 未做 P8-3 样例 skill 正式目录接入
- 未改协议文档口径
- 未大规模重构 registry/discovery
- `low` 模式未展开（只识别配置字段）
- `off` 模式拒绝执行（不提前开放真执行）

### 验收结果

- `python3 -m unittest tests.test_skill_runner`：20/20 通过
- `python3 -m unittest tests.test_skill_manifest`：24/24 通过
- `python3 -m unittest tests.test_skill_discovery`：19/19 通过
- `python3 -m unittest discover -s tests`：123/123 通过（0 回归）

### 改动文件

| 文件 | 改动 |
|------|------|
| `core/skills/skill_runner.py` | 骨架（6 行）→ 完整高安全子进程执行器（~220 行） |
| `tests/test_skill_runner.py` | 新增，20 项测试 |

### 下一步

进入 **P8-3**：接入 Agent 循环和 Agent Loop，将 SkillRunner 集成到主链路。同时可开始准备样例 skill（echo、device-status）和 `skills/` 目录结构。

---

## P8-1 验收补丁完成，准备进入 P8-2（2026-05-28）

目标：收掉 P8-1 验收时暴露的配置语义缺口，确保 SkillDiscovery 真正遵守 `config.yaml` 中的 `skills.paths` 和 `allow_home_agents`，再进入 P8-2。

### 完成项

- [x] 为 `SkillDiscovery` 补两组回归测试
  - 自定义 `skills.paths.*` 时，扫描必须使用配置路径而不是硬编码默认路径
  - `allow_home_agents: false` 时，必须跳过 `~/.agents/skills`
- [x] `SkillDiscovery` 改为按配置生成扫描源
  - 普通扫描路径从 `skills.paths` 读取，缺省时才回退默认路径
  - pending 路径从 `skills.paths.pending` 读取，缺省时回退默认路径
  - `allow_home_agents` 关闭时跳过 home skills 扫描
  - `skills.enabled: false` 时直接返回空扫描结果
- [x] 保持其余 P8-1 行为不变
  - 同名冲突优先级处理不变
  - metadata_only / invalid / pending 分类不变
  - 不提前引入 runner / matcher / importer / global / agent loop

### 验收结果

- `python3 -m unittest tests.test_skill_discovery`：19/19 通过
- `python3 -m unittest tests.test_skill_manifest`：24/24 通过
- `python3 -m unittest discover -s tests`：103/103 通过

### 下一步

进入 **P8-2 Skill Runner high 模式**：实现高安全默认子进程执行，覆盖 timeout、cwd 限制、limited_env、stdout 限制、stderr 日志。

---

## P8-1 Skill 发现与扫描完成（2026-05-28）

目标：实现 Skill 包扫描与解析，扫描 `data/skills/enabled`、`skills`、`.agents/skills`、`~/.agents/skills`、`data/skills/pending`，区分 executable / metadata_only / invalid / pending，按优先级处理同名冲突。

### 完成项

- [x] `SkillPackage` 补充字段：`source`、`source_type`、`priority`、`is_pending`、`status`、`errors`
- [x] `SkillDiscovery` 完整实现：
  - 扫描 4 个优先级路径 + 1 个 pending 路径
  - `_scan_one_dir` 处理完整状态判断（executable / metadata_only / invalid / skip）
  - 坏 frontmatter / 坏 JSON 标记为 invalid，不中断扫描
  - 同名冲突按优先级覆盖，低优先级结果进入冲突记录
  - `home_override` 参数支持测试态隔离
- [x] `SkillRegistry` 扩展：
  - `load_from_discovery` 批量加载
  - `get_executable()` / `get_invalid()` / `get_pending()` / `get_conflicts()` / `has_name()`
- [x] `tests/test_skill_discovery.py` — 17 项测试，覆盖 8 条验收：
  1. 扫描 `skills/` 发现有效 executable skill
  2. 扫描 home `~/.agents/skills` 成功
  3. 只有 `SKILL.md` 时标记 `metadata_only`
  4. pending skill 被发现但不注册为可执行
  5. 同名冲突时高优先级覆盖低优先级
  6. 名称不一致时标记 `invalid`
  7. 坏 frontmatter / 坏 JSON 不会中断整轮扫描
  8. 空目录和无关目录被忽略

### 验收结果

- `python3 -m unittest tests.test_skill_manifest`：24/24 通过
- `python3 -m unittest tests.test_skill_discovery`：17/17 通过
- `python3 -m unittest discover -s tests`：101/101 通过

### 改动文件

| 文件 | 改动 |
|------|------|
| `core/skills/skill_manifest.py` | `SkillPackage` 新增 6 个字段 |
| `core/skills/skill_discovery.py` | 骨架 → 完整实现 |
| `core/skills/skill_registry.py` | 骨架 → 完整实现 |
| `tests/test_skill_discovery.py` | 新建测试文件 |

### 下一步

进入 **P8-2 Skill Runner high 模式**：子进程执行、timeout、cwd 限制、limited_env、stdout 限制、stderr 日志。

---

## P8-0 尾项清理完成，准备进入 P8-1（2026-05-28）

目标：收掉 P8-0 验收后遗留的小问题，保证测试资产本身干净稳定，再进入 P8-1 Skill 发现与扫描路径实现。

### 完成项

- [x] 清理 `tests/test_skill_manifest.py` 中重复定义的 `TestMetadataOnlyDetection`
  - 删除被后一个同名类覆盖的死测试定义，避免对覆盖率和维护判断造成误导
- [x] 修复 `tests/test_tts_start_order.py` 手工事件循环未关闭问题
  - `_run_loop()` 在 `run_forever()` 返回后统一 `loop.close()` 并清理线程内 event loop
  - 收掉 `python3 -m unittest discover -s tests` 末尾的 `unclosed event loop` / `unclosed socket` ResourceWarning
- [x] 清理测试启动期的环境噪声 warning
  - 在测试文件入口定向忽略本机 Python 3.9 + LibreSSL 触发的 `urllib3.exceptions.NotOpenSSLWarning`
- [x] 同步更新进度文档与问题记录，明确当前可以从 P8-0 平滑切到 P8-1

### 验收结果

- `python3 -W error::ResourceWarning -m unittest tests.test_tts_start_order.ChatLlmTimeoutTest -v`：4/4 通过，不再出现 `unclosed event loop`
- `python3 -m unittest tests.test_skill_manifest`：24/24 通过
- `python3 -m unittest discover -s tests`：84/84 通过

### 下一步

进入 **P8-1 Skill 发现与扫描路径**：
- 在 `SkillDiscovery` 中实现扫描 `skills/`、`data/skills/enabled`、`~/.agents/skills`
- 复用现有 manifest 解析与 name consistency 校验
- 将发现结果注册到 `SkillRegistry`，并区分 executable / metadata_only / invalid

---

## P8-0 配置与 manifest 冻结实施完成（2026-05-28）

目标：把 P8 方案里的抽象内容落成后端可执行前的基础约定 — config.yaml skills 配置骨架、core/skills/ 包骨架、manifest 类型系统、首批测试。

### 完成项

- [x] `config.yaml` 新增 `skills:` 完整配置骨架
  - `enabled`、`security_level`、`allow_home_agents`、`enable_github_import`、`require_review`
  - `paths`（project_enabled / project_builtin / project_agents / home_agents / pending / disabled）
  - `runner`（high/low timeout、stdout_limit、env_allowlist）
  - `global_defaults`（enabled、phase、selected、max_skills_per_turn、total_timeout、context_char_limit、include_fast_path）
  - `on_demand`（enabled、allow_all_enabled、selected 含 trigger_mode）
- [x] 新建 `core/skills/` 包骨架，含 8 个模块：
  - `skill_manifest.py` — 最小 dataclass（SkillManifest / SkillTriggers / SkillAutoRun / SkillExecutableManifest / SkillPackage）+ 解析函数（parse_skill_md / parse_xiaoclaw_skill_json / validate_name_consistency）+ `is_valid_trigger_mode`
  - `skill_discovery.py` — 空骨架类
  - `skill_runner.py` — 空骨架类
  - `skill_matcher.py` — 空骨架类
  - `skill_global.py` — 空骨架类
  - `skill_importer.py` — 空骨架类
  - `skill_registry.py` — 空骨架类
  - `__init__.py` — 统一导出
- [x] `tests/test_skill_manifest.py` — 21 项测试，覆盖 6 条验收：
  1. 解析 `SKILL.md` frontmatter（有效/无 frontmatter/缺 name/缺 description）
  2. 解析 `xiaoclaw.skill.json`（有效/文件不存在/非法 JSON/缺 name）
  3. name / 目录名 / manifest name 三者必须一致（全部一致/单项不一致/模式校验）
  4. 没有 `xiaoclaw.skill.json` 时标记为 metadata_only
  5. `auto_run.enabled=true` 只是能力声明，不代表自动全局运行
  6. `trigger_mode` 只允许 `explicit_only` / `explicit_or_keywords` / `llm_tool_call`
- [x] 更新进度文档记录 P8-0 实施完成

### 验收结果

- `python3 -m unittest tests.test_skill_manifest`：21/21 通过
- 不创建新稳定备份（P8-0 为实施准备任务，不涉及主链路改动）

### 下一步

进入 **P8-1 Skill 发现与扫描路径**：实现 `SkillDiscovery` 扫描 `~/.agents/skills`、`data/skills/enabled`、`skills/` 等路径，解析 manifest 并注册到 `SkillRegistry`。

---

## P8 完整方案保存：自定义 Skill / Agent Loop / 全局默认 Skill（2026-05-28）

目标：在正式编码前，把 P8 整体范围、外部 Skill 资产兼容方式、安全边界、全局默认 Skill 能力和小任务拆分先固定下来，作为后续逐步实施的基础。

### 完成项

- [x] 新增完整方案文档：`P8完整方案_自定义Skill与Agent循环_XiaoClawBrain.md`
- [x] 明确 P8 总目标：后端 Skill 兼容层 + 最小 Agent 调度，不直接移植 OpenClaw / OpenCode / Hermes / Codex runtime
- [x] 明确兼容资产：`SKILL.md`、`scripts/`、`references/`、`templates/`、`assets/`、`~/.agents/skills`
- [x] 明确执行声明：新增 `xiaoclaw.skill.json`，没有该文件的导入 Skill 只能被发现，不能被执行
- [x] 增加全局默认 Skill 设计：
  - 支持 `auto_run.enabled=true`
  - 最终是否每轮默认运行由后端 `global_defaults.selected` 显式选择
  - 未被选中的 Skill 只在显式触发或匹配命中时按需运行
  - 第一版优先支持 `phase=before_llm`
  - 默认不影响 fast path
  - 默认失败策略为 `continue`
  - 受 timeout、数量上限、stdout 上限和安全等级约束
- [x] 补齐按需 Skill 判断策略：
  - `explicit_only`：仅响应“运行技能 / 使用技能 / 调用技能”
  - `explicit_or_keywords`：显式触发或保守关键词命中
  - `llm_tool_call`：保留字段，P8 第一版默认关闭
- [x] 将 P8 拆分为 P8-0 到 P8-10，便于后续逐项实施和验收
- [x] 同步更新 `START_HERE_项目速览.md`、架构方案文档、路线图中的 P8 口径

### 验收结果

- P8 方案已保存为独立文档
- 当前下一步仍为 P8，不提前启动 P9
- 当前稳定基底仍为 `XCB-STABLE-P7-complete+20260528.142020`；本轮为文档设计更新，不单独创建稳定备份

### 下一步

从 P8-0 / P8-1 开始实施：先冻结配置和 manifest，再实现 Skill 发现与解析。

---

## 文档口径与问题编号对齐（2026-05-28）

对齐 P7 收口完成后的文档状态，清理重复问题编号，确保后续从 P8 继续时不再被旧口径误导。

### 完成项

- [x] 更新 `START_HERE_项目速览.md`
  - `当前执行路线` 日期对齐到 2026-05-28
  - 当前实际进度改为 `P7 已完成收口验收`
  - `当前下一步` 改为进入 `P8 自定义 Skill 与 Agent 循环`
- [x] 更新 `开发问题解决记录.md`
  - 将 Fast Path 第二版修补条目从重复的 `问题 L` 顺延为 `问题 M`
  - 保留原 `问题 L`（DeepSeek `max_tokens: 120` 截断长回复）编号不变，避免历史引用歧义
- [x] 更新 `CLAUDE.md`
  - Current Direction Snapshot 明确 `P7 已完成`、`当前方向为 P8`
- [x] 回看当前进度文档，删除“本轮验收通过后即算完成”这类过渡表述，统一为已完成态

### 验收结果

- 文档口径已统一为：`P7 收口完成` → `下一步 P8`
- `开发问题解决记录.md` 不再存在重复的 `问题 L`

### 下一步

继续按稳定基底 `XCB-STABLE-P7-complete+20260528.142020` 推进 P8 自定义 Skill 与 Agent 循环，不提前启动 P9。

---

## Fast Path 收窄修正 + `actual_text` JSON 覆盖 Bug 修复（2026-05-28 第二版）

P7 稳定基底上修复 fast path 的两个问题。本轮验收已通过，P7 收口完成。

### 完成项

- [x] 收窄 `time_words`：只匹配 `几点`，移除上一版错误纳入的 `几时` / `什么时候` / `啥时间` / `当前时间` / `现在时间`
  - 根因：上一版把 `什么时候`/`啥时间`/`几时` 纳入 fast path，导致 `你什么时候有空`、`现在什么时候出发` 等非时间问句也走了本地快答
- [x] 修复 `startToChat()` JSON 输入时 `actual_text` 被原始 `text` 覆盖
  - 文件：`receiveAudioHandle.py:58-62`，去掉 `actual_text = text` 重置逻辑
  - 副产物：`language_tag = data.get("language")` 避免缺字段抛 KeyError
- [x] 重写测试 34 个，覆盖：
  - 应命中 13 项（`现在几点了？`、`几点`、`几点了现在`、`现在几点了啊` 等含"几点"的话术）
  - 不应命中 13 项（`当前时间是多少？`、`现在什么时候`、`几时了`、`你什么时候有空` 等不含"几点"的问法）
  - 边界/异常 4 项（空、None、纯空白、纯标点）
  - 混合优先级 2 项（天气/新闻优先）
  - JSON 输入集成测试 1 项（`{"speaker":"user1","content":"现在几点了？","language":"zh"}` → 验证 `get_fast_path_reply` 收到 `content`、`send_stt_message` 发 `content`、不走 LLM executor、说话人信息保留、TTS 入队）
- [x] 修复测试夹具：`TIME_FIXTURE` lambda 被类绑定为 bound method 问题

### 验收结果

- `python3 -m unittest tests.test_fast_path`：34/34 通过
- `python3 -m unittest discover -s tests`：60/60 通过（原 26 + 新增 34）
- 语法检查：三个改动文件全部通过

### 下一步

P7 收口工作至此全部完成。后续方向为 P8 自定义 Skill 与 Agent 循环。不提前启动 P9。

---

## P7 收口体验修补完成（2026-05-28）

目标完成。以 `XCB-STABLE-0.9.3+20260527.193019` 为基底，修补了主链路收口和播放体验问题。

### 完成项

- [x] 查明并修复 LLM / 工具轮自然 `LAST -> tts stop -> idle` 收口链路（根因：BaiduTTS `_process_remaining_text_stream(is_last=True)` 在剩余文本非空时未调用 `_process_before_stop_play_files()`，导致音频队列无 `SentenceType.LAST`，`send_tts_message("stop")` 永不执行）
- [x] 调整 timeout 边界：`round_timeout: 35` → `120`（极端死锁兜底），保留 ASR 5s / LLM effective 20s / TTS 8s / tool_call 30s 分段 timeout 作为主要防卡死前锋
- [x] `playback_drain_timeout: 90` → `120`，`send_tts_message("stop")` 先清 round timer，日志标注 `playback drain phase started`
- [x] 增加 BOOT 短按停止当前播放：`XiaoClawAbortCurrentPlayback()` → `SendAbortMessage()` + `ClearPlaybackQueues()` + 回 `idle`
- [x] `abortHandle.py`：`hasattr` 防御清 `round_timer` / `llm_watchdog`
- [x] 补齐 BaiduTTS abort 收尾：`client_abort` 后丢弃 `pcm_buffer` 残留，不再把半截 PCM 编码进后续音频队列
- [x] 修复长回复半句截断：`DeepSeekLLM.max_tokens: 120` → `500`，保留语音预算软提示但移除过低硬上限

### 暂缓项（后续单独排查）

- [x] `现在几点了？` fast path 偶发未命中（已于 2026-05-28 修复，见上方 Fast Path 修补条目）
- [ ] LLM 按意图动态语音预算（普通短答、故事/教程类自动放宽）
- [ ] 用户打断后立即重新拾音（第一版不做）
- [ ] LLM 句子级硬截断

### 验收结果

- `python3 -m unittest discover -s tests`：30/30 通过（后续 Fast Path 两版修补后增至 60/60）
- BaiduTTS 单测：`test_baidu_tts_last.py` 3/3 通过（TDD 流程确认红灯→绿灯）
- 语法检查：`baidu.py` / `sendAudioHandle.py` / `abortHandle.py` 全部通过
- 后端 Docker：`docker compose -f docker-compose_all.yml build xiaozhi-esp32-server` 通过
- 固件改动：`idf.py build` 通过，生成 `build/xiaozhi.bin`
- 实机复测：普通 LLM 轮、weather 工具轮、长故事轮、播放中 BOOT abort 均通过；长故事不再半句截断，BOOT abort 后后端继续收到 ping，无 `ROUND_TIMEOUT` / `TTS_TIMEOUT` 尾巴
- 连续实机复测：连续 5 轮通过（普通身份问答、小笑话、新闻工具轮、个性判断、功能说明），每轮均出现自然 `SentenceType.LAST` 和 `playback drain phase started`；后续 ping 正常，无 `ROUND_TIMEOUT` / `TTS_TIMEOUT` / 连接断开 / 状态残留
- 文档同步：`项目开发进度_XiaoClawBrain.md` / `开发问题解决记录.md` / `START_HERE_项目速览.md` / `CLAUDE.md` / `AGENTS.md` 全部更新

### 下一步

P7 收口工作至此全部完成。后续方向为 P8 自定义 Skill 与 Agent 循环。

### 稳定版本与备份

- 稳定版本标记：`XCB-STABLE-P7-complete+20260528.142020`
- 非敏感全资产备份：`backup_snapshot_20260528_142020_full`
- 敏感本机资产备份：`backup_sensitive_20260528_142020`
- 备份轮转：这是新规则下第 2 个稳定全资产基底；当前稳定基底数量未超过 3 个，不删除旧稳定备份

---

## DeepSeek Key 明文迁移（2026-05-27）：默认配置脱敏，私有配置继续覆盖

目标：处理默认 `config.yaml` 中误写入的 DeepSeek 明文 API Key，迁移到与其他敏感信息一致的本机私有配置位置，并保证现有调用路径不变。

### 完成项

- [x] 确认配置加载顺序：默认 `config.yaml` 与 `data/.config.yaml` 递归合并，私有配置优先
- [x] 确认 `main/xiaozhi-server/data/.config.yaml` 被 `.gitignore` 忽略
- [x] 将默认 `LLM.DeepSeekLLM.api_key` 改回空值模板
- [x] 新增回归测试，防止默认 DeepSeek 配置再次出现真实形态 API Key
- [x] 验证合并后 DeepSeek Key 仍由私有配置提供，运行态为 `SET`

### 当前结论

DeepSeek 真实 Key 不再保存在默认配置文件中；本机运行仍通过 `data/.config.yaml` 覆盖默认模板，调用路径保持不变。

### 下一步

继续处理 P7 实机复测报告中的剩余问题：优先排查 BaiduTTS 文本 `LAST` 到音频 `LAST` 的收口链路，再做 LLM 语音预算硬约束。

---

## 当前下一步文档口径对齐（2026-05-27）：进入百度 STREAM 实机稳定性复测

目标：修正旧文档中仍指向 P1 或粗粒度 P5/P7 的过期口径，统一后续助手接手时的执行方向。

### 完成项

- [x] 更新 `START_HERE_项目速览.md`：当前执行路线改为 2026-05-27 对齐版
- [x] 更新 `项目开发路线图_XiaoClawBrain.md`：当前已知状态改为 0.9.3 稳定基底后的真实状态
- [x] 更新 `CLAUDE.md`：当前方向改为 P7 百度 STREAM ASR/TTS 实机稳定性复测
- [x] 明确 P8 Skill/Agent 和 P9 本地语音唤醒暂不提前启动

### 当前结论

项目当前不再回到 P1；后续开发以 `XCB-STABLE-0.9.3+20260527.193019` 为基底，优先验证百度 `InterfaceType.STREAM` 接入后的真实连续对话稳定性。

### 下一步

执行 P7 连续实机复测，重点观察 `listen start -> binary audio -> listen stop -> recognizing -> ASR final -> thinking/fast path -> tts start -> speaking/tts stop -> idle`，确认无 `ASR_TIMEOUT`、误触发 `LLM_TIMEOUT/ROUND_TIMEOUT`、延迟 `tts stop`、`speaking` 卡死和状态残留。

---

## 稳定备份基底确定（2026-05-27）：以 0.9.3 作为后续开发版本基础

目标：把本次全资产备份定为后续开发基底，并明确后续只备份双方确认的稳定版本，避免临时迭代快照堆积和版本编号混乱。

### 完成项

- [x] 确认当前后端运行时版本为 `0.9.3`
- [x] 确认后端 `config.yaml` 的 `xiaozhi.version: 1` 是设备 hello/协议配置版本，不作为项目发布版本号
- [x] 将当前稳定基底标记为 `XCB-STABLE-0.9.3+20260527.193019`
- [x] 将非敏感全资产备份 `backup_snapshot_20260527_193019_full` 作为稳定基底主体
- [x] 将敏感本机资产备份 `backup_sensitive_20260527_194242` 作为稳定基底的私有补充
- [x] 在 `START_HERE_项目速览.md` 和 `CLAUDE.md` 中新增稳定备份规则

### 新规则

后续开发过程只在双方讨论并确认“稳定版本”时创建新的全资产备份；两个稳定版本之间的普通开发迭代不做全资产备份。稳定全资产备份只保留最新 3 个，创建第 4 个稳定全资产备份时删除最旧的稳定全资产备份。备份和继续开发时必须明确记录稳定版本编号。

### 当前结论

当前后续开发基础版本为 `0.9.3`，稳定基底编号为 `XCB-STABLE-0.9.3+20260527.193019`。这是新规则下的第 1 个稳定全资产基底，因此本次不删除旧快照；旧的时间戳快照暂按历史临时快照看待，不计入新的“最新 3 个稳定版本”轮转。

### 下一步

继续以 `XCB-STABLE-0.9.3+20260527.193019` 为基底做百度流式 ASR/TTS 实机复测；只有当复测后双方确认进入下一个稳定状态时，再创建新的稳定全资产备份。

---

## 百度语音接入（2026-05-27）：实时 ASR/TTS 已切换

目标：用百度智能云 ASR/TTS 降低本地音频生成耗时，并保持后端到 ESP32 的 binary Opus 小包协议不变。

### 完成项

- [x] 百度 OAuth 连通测试通过：返回 access token，约 0.18-0.52s
- [x] 百度 TTS 裸 API 测试通过：短文本返回 16k PCM，约 0.37-0.56s
- [x] 新增 `BaiduTokenProvider`，统一处理百度 OAuth token 缓存，不把 token/key 写入日志
- [x] 用户提供百度实时语音识别官方文档后，确认前一次 ASR 失败是测到了短语音 REST 产品线
- [x] 按实时 ASR WebSocket API 重写 `BaiduASR`，使用 `appid + appkey + dev_pid=15372`
- [x] 新增 `BaiduTTS` provider：HTTP 流式接收 PCM chunk，边收边转 Opus 入队
- [x] 默认配置只增加空模板；真实百度凭据只写入本机 `data/.config.yaml`
- [x] 本机私有配置已切换：`selected_module.TTS = BaiduTTS`
- [x] 本机私有配置已切换：`selected_module.ASR = BaiduASR`
- [x] 实机首轮复测触发到 `BaiduASR`，但批量喂实时 ASR 时因模拟实时发送间隔导致 5s `ASR_TIMEOUT`
- [x] 将非流式批量识别的 `send_interval` 调整为 `0`，录音结束后尽快推完 PCM，再继续复测
- [x] 批量模式二次复测仍在 5s ASR watchdog 触发 `ASR_TIMEOUT`
- [x] 已切换为真正 `InterfaceType.STREAM`：第一帧音频到达时建立百度实时 ASR WebSocket，录音过程中边解 Opus 边推 PCM，stop 时发送 `FINISH`
- [x] STREAM 模式 stop 后立即发送 `recognizing` 状态并启动 round timer，保持协议顺序稳定

### 验证结果

- [x] 百度短语音 REST ASR 测试未通过：OAuth token scope 缺少短语音 REST 识别权限，`dev_pid=1537/80001/1737` 均返回 `3302 No permission to access data`
- [x] 百度实时 ASR WebSocket 测试通过：返回 `FIN_TEXT err_no=0`
- [x] 单元测试通过：`python3 -m unittest discover -s tests`，24 个测试通过
- [x] 语法检查通过：`PYTHONPYCACHEPREFIX=/private/tmp/xcb_pycache python3 -m py_compile core/providers/baidu_token.py core/providers/asr/baidu.py core/providers/tts/baidu.py core/handle/sendAudioHandle.py core/handle/receiveAudioHandle.py tests/test_baidu_token.py`
- [x] 后端镜像重建并重启，容器 healthy
- [x] 容器内配置确认：`selected_tts=BaiduTTS`，`selected_asr=BaiduASR`，`asr_dev_pid=15372`
- [x] 容器内百度 TTS provider 冒烟测试通过：短文本生成后 Opus 队列有数据
- [x] 调整 `send_interval=0` 后重新构建并重启后端，容器 healthy
- [x] 全量单元测试通过：`python3 -m unittest discover -s tests`，24 个测试通过
- [x] STREAM 版语法检查通过：`PYTHONPYCACHEPREFIX=/private/tmp/xcb_pycache python3 -m py_compile core/providers/asr/baidu.py core/handle/textHandler/listenMessageHandler.py`
- [x] STREAM 版后端镜像已重建并重启，容器 healthy
- [x] 容器内确认：`BaiduASR interface=InterfaceType.STREAM`

### 当前结论

百度实时 ASR/TTS 已接入并切换到运行配置。前一次 `3302` 是短语音 REST 产品线权限不匹配，不代表实时 ASR 不可用。批量喂实时 ASR 在实机中仍压不进 5s ASR timeout，已按目标切到真正流式推帧。

### 下一步

继续做设备实机复测，重点观察 `listen start -> binary audio -> listen stop -> recognizing -> ASR final -> thinking/fast path -> speaking -> idle`，确认无 `ASR_TIMEOUT` 和状态残留。

---

## timeout 分层收口与本地 fast path（2026-05-27）

目标：先减少误伤型 `ROUND_TIMEOUT` 和短问题无谓 LLM/工具耗时，再等待百度流式 ASR/TTS API 信息做云端音频链路切换。

### 完成项

- [x] 拆分“回复生成总轮次 timeout”和“TTS 已生成音频播放 drain timeout”
  - 35s `round_timeout` 继续覆盖 ASR/LLM/TTS 生成阶段
  - TTS 生成完成并发送 `tts stop` 前，先清理 round watchdog
  - 已进入播放发送队列的音频使用独立 `playback_drain_timeout: 90` 等待自然发完
  - 播放队列自身 drain 超时后发送 `TTS_TIMEOUT -> idle`，不再伪装成 `ROUND_TIMEOUT`
- [x] 新增本地确定性 fast path：
  - 「现在几点」「当前时间」等时间问题本地直接回复
  - 天气、新闻类问题明确不走 fast path，继续留给工具/LLM
  - fast path 仍经过绑定检查、输出上限和播放打断处理
- [x] 为 fast path 懒加载常规意图模块，避免短问题提前加载工具/Opus 相关重依赖
- [x] 新增 `tests/test_fast_path.py`，补充播放 drain 分层测试

### 验证结果

- [x] 专项测试通过：`python3 -m unittest tests.test_tts_start_order tests.test_fast_path`，22 个测试通过
- [x] 全量后端测试通过：`python3 -m unittest discover -s tests`，22 个测试通过
- [x] 语法检查通过：`PYTHONPYCACHEPREFIX=/private/tmp/xcb_pycache python3 -m py_compile core/connection.py core/handle/sendAudioHandle.py core/handle/receiveAudioHandle.py core/handle/fastPath.py tests/test_tts_start_order.py tests/test_fast_path.py`
- [x] 后端镜像已重建并重启，`xiaozhi-esp32-server` 状态 healthy
- [x] 新建快照 `backup_snapshot_20260527_155208`，不覆盖旧快照

### 当前结论

这轮先修掉“LLM/TTS 生成已经结束，但播放队列还没按实时语速发完就被 35s round watchdog 判死”的误判路径。短问题中的“现在几点”已可绕开 LLM 和工具，降低连续多轮下的平均延迟。

### 下一步

等待用户提供百度流式 ASR/TTS API 信息与凭据后，先做最小化 API 连通测试，再接入 provider 并切换 `data/.config.yaml`，不把任何密钥写入源码、日志或提交文档。

---

## 连续对话实机复测与后端 timeout 再修补（2026-05-27）：LLM 有效首包 watchdog

目标：验证本轮 timeout 修补在真实多轮场景中的稳定性，重点观察普通短问题、工具调用倾向问题、长问题下的 `LLM_TIMEOUT` / `ROUND_TIMEOUT` / `tts stop` / `idle` 收口。

### 实机复测结果

- [x] 后端容器重建并运行新镜像，healthcheck 为 healthy
- [x] 设备重新连接后端并回到 `idle`
- [x] 连续实机复测同一 session：`8c066f28-4801-439f-b73e-94ae23448c32`
- [x] 普通短问题：
  - 「你好，你是谁？」：ASR 正常，LLM 约 11s 首包，进入 `thinking -> tts start -> speaking`，但 TTS 队列在 35s deadline 被截断，收到 `tts stop -> ROUND_TIMEOUT -> idle`
  - 「现在几点了？」：ASR 正常，LLM 正常首包，TTS 正常 stop，设备回 `idle`
  - 「讲一个小笑话。」：ASR 正常，LLM 正常首包，TTS stop，设备回 `idle`
- [x] 工具调用倾向问题：
  - 「今天的天气怎么样？」：ASR 正常，LLM 正常首包，TTS stop，设备回 `idle`
  - 「今天有什么新闻？说重点。」：触发 `get_news_from_newsnow`，递归 chat 能收口并生成 TTS，但 TTS 队列在 35s deadline 被截断，收到 `tts stop -> ROUND_TIMEOUT -> idle`
- [x] 长问题/异常：
  - 「用两句话解释一下量子计算。」：ASR 正常并进入 `thinking`，但没有 LLM/TTS 首包，最终退化为 35s `ROUND_TIMEOUT`
  - 超长问题：ASR 阶段 5s 超时，后端发送 `ASR_TIMEOUT -> idle`，未进入 LLM

### 发现问题

- [x] 发现新的真实问题：LLM 进入 `thinking` 后，如果同步生成器线程没有及时向外抛出 per-chunk timeout，设备会等到 35s `ROUND_TIMEOUT`，没有按预期在 20s 收到 `LLM_TIMEOUT`
- [x] 现象时间戳：后端 `14:16:34` ASR 出「用两句话解释一下量子计算。」并进入 LLM，`14:17:09` 才触发 `Round watchdog fired after 35.0s`
- [x] 设备状态流转：`idle -> wakeup_detected -> listening -> uploading_audio -> recognizing -> thinking -> error -> idle`

### 修补项

- [x] `ConnectionHandler` 新增独立 LLM 有效 chunk watchdog：
  - 进入 LLM streaming 后启动
  - 20s 内没有有效文本、工具调用或 reasoning chunk 时，直接发送 `state=error -> LLM_TIMEOUT -> state=idle`
  - 不再只依赖同步生成器线程的 `future.result(timeout)`
- [x] 保留原有 `_timeout_iter` per-chunk timeout 和 35s round watchdog，形成两级兜底
- [x] 针对普通回复仍容易拖到 35s 的情况，收紧语音预算配置：
  - `voice_reply_budget.target_speech_seconds: 25`
  - `voice_reply_budget.max_chinese_chars: 80`
  - `voice_reply_budget.max_sentences: 2`
  - `DeepSeekLLM.max_tokens: 120`
- [x] 新增/更新单元测试：
  - function_call 空 chunk 持续保活时触发 `LLM_TIMEOUT`，不退化为 `ROUND_TIMEOUT`
  - 独立 LLM watchdog 在 worker 无进展时发送 `LLM_TIMEOUT`

### 验证结果

- [x] 后端单测通过：`python3 -m unittest discover -s tests`，17 个测试通过
- [x] 语法检查通过：`PYTHONPYCACHEPREFIX=/private/tmp/xcb_pycache python3 -m py_compile core/connection.py tests/test_tts_start_order.py`
- [x] 后端镜像重建并重启，容器状态 healthy
- [x] 后修补后的「量子计算」实机复测已捕捉：
  - 后端 `14:34:31` ASR 出文本并进入 LLM
  - 后端 `14:34:44` 发送第一段 TTS，说明不再是 LLM 无首包卡死
  - 后端 `14:35:06` TTS 队列等待超时并 `ROUND_TIMEOUT` 收口，说明普通回复仍可能过长
- [x] 收紧语音预算后再次重建/重启后端，容器 healthy

### 当前结论

本轮连续复测证明：协议顺序整体稳定，设备在 timeout 后能回到 `idle`，未观察到永久 `speaking` 卡死；修补前存在 `thinking` 无首包退化为 `ROUND_TIMEOUT` 的问题，已通过独立 LLM watchdog 修补。后续复测显示 LLM 首包正常，但普通回复仍可能因 TTS 过长触发 `ROUND_TIMEOUT`，已先用最小配置改动收紧生成预算。

### 下一步

继续做 1 轮针对性实机复测：「用两句话解释一下量子计算。」预期结果为正常首包、TTS 自然 stop、设备回 `idle`，不再靠 `ROUND_TIMEOUT` 收尾。

---

## 文档与备份对齐完成（2026-05-27）：问题记录整理 + 后端超时修补新快照

目标：把项目进度、问题记录和当前后端 timeout 修补现实情况重新对齐，并在不覆盖旧目录的前提下补一份新的时间戳快照。

### 完成项

- [x] 整理 `开发问题解决记录.md`：把最新的 `问题 F` 和 `问题 E` 放到顶部，修正错位标题，删除重复的旧 `问题 E` 段落
- [x] 校对当前 timeout 修补现状：`LLM_TIMEOUT` 显式错误协议、递归 `chat(depth>0)` 漏口修补、15 个单测通过
- [x] 新建快照目录 `backup_snapshot_20260527_120432`，保留本轮最新根目录文档和后端 timeout 相关关键文件
- [x] 在新快照中新增 `BACKUP_README.md`，记录后端/固件分支、HEAD 和本轮快照覆盖范围

### 当前状态

当前文档已经和代码现实对齐；旧快照 `backup_snapshot_20260527_102417` 保留不动，新增快照用于保存本轮后端 timeout 修补后的稳定参考版本。

### 下一步

继续做连续对话稳定性测试，重点观察多轮工具调用、递归 `chat()` 和长回复场景下 `LLM_TIMEOUT` / `ROUND_TIMEOUT` 的实际触发分布。

---

## 递归 timeout 漏口修复完成（2026-05-27）：depth>0 统一走 LLM_TIMEOUT + 2 个 chat() 协议收口测试

目标：修掉递归 chat(depth>0) 的 LLM timeout 漏口，保证无论哪层递归命中 llm_timeout 都统一发送 state=error → LLM_TIMEOUT → state=idle。

### 完成项

- [x] **移除 depth==0 守卫**：`except LLMChunkTimeoutError` 不再区分 depth，任何深度都调用 `_fail_llm_timeout()`
- [x] **同步等待协议发送完毕**：`_fail_llm_timeout` 通过 `future.result(timeout=5)` 等待，确保返回前协议已发出
- [x] **新增 2 个协议收口测试**：
  - `test_top_level_chat_sends_llm_timeout_protocol`：顶层 chat(depth=0) 超时 → 断言存在 `state=error`、`LLM_TIMEOUT`、`state=idle`
  - `test_recursive_chat_sends_llm_timeout_protocol`：工具调用后递归 chat(depth>0) 超时 → 同一断言

### 验证结果

- [x] 全部 15 个单元测试通过：`python3 -m unittest discover -s tests`
- [x] 语法检查通过

### 当前状态

递归 chat(depth>0) 的 LLM 超时已覆盖。LLM_TIMEOUT 协议在任何深度命中时都会统一发送。

---

## 当前快照备份完成（2026-05-27）：关键文件与固件回滚快照

目标：在继续后续修改前，把当前后端关键文件、固件关键源码和当前可烧录固件产物做一次独立快照，保证下一轮改动出问题时可以快速回滚。

### 完成项

- [x] 新增根目录快照目录 `backup_snapshot_20260527_102417`
- [x] 备份根目录关键文档、后端关键配置与核心实现、固件关键源码与板级配置
- [x] 备份当前固件产物：`xiaozhi.bin`、`bootloader.bin`、`partition-table.bin`、`flasher_args.json`
- [x] 在快照目录中新增 `BACKUP_README.md`，记录当前后端/固件分支、HEAD 和恢复说明
- [x] 记录固件二进制校验值，便于后续确认备份完整性

### 当前状态

当前已经有一份可直接回滚的工作快照。后续如果新改动破坏了当前可用状态，可以优先从该快照恢复关键文件，或直接回用当前备份的固件产物。

### 下一步

继续按测试模板做 BOOT 实机复测；如果后续进入下一轮较大改动，建议在改动前再生成一个新的时间戳快照，不覆盖本次备份。

---

## P7 文档整理完成（2026-05-27）：长回复 / TTS 收尾测试报告模板

目标：把“长回复 / TTS 收尾过慢”验证流程整理成可直接交给 AI 助手执行和回填的独立文档，减少后续测试沟通成本。

### 完成项

- [x] 新增根目录文档 `XiaoClaw后端长回复TTS测试报告模板.md`
- [x] 文档中整理了测试目标、执行顺序、代码验证命令、Docker 重启步骤、三类实机测试项和统一汇报模板
- [x] 增加验收口径，方便后续快速判断“可以继续稳定性测试”还是“需要继续修补”

### 当前状态

当前已经有一份可独立流转的测试文档，后续无论是人工测试还是交给 AI 助手执行，都可以直接按模板回填结果。

### 下一步

继续按模板做 BOOT 实机复测，优先验证普通长问题是否自然缩短、超长问题是否仍能由 35 秒 timeout 正常收口。

---

## 后端超时返工完成（2026-05-27）：LLM_TIMEOUT 显式错误协议 + per-chunk 超时 + 新增 5 个测试

目标：返工修复 v1 的遗留问题：`_timeout_iter` 使用累计预算而非 per-chunk 超时，超时后静默 break 不发送错误协议。

### 完成项

- [x] **`_timeout_iter` 改为 per-chunk 独立超时**：每次 `next()` 独立计时 `timeout_seconds`，不是累积总预算
- [x] **超时抛 `LLMChunkTimeoutError` 异常**：不再静默 break，上游 `chat()` 通过异常捕获知晓超时
- [x] **`LLMChunkTimeoutError` 显式错误协议**：`chat()` 捕获后调用 `_fail_llm_timeout()` 发送 `state=error → LLM_TIMEOUT → state=idle`
- [x] **`_fail_llm_timeout()`**：新 async 方法，先发 tts stop（如正在播放），再发 error → LLM_TIMEOUT → idle，清除队列和计时
- [x] **独立 round watchdog 保持不变**：流循环进不去时 35s 仍能触发 ROUND_TIMEOUT
- [x] **新增 5 个单元测试**：正常迭代、首包超时 → LLM_TIMEOUT、中途超时 → LLM_TIMEOUT、watchdog 流不进触发 ROUND_TIMEOUT、watchdog 正常完成不触发

### 实机复测

- [x] 短问题「你好，你是谁？」：LLM 3s 响应，完整播完，正常回 idle

### 当前状态

本轮返工修补通过。两级保护已到位：
1. httpx read=20s + `_timeout_iter` per-chunk 超时 → `LLM_TIMEOUT` 显式错误
2. 独立 round watchdog 35s → `ROUND_TIMEOUT`

所有新增测试覆盖了首包超时、中途超时、流不进三种场景。

### 下一步

进入连续对话稳定性测试。观测多轮交互中 llm_timeout 和 round_timeout 覆盖率是否足够。如有需要可优化「watchdog 后队列中多余 TTS 裁剪」。

## 当前总状态

- 当前阶段：**P7 XiaoClaw WS 已连通，OpenClaw V2.7 BOOT 主链路已打通，后端 TTS start / thinking 状态顺序、长回复 timeout 收尾和语音短回复预算已修补**
- 后端主线：分支 `xiaoclawbrain-v0.1`，基准 commit `fb7e2e1`
- ESP32 主线：分支 `xiaoclaw-ghproxycom`，已完成 OpenClaw V2.7 板型修复、配网退出逻辑修复、XiaoClaw WS 运行时配置接入、STA 联网后 `:8080` 本地配置页写入 `Settings("websocket")`，以及 P1-P8 WakeNet 缺失时的本地降级
- 下一步任务：继续触发 BOOT 长回复实机复测，重点观察普通长问题是否自然压缩到约 30 秒语音内完成，异常长回复是否仍由 `ROUND_TIMEOUT` / `tts stop` / `idle` 收口

---

## P7 修补完成（2026-05-27）：后端长回复语音短回复预算修补

目标：在保留 35 秒单轮硬 timeout 的基础上，给后端 LLM 增加语音短回复约束，尽量让普通回复在约 30 秒 TTS 内自然播完，而不是频繁依赖硬截断。

### 完成项

- [x] 新增配置项 `voice_reply_budget`，默认启用，目标 30 秒、约 120 中文字、最多 3 句，并配置自然收口提示
- [x] `ConnectionHandler` 新增 `_build_voice_reply_budget_instruction()`，生成“适合语音播放、1-3 句、不超过约 120 字、不展开长列表、自然完整收尾”的本轮约束
- [x] LLM 调用前通过 `_get_budgeted_llm_dialogue()` 临时追加本轮 system 指令，不写回 `self.dialogue`，避免永久污染历史对话
- [x] `OMLXLLM` 新增 `max_tokens: 180`
- [x] Ollama OpenAI-compatible provider 读取并传递 `max_tokens`，覆盖普通 streaming 和 `response_with_functions` 两条路径
- [x] 保留 35 秒 `round_timeout` 硬保险丝，不改变 WebSocket binary TTS 协议，不修改 ESP32 状态机

### 验证结果

- [x] 后端单测通过：`python3 -m unittest discover -s tests`
- [x] 后端语法检查通过：`PYTHONPYCACHEPREFIX=/private/tmp/xcb_pycache python3 -m py_compile core/connection.py core/providers/llm/ollama/ollama.py tests/test_tts_start_order.py`

### 当前状态

代码侧已增加 LLM 生成前的语音短回复预算和 Ollama 生成上限。普通长问题应更倾向于输出 1-3 句、约 120 中文字以内的完整回复；35 秒单轮 timeout 仍作为异常兜底负责丢弃剩余 TTS 队列、发送 `tts stop`、`ROUND_TIMEOUT` 和 `idle`。

### 下一步

继续 BOOT 长回复实机复测：重点观察普通长问题是否自然在约 30 秒语音内完成；如果实机仍常超长，再考虑增加流式句子边界裁剪，但必须避免等完整回复生成完才送 TTS。

---

## P7 修补完成（2026-05-26）：后端长回复 TTS 收尾与单轮 timeout 修补

目标：解决长回复场景下后端持续按播放速率 drain 历史 TTS binary frame，导致 ESP32 长时间停在 `speaking`、迟迟等不到 `tts stop` 的问题。

### 完成项

- [x] 定位根因：`send_tts_message(state="stop")` 会先等待 `AudioRateController.queue_empty_event`，长回复队列按 60ms 帧时长慢慢清空，`tts stop` 必须等队列 drain 后才发送
- [x] 新增后端回归测试，覆盖“单轮 deadline 已超时且 TTS 队列未清空时，必须丢弃剩余待发音频并快速发送 `tts stop`”
- [x] 后端新增单轮 35 秒计时：ASR 进入 `recognizing` 前启动，文本/BOOT 聊天入口兜底启动
- [x] `AudioRateController` drain 等待改为受单轮 deadline 约束；超时后清空剩余音频队列、取消后台发送任务，不再继续慢慢发送历史帧
- [x] 超时收尾发送 `tts stop`，随后发送 `ROUND_TIMEOUT` 错误和 `state=idle`
- [x] 新增配置项 `round_timeout: 35`

### 验证结果

- [x] 后端单测通过：`python3 -m unittest discover -s tests`
- [x] 后端语法检查通过：`PYTHONPYCACHEPREFIX=/private/tmp/xcb_pycache python3 -m py_compile core/handle/sendAudioHandle.py core/connection.py core/providers/asr/base.py core/handle/receiveAudioHandle.py tests/test_tts_start_order.py`
- [x] 后端镜像重建并重启服务：`docker build -f Dockerfile-server -t xiaozhi-esp32-server:local .`，随后 `docker compose -f docker-compose.yml up -d xiaozhi-esp32-server`
- [x] 容器内 healthcheck 通过：`{"status": "ok", "service": "xiaoclawbrain", ...}`
- [x] 串口确认设备重启后仍能连 WiFi、使用 runtime WS 地址连上新后端，并收到 `server hello`
- [ ] BOOT 长回复实机复测待继续触发，本轮 monitor 未捕捉到 BOOT 按键录音

### 当前状态

代码侧已优先在后端收口长回复 speaking 延迟，不放宽 ESP32 状态机，不改变 WebSocket binary TTS 协议，不恢复 base64 JSON 音频。长回复如果无法在单轮 35 秒内完成发送，后端会丢弃剩余待发音频并强制让设备回 `idle`。新镜像已运行，设备也已重新连上新后端；还需要继续做一次实际 BOOT 长回复触发验证。

### 下一步

继续实机 BOOT 主链路复测：重点观察长回复场景是否在 35 秒左右收到 `tts stop` / `ROUND_TIMEOUT` / `idle`，且不再长时间停留在 `speaking`。

---

## P7 修补完成（2026-05-26）：后端 TTS start / thinking 状态顺序修补

目标：解决 ESP32 收到 `tts_start` 后进入 `synthesizing`，随后又收到服务端 `state=thinking`，导致固件日志出现 `Invalid state transition: synthesizing -> thinking` 的状态顺序问题。

### 完成项

- [x] 定位根因在后端控制消息顺序：`send_stt_message()` 在 STT 返回后立即发送 `type=tts,state=start`，而 `ConnectionHandler.chat()` 随后才异步发送 `state=thinking`
- [x] 修补后端：STT 消息不再提前夹带 TTS start
- [x] 修补后端：第一段 TTS 音频即将发送时才发送 `type=tts,state=start`，并保持其位于 `sentence_start` 和 binary audio 之前
- [x] 修补后端：`chat()` 中等待 `state=thinking` 发送完成或超时后再处理快速 LLM/TTS 输出，降低 fast path 下的乱序风险
- [x] 新增后端回归测试 `tests/test_tts_start_order.py`，覆盖 STT 不提前启动 TTS、首段 TTS 音频发送前启动 TTS 两个行为

### 验证结果

- [x] 后端单测通过：`python3 -m unittest tests.test_tts_start_order`
- [x] 后端语法检查通过：`PYTHONPYCACHEPREFIX=/private/tmp/xcb_pycache python3 -m py_compile core/handle/sendAudioHandle.py core/connection.py tests/test_tts_start_order.py`
- [x] 后端镜像重建并重启服务：`docker build -f Dockerfile-server -t xiaozhi-esp32-server:local .`，随后 `docker compose -f docker-compose.yml up -d xiaozhi-esp32-server`
- [x] 容器内 healthcheck 通过：`{"status": "ok", "service": "xiaoclawbrain", ...}`
- [x] 实机 BOOT 复测通过：`recognizing -> thinking -> synthesizing -> speaking -> idle`，串口未再出现 `Invalid state transition: synthesizing -> thinking`

### 当前状态

代码侧已修补后端控制消息顺序，不修改 ESP32 状态机白名单，不改变 TTS binary frame 下发协议，不恢复 base64 JSON 音频。后端容器已运行新镜像，实机 BOOT 复测确认 `thinking` 已早于 `tts start` 到达，设备最终收到 `tts stop` 并回到 `idle`。

### 下一步

继续做连续 BOOT 对话稳定性复测。后续单独观察本轮暴露出的长回复/TTS stop 延迟：长回复期间设备会保持 `speaking`，需要确认是否需要按单轮 35 秒 timeout 做服务端截断或客户端恢复策略。

---

## P7 修补完成（2026-05-26）：OpenClaw V2.7 本地唤醒模型初始化降级

目标：解决 XiaoClaw WS 连通后出现 `MODEL_LOADER: Can not find model in partition table`、`AfeWakeWord: Failed to initialize wakenet model`、`AudioService: Failed to initialize wake word` 的新问题，同时不破坏阶段九本地语音唤醒必做路线。

### 完成项

- [x] 确认该问题发生在 `ws connected` 与 `server hello received` 之后，不是 WS `reconnecting` 根因
- [x] 确认 OpenClaw V2.7 阶段一至阶段八应优先使用 BOOT 按键录音入口，本地 WakeNet 不应强制初始化
- [x] 在 OpenClaw V2.7 板型配置和当前 `sdkconfig` 中显式选择 `CONFIG_WAKE_WORD_DISABLED=y`
- [x] `AudioService::EnableWakeWordDetection(true)` 在 `CONFIG_WAKE_WORD_DISABLED` 下直接 no-op，避免 idle 后懒加载 `AfeWakeWord`
- [x] `scripts/release.py` 增加 Wake Word choice 清理，避免 `idf.py set-target` 默认 `USE_AFE_WAKE_WORD` 残留
- [x] `AfeAudioProcessor` 不再在 BOOT 录音时主动探测 `model` 分区；没有外部 `models_list` 时直接走无模型 AFE / WebRTC VAD
- [x] 新增 `tests/test_openclaw_wake_word_policy.sh`，固化 OpenClaw V2.7 阶段一至阶段八的 wake word 降级策略

### 验证结果

- [x] 主机侧策略测试通过：`sh tests/test_openclaw_wake_word_policy.sh`
- [x] 运行时配置单测通过：`cc -I main/providers tests/test_openclaw_runtime_config_logic.c main/providers/openclaw_runtime_config_logic.c -o /tmp/test_openclaw_runtime_config_logic && /tmp/test_openclaw_runtime_config_logic`
- [x] 固件构建通过：`idf.py build` 输出 `Project build complete.`
- [x] 固件烧录成功：`idf.py -p /dev/cu.usbmodem5B5E1028821 flash` 输出 `Done`
- [x] 实机启动验证：WS 使用 runtime 地址连通，进入 `idle` 后不再出现 `MODEL_LOADER` / `AfeWakeWord` / `Failed to initialize wake word`
- [x] 实机 BOOT 主链路验证：`idle -> wakeup_detected -> listening -> uploading_audio -> recognizing -> synthesizing -> speaking`，上传首帧与约 50 帧 Opus，收到 STT、TTS start 和首个 TTS binary frame

### 当前状态

OpenClaw V2.7 在阶段一至阶段八不会再因为缺少 WakeNet/model 资源而初始化失败；BOOT 录音主链路仍可使用 AFE 的 WebRTC VAD 路径完成音频上传。阶段九本地语音唤醒仍是产品必做项，后续需要再单独评估 ESP-SR / WakeNet 模型资源分区和唤醒状态机接入。

### 下一步

继续做连续 BOOT 对话和异常恢复复测。本节后续发现的 `tts_start` / `thinking` 状态顺序问题已在 2026-05-26 的后端状态顺序修补中单独收口。

---

## P7 修补完成（2026-05-26）：STA 配置页写入 XiaoClaw WS 地址并实机连通

目标：解决设备已连上 WiFi 但屏幕一直显示 `reconnecting`，串口持续连接旧编译期地址 `192.168.1.10:8000` 的问题。

### 完成项

- [x] 串口确认 WiFi 已成功连接，设备 IP 为 `192.168.31.128`
- [x] 串口确认 `reconnecting` 根因不是 WiFi，而是 XiaoClaw WS 仍回退到编译期地址：`url_source=build`
- [x] 确认 OpenClaw V2.7 当前启用 `CONFIG_XIAOZHI_SKIP_OTA_VERSION_CHECK=y`，因此 OTA `websocket` 下发链路不会执行
- [x] 在 OpenClaw 本地配置页新增 XiaoClaw WebSocket 配置入口：`ws_url/ws_token/ws_device_id/ws_client_id`
- [x] 让 OpenClaw `:8080` 配置页在 STA 联网后也启动，支持访问 `http://<device-ip>:8080/` 直接写入运行时配置
- [x] 在 XiaoClaw WS 启动日志中加入 `url_source/token_source/device_id_source/client_id_source`，区分 runtime、build、empty
- [x] 通过设备本地配置页将运行时地址写入 `ws://192.168.31.53:8000/xiaozhi/v1/`
- [x] 实机串口验证通过：下一次重连打印 `url_source=runtime`，并成功 `ws connected`、`server hello received`、`reconnecting -> idle`

### 当前状态

当前 XiaoClaw WS 地址写入与读取链路已闭环。设备不再固定连接旧编译期 IP；如果运行时 NVS 中配置了当前局域网后端地址，固件会优先使用运行时地址。

本轮串口还出现：

```text
MODEL_LOADER: Can not find model in partition table
AfeWakeWord: Failed to initialize wakenet model
```

该日志发生在 WS 连通之后，属于本地唤醒模型资源分区/资产问题，不是当前 `reconnecting` 的根因。

### 下一步

继续做 BOOT 录音主链路复测，确认 WS 已连通后是否能正常上传 Opus、后端 ASR/LLM/TTS 是否恢复。后续单独处理本地唤醒模型资源分区问题。

---

## P7 修复（2026-05-26）：XiaoClaw WS 不再依赖写死 IP

目标：修复固件 XiaoClaw WS 客户端只读取编译期 `CONFIG_XIAOCLAW_*`，导致设备换网络环境后仍尝试连接旧后端 IP 的问题。

### 完成项

- [x] 只读定位根因：`main/xiaoclaw_ws_client.cc` 直接读取 `CONFIG_XIAOCLAW_WS_URL / DEVICE_TOKEN / DEVICE_ID / CLIENT_ID`
- [x] 确认 OTA 已经会把后端返回的 `websocket.url` / `websocket.token` 写入 `Settings("websocket")`
- [x] 确认当前后端默认 OTA 返回的 WebSocket 路径仍为 `/xiaozhi/v1/`，问题不在路径本身
- [x] 扩展 `openclaw_runtime_config_logic`：新增逐字段解析 XiaoClaw WS 配置的纯逻辑 helper
- [x] 新增/扩展主机侧最小测试，覆盖“运行时字段优先、缺失时回退到编译期默认值”的行为
- [x] `XiaoClawWsClient` 改为优先读取 `Settings("websocket")` 中的 `url/token/device_id/client_id`，仅在缺失时回退到 `CONFIG_XIAOCLAW_*`
- [x] `hello` 消息中的 `device_id/client_id` 同步切到同一份解析结果，避免 header 与 hello 体不一致
- [x] 主机侧验证通过：`cc -I main/providers tests/test_openclaw_runtime_config_logic.c main/providers/openclaw_runtime_config_logic.c -o /tmp/test_openclaw_runtime_config_logic && /tmp/test_openclaw_runtime_config_logic`

### 当前状态

固件代码侧已经收口：后续只要 OTA/运行时配置中下发了新的 WebSocket 地址，XiaoClaw WS 启动时就应优先使用该地址，而不是继续连接编译时写进 `sdkconfig` 的 `192.168.1.10:8000`。当前未完成的部分是实机复测，因为本会话环境缺少可直接运行的 ESP-IDF `idf.py`。

### 下一步

重新烧录/构建当前固件后上板观察串口，重点确认：

```text
XiaoClawWS: ws config url=<当前局域网实际地址>
```

如果地址已更新但仍连接失败，再继续沿“后端服务是否真的在跑 / 当前局域网是否可达 / 服务监听是否有效”三条线排查。

---

## P5 修补完成（2026-05-26）：配网保存后不退出配网模式

目标：修复用户在 `http://192.168.4.1` 配网页面填入 WiFi 信息并保存后，设备仍停留在配网模式的问题。

### 完成项

- [x] 复现实机问题：默认配网页面提交成功后，设备未自动退出配网模式
- [x] 定位根因 1：`main/boards/common/wifi_board.cc` 的 OpenClaw API 页面在 HTTP 请求线程里直接切 WiFi 状态，切换路径不稳定
- [x] 定位根因 2：默认 `192.168.4.1` 配网页面走 `managed_components/78__esp-wifi-connect/wifi_configuration_ap.cc` 的 `/submit`，保存成功后原本没有触发退出配网模式
- [x] 修复 OpenClaw API 页面：先回 HTTP 响应，再在主任务里触发 `StopConfigAp()`，交由既有 `ConfigModeExit -> TryWifiConnect()` 链路接管
- [x] 修复默认配网页面：`/submit` 成功后延迟 200ms 自动调用 `on_exit_requested_()`，确保返回成功后自动退出配网
- [x] 完成 `erase-flash -> flash -> monitor` 干净实机复测
- [x] 串口确认设备可进入配网模式，热点 `Xiaozhi-1279` 和 `http://192.168.4.1` 正常启动
- [x] 用户完成配网后，设备不再停留在 `wifi_configuring`，而是进入 XiaoClaw WebSocket 连接重试阶段

### 当前状态

配网保存后“看起来不生效”的直接问题已修复。当前设备已经能完成从配网模式退出并进入联网后的业务阶段。此后串口上的主要报错已切换为 XiaoClaw 后端不可达，不属于本次配网页面修补范围。

### 下一步

继续排查 XiaoClaw 后端地址、服务状态和鉴权配置，确认 `192.168.1.10:8000` 是否可达，以及本地 WS 服务是否正常监听。

---

## P5 验证完成（2026-05-26）：OpenClaw V2.7 白屏/按键异常实机关闭

目标：将 2026-05-25 的板型构建修复真正烧录到设备，并用串口日志确认白屏/按键无响应问题是否收口。

### 完成项

- [x] 使用当前 `build/xiaozhi.bin` 对 OpenClaw V2.7 实机重新烧录
- [x] 串口启动日志确认 `SKU=openclaw-v2.7`
- [x] 串口日志确认显示链路完成初始化：`LcdDisplay`、背光、`OpenClaw V2.7 board initialized`
- [x] 串口日志确认 `BOOT` 键事件恢复：出现 `DIAG_BOOT_PRESS`
- [x] 串口日志确认 `VOL+ / VOL-` 事件恢复：音量日志从 `60 -> 70 -> 60 -> 50`
- [x] 监控期间未出现 panic、异常重启或显示初始化报错

### 当前状态

本轮已完成“代码修复 -> 构建 -> 烧录 -> 串口验证”的闭环。原始故障“烧录后 TFT 白屏、按键无反应”在当前 OpenClaw V2.7 固件上已完成收口，P5 修复任务可以关闭。

### 下一步

切回 P7 联调验证，继续做连续对话和异常恢复，不再在 P5 板型问题上停留。

---

## P5 修复（2026-05-25）：OpenClaw V2.7 板型构建配置修复

目标：定位并修复烧录后 TFT 白屏、BOOT / 音量键无反应的问题。

### 完成项

- [x] 定位根因：当前 `sdkconfig` 实际选中 `CONFIG_BOARD_TYPE_BREAD_COMPACT_WIFI=y`，导致编译错误板级文件
- [x] 确认错误影响：Bread Compact 初始化 I2C OLED 和错误按键引脚；OpenClaw V2.7 需要 SPI ST7735 与 `VOL+=GPIO38`
- [x] 修复 `scripts/release.py`：追加 OpenClaw 板型前先清理旧 `CONFIG_BOARD_TYPE_*` choice，避免 CMake 先命中默认板型
- [x] 同步清理 `Flash Assets` choice，避免 `FLASH_NONE_ASSETS` 与默认资源选择冲突
- [x] 收口当前本地 `sdkconfig` 为 `CONFIG_BOARD_TYPE_OPENCLAW_V2_7=y`、16MB DIO flash、OpenClaw 分区表和 `FLASH_NONE_ASSETS`
- [x] 固件构建通过：`idf.py build` 输出 `Project build complete.`
- [x] 构建日志确认编译 `main/boards/openclaw-v2.7/openclaw_v2_7_board.cc.obj`
- [x] 构建日志确认 `Assets flashing disabled (FLASH_NONE_ASSETS)`，最终 flash 命令不再包含 `generated_assets.bin`
- [x] 已在 `开发问题解决记录.md` 新增问题 8，记录根因、修复与后续注意

### 当前状态

白屏/按键无响应的代码侧根因已修复。当前产物应使用 OpenClaw V2.7 板级初始化：

```text
TFT SPI: SCK=GPIO21 / MOSI=GPIO47 / RST=GPIO45 / DC=GPIO40 / CS=GPIO41 / BL=GPIO42
Buttons: BOOT=GPIO0 / VOL+=GPIO38 / VOL-=GPIO39
```

### 下一步

重新烧录当前构建产物并观察串口与实物：优先确认 TFT 不再白屏、BOOT 按下出现 `DIAG_BOOT_PRESS` 日志、VOL+/VOL- 能调整音量。通过后再回到 P7 连续 10 轮对话与异常恢复验证。

---

## P5/P7 联调完成（2026-05-25）：BOOT → ASR → LLM → TTS 主链路打通

目标：确认上一轮 oMLX ASR 配置修正后，ESP32 BOOT 录音是否能完整走通后端 ASR、LLM、TTS，并把音频下发回设备。

### 完成项

- [x] BOOT 手动录音继续成功触发后端 `[LISTEN] START`
- [x] ESP32 成功上传 WebSocket binary Opus 音频，共约 50 帧
- [x] 后端收到 `[LISTEN] STOP` 后成功进入非流式 ASR
- [x] ASR 成功返回识别文本：`你好，你是谁？`
- [x] LLM 成功收到用户文本并生成回复
- [x] TTS 成功完成三段语音合成并向 ESP32 下发全部音频
- [x] P5/P7 第一版端到端主链路达到“稳定听、稳定想、稳定说”的基本闭环

### 当前状态

当前已确认主链路为：

```text
BOOT -> listen start -> Opus upload -> ASR -> LLM -> TTS -> binary audio downlink
```

本轮说明前一阶段的 oMLX ASR 404 阻塞已经实机消除，后端不再停留在假 `ASR_EMPTY` 路径。当前主问题已从“主链路是否打通”切换为“设备端实际播放效果和异常恢复覆盖是否足够”。

### 下一步

进入 P7 异常恢复验证，优先只做一个方向：连续 10 轮对话与异常场景复测，重点覆盖 `ASR_EMPTY`、断线重连、TTS 失败回退和单轮 timeout 是否都能回到 `idle` 或 `reconnecting`。

---

## P5/P7 只读分析（2026-05-25）：确认 oMLX ASR 基础设施就绪，上次 404 阻塞已解决

目标：在不动代码的前提下，对 BOOT → Opus 上传 → 后端 ASR 返回的全链路做只读分析，确认当前阻塞状态。

### 分析结果：系统处于 A/B 类就绪态

**上一次阻塞（404，模型名 `omlx-asr` 占位符）已经解决。**

本次分析未改代码，以下为基础设施检查结果：

#### 1. oMLX 服务器状态（已确认）

| 检查项 | 结果 |
|--------|------|
| oMLX 进程 `python3` PID | `8107`，绑定 `127.0.0.1:8009` |
| 模型列表包含 Qwen3-ASR-1.7B-8bit | ✅ `http://127.0.0.1:8009/v1/models` 确认 |
| ASR 端点 `POST /v1/audio/transcriptions` | ✅ 返回 200，0.3s 内响应 |
| 从 Docker 内访问 host.docker.internal:8009 | ✅ 可达，返回模型列表 |
| ASR 端点对纯音频返回结果 | ✅ `{"text":"","language":null,...}` 正确返回空 |

#### 2. 后端配置（已确认）

| 检查项 | 结果 |
|--------|------|
| config.yaml OMLXASR.model_name | `Qwen3-ASR-1.7B-8bit` ✅ |
| config.yaml OMLXASR.base_url | `http://host.docker.internal:8009/v1/audio/transcriptions` ✅ |
| data/.config.yaml 覆盖 | 与 config.yaml 一致 ✅ |
| config_loader.py 环境变量覆盖 | 仅 `OMLX_BASE_URL` / `OMLX_ASR_MODEL` 设置时覆盖 ✅ |

#### 3. 后端 ASR 代码路径（已确认）

| 步骤 | 文件 | 状态 |
|------|------|------|
| Binary → `asr_audio_queue.put()` | `connection.py:413` | ✅ |
| Queue 消费者 → `handleAudioMessage` | `base.py:48-52` | ✅ |
| VAD 检查 → `receive_audio` | `receiveAudioHandle.py:30` | ✅ |
| Manual 模式 `asr_audio.append()` | `base.py:73` | ✅ |
| Listen stop → copy → `handle_voice_stop` | `listenMessageHandler.py:49-62` | ✅ |
| Opus 解码 → PCM → WAV 文件 | `base.py:96-105`、`299-347` | ✅ |
| HTTP POST → oMLX API | `openai.py:52-60` | ✅ |
| ASR_EMPTY → `fail_round` | `base.py:201-202` | ✅ |

#### 4. ESP32 代码路径（已确认）

| 步骤 | 文件 | 状态 |
|------|------|------|
| BOOT PressDown → listen start + upload | `openclaw_v2_7_board.cc:108-140` | ✅ |
| BOOT Release → 1.5s flush → listen stop | `openclaw_v2_7_board.cc:143-153` | ✅ |
| Recognizing 10s timeout | `application.cc:1052-1058` | ✅ |
| `stt` 消息接收日志 | `xiaoclaw_ws_client.cc:345-353` | ✅ |
| `state` 消息驱动状态机 | `xiaoclaw_ws_client.cc:333-343` | ✅ |

#### 5. 当前唯一限制

**无法在此会话中执行实机 BOOT 测试。** 所有代码分析和基础设施检查都表明系统已就绪。需要用户亲自按 BOOT 说话并观察后端日志。

预计正常路径：
1. `[LISTEN] START` → ESP32 上传 Opus
2. `[WS_BINARY] FIRST frame` → 后端接收帧
3. `[LISTEN] STOP` → `ASR triggering manually`
4. `语音识别耗时: Xs | status=200` → oMLX 返回文本
5. `识别文本: <TEXT>` 或 `ASR 空识别，返回 ASR_EMPTY`

### 当前状态

从基础设施层面确认，系统可归为 **A/B 类就绪态**：oMLX ASR 端点正常、模型已加载、后端链路完整、ESP32 链路完整。只缺一次实机 BOOT 验证来确认最终结果。

### 下一步

用户执行一次实机 BOOT 按说话测试。观察后端日志确认返回文本（A 类）或真实 ASR_EMPTY（B 类）。如果仍出现上游错误（C 类），再在 `openai.py` 中增加最小诊断日志。

目标：给 Claude Code、Codex/OpenCode 等代码代理提供稳定的项目护栏，降低跨会话代码漂移风险。

### 完成项

- [x] 新增 `CLAUDE.md`，汇总项目主线、架构边界、协议红线、状态机、timeout、语音唤醒路线、日志密钥约束、Skill 安全和开发工作流
- [x] 更新 `AGENTS.md`，要求新会话同时阅读 `START_HERE_项目速览.md` 与 `CLAUDE.md`
- [x] 新增 `opencode.json`，通过 `instructions` 显式加载 `CLAUDE.md`
- [x] 确认本次没有修改协议、状态机或架构决策本身，因此无需同步修改速览和架构方案
- [x] 确认本次没有解决新的开发问题，因此不更新 `开发问题解决记录.md`

### 当前状态

工具护栏已落到项目根目录。Claude Code 可使用 `CLAUDE.md` 项目记忆；OpenCode 在已有 `AGENTS.md` 的情况下也会通过 `opencode.json` 额外加载 `CLAUDE.md`。

### 下一步

继续 P5/P7 联调：BOOT 复测 ASR 是否加载 `Qwen3-ASR-1.7B-8bit` 并返回有效文本或真实 `ASR_EMPTY`。

---

## P5/P7 修复（2026-05-22）：oMLX ASR 模型名修正

目标：解释并修正 oMLX 控制台没有加载 ASR 模型的问题，只处理 ASR 请求配置。

### 完成项

- [x] 确认后端并非没有走到 ASR：日志已出现 `ASR triggering manually`，并由 `core.providers.asr.openai` 发起 ASR 请求
- [x] 确认容器内可访问 oMLX：`http://host.docker.internal:8009/v1/models` 返回模型列表，包含 `Qwen3-ASR-1.7B-8bit`
- [x] 确认 ASR endpoint 存在：`GET /v1/audio/transcriptions` 返回 `405 Method Not Allowed`，说明路由存在但需要 POST
- [x] 定位 404 高概率原因：后端实际加载的 `OMLXASR.model_name` 仍是占位值 `omlx-asr`
- [x] 已将 `config.yaml` 与 `data/.config.yaml` 中 `OMLXASR.model_name` 修正为 `Qwen3-ASR-1.7B-8bit`
- [x] 已重启后端容器并确认健康状态，容器内加载配置为 `OMLXASR base_url=http://host.docker.internal:8009/v1/audio/transcriptions`、`model_name=Qwen3-ASR-1.7B-8bit`

### 当前状态

后端 ASR provider 已配置到正确模型名。ESP32 已在容器重启后自动重连并继续发送 ping。尚未完成 BOOT 复测，因此还不能确认最终识别文本。

### 下一步

按 BOOT 重新说话，观察后端是否仍出现 `[LISTEN] START`、`[WS_BINARY] FIRST frame`、`[ASR_DIAG] manual stop captured frames`、`ASR triggering manually`，并确认 oMLX 控制台是否加载 `Qwen3-ASR-1.7B-8bit`，后端最终返回识别文本或真实 `ASR_EMPTY`。

---

## P5/P7 联调（2026-05-22）：BOOT 录音到后端 ASR 触发链路确认

目标：只验证 BOOT 按下说话 → Opus 上传 → 后端 ASR 给出明确结果，不改唤醒词、OTA、Skill、TTS 协议、Docker 数据库和 Redis。

### 完成项

- [x] 后端容器确认仍为 `xiaozhi-esp32-server`，主进程 `python app.py`，状态 `healthy`
- [x] ESP32 已成功连接后端并完成 `hello`，后端会话初始化 `need_bind=False`
- [x] BOOT 手动录音触发后端 `listen start`：出现 `[LISTEN] START`
- [x] Opus binary 上传到达后端：出现 `[WS_BINARY] FIRST frame`，首帧长度 150 bytes，`vad=OK`、`asr=OK`
- [x] 手动停止录音后端捕获音频帧：出现 `[ASR_DIAG] manual stop captured frames=26`，随后复测出现 `frames=85`
- [x] 非流式 ASR 被手动触发：出现 `ASR triggering manually: frames=26`，随后复测出现 `frames=85`
- [x] 本轮 ASR 已有明确结果：连续复测中 oMLX ASR 请求均返回 `API请求失败: 404`，随后后端按当前逻辑返回 `ASR_EMPTY`

### 当前状态

BOOT → Opus 上传 → 后端收帧 → 手动触发 ASR 的边界已确认打通。本轮没有新增诊断代码，也没有改主逻辑。当前阻塞点已从“是否触发 ASR”收敛为“oMLX ASR 请求返回 404，导致后端进入 ASR_EMPTY”。

### 下一步

只沿 ASR provider 配置与 oMLX 请求路径继续排查 404，目标是拿到有效识别文本；若确实为空音频，则保持 `ASR_EMPTY`，但需要区分上游 404 与真实空识别。`listening/uploading_audio` 状态抖动暂不处理，除非它直接阻塞 ASR 返回。

---

## 后端工作区收口（2026-05-22）：备份、清理、oMLX 配置恢复

目标：在继续 BOOT 录音链路和状态抖动前，先把后端本轮未提交改动收口，避免在大块重写上继续叠改。

### 完成项

- [x] 已备份当前后端工作区 tracked diff、diffstat、status 与未跟踪文件归档到 `工作区备份/20260522-155258/`
- [x] 已清理未跟踪的 `tests/`、`core/memory/` 和 `__pycache__` 生成物；相关内容已在备份归档中保留
- [x] 已搁置 TTS 基类大块重写、每帧状态回包改动、LLM timeout/retry 大块改写残留
- [x] 保留 Docker 主进程固化、healthcheck、`/health`、入站帧大小校验、`last_activity_time` 保活刷新、WS binary/ASR 诊断日志
- [x] 已移除 compose 中 `models/SenseVoiceSmall/model.pt` 挂载，避免 Docker 在宿主机自动创建空目录
- [x] 默认配置和本地 `data/.config.yaml` 已从 `FunASR/ChatGLMLLM/EdgeTTS` 切回 oMLX 本地服务：`OMLXASR/OMLXLLM/OMLXTTS`
- [x] 已重建并重启后端容器，当前 `xiaozhi-esp32-server` 为 `Up ... (healthy)`，日志显示 `llm成功 OMLXLLM`、`asr成功 OMLXASR`

### 当前状态

后端已不再因 FunASR `model.pt` 目录错误重启，Docker 主进程为 `python app.py`，healthcheck 正常。本轮只完成收口和配置恢复，未继续处理 BOOT ASR 返回和状态抖动。

### 下一步

继续唯一联调目标：BOOT 按下说话 → Opus 上传 → 后端 ASR 出现明确结果（文本 / `ASR_EMPTY` / 明确错误日志），随后再处理 `listening/uploading_audio` 状态抖动。

---

## 总体阶段看板

| 阶段 | 状态 | 说明 |
|------|------|------|
| P0：资料、仓库与现状对齐 | [x] 完成 | 2026-05-20 已新增路线图并同步速览、架构方案、进度文档 |
| P1：仓库基线与开发环境固定 | [x] 完成 | 后端 Git 仓库已恢复；ESP32 分支和改动已梳理 |
| P2：后端协议骨架与鉴权硬化 | [x] 完成 | Token、帧大小限制、JSON/binary 边界、smoke test |
| P3：后端稳定听、稳定想、稳定说 | [x] 已完成 | 2026-05-20 验收通过，真实 chat() 测试覆盖生产路径，36 测试全过 |
| P4：后端持久化、日志与运维 | [x] 已完成 | 2026-05-21 SQLite session 记忆、日志脱敏、/health endpoint、Docker healthcheck，最终 60 测试全过 |
| P5：ESP32 瘦客户端协议适配 | [~] 主问题验收通过 | WS 心跳保活、自动重连、BOOT 恢复已上板通过；剩余端到端语音返回继续联调 |
| P6：ESP32 旧架构清理与本地降级 | [x] 已完成 | 旧 `tts_response.audio` base64 播放入口已禁用；本地 provider、bridge/mimiclaw 不再是当前编译主链路依赖 |
| P7：端到端联调与异常恢复 | [x] 已完成 | BOOT 主链路、断线重连、空识别、超时、播放失败验证已收口 |
| P8：自定义 Skill 与 Agent 循环 | [x] 已完成 | P8-1 至 P8-8 已验收通过，含 pending 启用流程 |
| P9：ESP32 本地语音唤醒 | [x] 已完成 | 2026-06-02 多轮实机复测 10/10 通过，主链路稳定闭环成立 |

---

## P5 修复（2026-05-22）：XiaoClaw WS 心跳保活与自动重连

目标：修复后端空闲关闭 WebSocket 后 ESP32 卡在 `reconnecting`、BOOT 无响应的问题。

### 完成项

- [x] 后端启用 WebSocket ping/pong：`config.yaml` 与 `data/.config.yaml` 均设置 `enable_websocket_ping: true`
- [x] ESP32 `XiaoClawWsClient` 新增 `SendPingJson()`，并识别后端 `pong`
- [x] ESP32 主循环每 30 秒发送一次 `{"type":"ping"}`，保持后端 `last_activity_time` 刷新
- [x] ESP32 断线后进入 `reconnecting`，并按 2s 起步、最高 30s 的退避策略自动重连
- [x] 修复连接启动失败路径：`Start()` 失败后继续安排下一次重连，避免卡死在 `reconnecting`
- [x] 后端 `ConnectionHandler._route_message()` 在通过帧校验后统一刷新 `last_activity_time`，确保 ping/hello/listen/binary 都能证明连接存活
- [x] 固件构建通过：`idf.py build` 显示 `Project build complete.`
- [x] 上板验证通过：空闲超过原 180 秒断开窗口后仍持续收到 ping，未再出现 `连接超时，准备关闭`
- [x] 上板验证通过：后端服务中断期间 ESP32 进入 `reconnecting`，按 2s/4s/8s/16s/30s 退避持续重连，服务恢复后回到 `idle`
- [x] 上板验证通过：恢复到 `idle` 后 BOOT 可触发 `wakeup_detected → listening → uploading_audio`，并成功上传第一帧 Opus

### 当前状态

主问题已验收通过：设备不会再因 3 分钟左右后端空闲超时而长期卡在 `reconnecting`，即使服务端断开也能自动重连并恢复 BOOT 入口。

### 下一步

继续联调识别后链路：本次 BOOT 上传后后端未返回有效识别结果，固件按 10 秒 recognizing timeout 回到 `idle`；另有后端频繁回发 `listening/uploading_audio` 状态造成状态抖动日志，需要后续单独处理。

---

## P5 上板诊断（2026-05-22）：WS 空闲断开后 BOOT 无响应

目标：定位设备启动约 3-4 分钟后屏幕显示 reconnecting/reset 类状态且 BOOT 无响应的问题。

### 诊断结果

- [x] 已刷入最小诊断日志固件，仅增加 `DIAG_XC_WS_DISCONNECTED` 与 `DIAG_BOOT_PRESS` 日志，不改变运行行为
- [x] ESP32 串口确认：启动后约 187 秒出现 `ws disconnected`
- [x] ESP32 串口确认：断开时 `DIAG_XC_WS_DISCONNECTED uptime_ms=186628 state_before=idle`
- [x] ESP32 状态机确认：随后 `State: idle -> reconnecting`
- [x] BOOT 按键确认：`DIAG_BOOT_PRESS ... state=reconnecting` 后 `BOOT press ignored in state=reconnecting`
- [x] 后端日志确认：同一连接在约 3 分钟后出现 `连接超时，准备关闭`
- [x] 本轮未出现 `Guru Meditation`、`WDT`、`brownout`、`panic` 等硬崩溃证据

### 当前结论

根因已定位为：后端空闲超时关闭 WebSocket，ESP32 XiaoClaw 客户端进入 `reconnecting` 后没有自动恢复连接；OpenClaw BOOT 按键只允许 `idle` 状态触发，因此在 `reconnecting` 状态下被忽略。

### 下一步

修复 XiaoClaw WS 自动重连或保活策略，并确保断线恢复后状态回到可交互状态；修复后继续 P5 上板验证。

---

## P0：资料、仓库与现状对齐

- [x] 新增 `项目开发路线图_XiaoClawBrain.md`
- [x] 更新 `START_HERE_项目速览.md`，加入 P0-P9 当前执行路线
- [x] 更新架构方案实施里程碑，替换旧 MVP 口径
- [x] 重写本进度文档，按 P0-P9 路线记录后续进度
- [x] 确认 `开发问题解决记录.md` 本次不更新：本次没有解决新的开发问题

验收状态：已完成。

---

## P5.1：ESP32 瘦客户端协议骨架（补修后完成）

目标：让 ESP32 初步成为 XiaoClawBrain 后端的瘦客户端骨架（连接、鉴权、JSON、状态机、BOOT 入口），不做真实音频上传/播放。

### 改动文件

- `main/xiaoclaw_ws_client.cc`（新增）：独立 WebSocket 客户端，使用现有 `WebSocket` 类（来自 `78__esp-ml307`），携带 Authorization/device-id/client-id header，发送 hello/listen start/listen stop JSON
- `main/xiaoclaw_ws_client.h`（新增）：同上头文件
- `main/device_state.h`：扩展 `DeviceState` 枚举，新增 `kDeviceStateWakeupDetected`、`kDeviceStateUploadingAudio`、`kDeviceStateRecognizing`、`kDeviceStateSynthesizing`、`kDeviceStateReconnecting`
- `main/device_state_machine.cc`：扩展 `STATE_STRINGS` 数组，修复 `GetStateName()` bounds，新增 XiaoClaw 状态转换规则
- `main/application.h`：新增 `XiaoClawWsClient` 指针成员和 `InitializeXiaoClawWsClient()`、`XiaoClawSendListenStart()`、`XiaoClawSendListenStop()` 方法声明
- `main/application.cc`：实现 XiaoClaw WS 初始化、状态回调、BOOT 按键 listen start/stop 入口
- `main/boards/openclaw-v2.7/openclaw_v2_7_board.cc`：BOOT 按键改为 PressDown/Release 监听，分别发送 listen start/stop
- `main/Kconfig.projbuild`：新增 `XIAOCLAW_WS_URL`、`XIAOCLAW_DEVICE_TOKEN`、`XIAOCLAW_DEVICE_ID`、`XIAOCLAW_CLIENT_ID` 配置项
- `main/CMakeLists.txt`：新增 `xiaoclaw_ws_client.cc`
- `main/boards/common/lamp_controller.h`：修复 git conflict marker（与 P5.1 无关但阻塞构建）

### 已支持 JSON 消息类型

接收并处理：`state`、`stt`、`tts_start`、`tts_end`、`sentence_start`、`sentence_delta`、`sentence_end`、`error`、`hello`

### BOOT 按键交互

- BOOT PressDown：从 `idle` 进入 `wakeup_detected → listening`，发送 `{"type":"listen","state":"start","mode":"manual"}`
- BOOT Release：从 `listening` 进入 `recognizing`，发送 `{"type":"listen","state":"stop"}`
- 非 `idle` 状态忽略 BOOT

### TFT/显示状态

`Display::SetStatus(state_string)` 接入：收到后端 `state` JSON 时显示，BOOT 触发时显示 `listening`/`recognizing`，连接/断连时显示 `idle`/`reconnecting`

### 构建命令与结果

```bash
. /Users/mashiyue/esp/esp-idf-v5.5.2/export.sh && idf.py build
# Project build complete. xiaozhi.bin binary size 0x2b9170 bytes
```

静态搜索：无 `base64_opus` 新增、无完整 token 泄漏、Authorization header 构建时脱敏。

### P5.1 验收标准通过项

- [x] ESP32 编译通过
- [x] 能配置 `ws_url`、`device_token`、`device_id`、`client_id`（Kconfig）
- [x] WebSocket 连接携带 Authorization/device-id/client-id header
- [x] 连接成功后发送 `hello`
- [x] BOOT 能触发 `wakeup_detected → listening`
- [x] BOOT stop 能发送 `listen stop` 并进入 `recognizing`
- [x] 能解析 `state` / `error` / `stt` / `tts_start` / `tts_end`
- [x] TFT/显示系统能显示当前状态入口
- [x] 不上传真实音频，不播放 binary
- [x] 没有新增 base64 TTS
- [x] 没有 token / Authorization / API key / secret 泄漏
- [x] 不删除 P9 语音唤醒预留

### 下一步

进入 P5.3：Opus binary 接收播放（后端 TTS binary 帧到 ESP32 播放）。

---

## P5 补修（2026-05-21）：诊断日志 + flush timer + recognizing timeout + 状态机

目标：修复 P5 上板验收发现的根因问题（Opus 无实际上传、TTS binary 不播放、recognizing 死等）。

### 上板验收发现的问题

1. **BOOT 松开后没有 Opus 上传帧**：上传开关在 BOOT Release 时立即关闭，但 Opus 编码器缓冲还没 flush 完，后续才产出的帧被静默丢弃
2. **TTS binary 帧没有播放**：WS binary 回调链正确（`SetTtsBinaryCallback` 已在 408 行接 `OnTtsBinaryFrame`），但缺第一帧日志无法确认 binary 帧是否到达
3. **`idle → synthesizing` 被状态机拒绝**：后端可能在 idle 时主动发 TTS，状态机拒绝该迁移
4. **recognizing 无超时**：后端无响应时 recognizing 死等，没有超时回退
5. **WS binary 收包缺日志**：`HandleWsData` binary 分支无第一帧日志

### 改动文件

- `main/xiaoclaw_ws_client.cc`：
  - `HandleWsData` binary 分支加第一帧 + 每 25 帧日志（rate-limited）
  - `SendOpusFrame` 加第一帧日志（WS client 层，不跨层改状态）
  - 新增 `ResetTtsStats()`
- `main/xiaoclaw_ws_client.h`：新增 `tts_binary_frame_count_`/`tts_binary_bytes_` 成员 + `ResetTtsStats()` 声明
- `main/application.cc`：
  - `OnOpusFrameFromAudio` 加限流 drop 日志（bad_state / no_ws / upload_disabled）
  - `OnTtsBinaryFrame` 加第一帧日志 + 限流 bad_state drop 日志
  - `OnTtsStart` 回调加 `ResetTtsStats()`（每轮 TTS 重置计数）
  - 新增 `StartRecognizingTimeout()` / `CancelRecognizingTimeout()`
  - 状态机 listener：进 `recognizing` 启动 10s timer；进 `thinking/synthesizing/speaking/idle/error/reconnecting` 停止 timer
- `main/application.h`：新增 `xiaoclaw_recognizing_timeout_timer_` 成员 + `StartRecognizingTimeout()` / `CancelRecognizingTimeout()` 声明
- `main/boards/openclaw-v2.7/openclaw_v2_7_board.cc`：
  - 新增 `upload_flush_timer_` + `OnUploadFlushTimer` callback
  - BOOT Release 不再立即关闭上传，改为启动 1.5s flush timer
  - Timer callback 只 `Schedule()` 到 Application 主任务，由主任务做状态检查 + 关闭上传 + 发 listen stop
  - `OnPressDown` 先 `esp_timer_stop()` 取消上一次 flush timer，防止误伤新录音
- `main/device_state_machine.cc`：`kDeviceStateIdle` 允许迁移到 `kDeviceStateSynthesizing`

### BOOT 流程更新（补修后）

```
BOOT PressDown（idle 状态）
→ esp_timer_stop(upload_flush_timer_)  // 取消上一次 flush
→ SetXiaoClawBootListening(true)
→ WakeupDetected
→ Listening
→ SendListenStart JSON
→ XiaoClawStartAudioUpload()
  → ResetUploadStats()
  → SetUploadingEnabled(true)
  → EnableWakeWordDetection(false)
  → EnableVoiceProcessing(true)

BOOT Release（Listening/UploadingAudio 状态）
→ esp_timer_stop(upload_flush_timer_)
→ esp_timer_start_once(upload_flush_timer_, 1.5s)  // 不立即关闭
→ 日志："BOOT released, upload flush scheduled in 1.5s"

1.5s 后 OnUploadFlushTimer 触发（timer task）
→ Schedule() 到 Application 主任务
→ 主任务检查状态仍是 listening/uploading_audio
→ XiaoClawStopAudioUpload()
→ XiaoClawSendListenStop()
→ SetDeviceState(kDeviceStateRecognizing)
```

### Recognizing 超时流程

```
状态进入 kDeviceStateRecognizing
→ StartRecognizingTimeout() 启动 10s timer

状态进入 thinking/synthesizing/speaking/idle/error/reconnecting
→ CancelRecognizingTimeout() 停止 timer

10s 超时触发（timer task）
→ Schedule() 到 Application 主任务
→ 主任务检查状态仍为 recognizing
→ SetUploadingEnabled(false)
→ SetDeviceState(kDeviceStateIdle)
```

### 构建结果

```bash
idf.py build
# Project build complete. 干净构建，无 warning
```

### 下一步

P5 上板验证：按住 BOOT 2-4 秒，说一句话，期望看到：

```
FIRST opus frame uploaded len=...
opus cb OK count=1 len=... upload_en=1 state=listening
opus upload frames=25 bytes=...
P5.2 BOOT delayed listen stop completed
state=recognizing
FIRST tts binary frame len=...
FIRST tts binary frame OK len=... state=synthesizing
state=speaking
tts binary frames=25 bytes=...
state=idle
```

---

## P5.2：ESP32 Opus binary 上传（补修后完成）

目标：把 ESP32 的录音/编码输出接入 WebSocket binary 上传，BOOT 按下开始录音上传，松开停止。

### 补修记录（2026-05-21）

首次验收发现两个阻塞问题：

1. **`application.h` 类未闭合**：缺少 `};` 导致 `TaskPriorityReset` 被嵌套进 `Application`，所有包含该头文件的编译单元报错。
2. **BOOT 进入 listening 后关闭录音链路**：`HandleStateChangedEvent` 中 BOOT 路径执行 `EnableVoiceProcessing(false)` + `break`，导致 `on_opus_frame` 无法获得真实 PCM 输入。

### 改动文件

- `main/xiaoclaw_ws_client.h`：`SendListenStartJson()`/`SendListenStopJson()` 改为返回 `bool`
- `main/xiaoclaw_ws_client.cc`：同上方法实现返回 `bool`（成功取决于 `SendJson()`）
- `main/audio/audio_service.h`：`AudioServiceCallbacks` 新增 `on_opus_frame` callback
- `main/audio/audio_service.cc`：Opus 编码输出处调用 `callbacks_.on_opus_frame()`
- `main/application.h`：`XiaoClawSendListenStop()` 改为返回 `bool`；新增 `XiaoClawStartAudioUpload()`/`XiaoClawStopAudioUpload()`；修复 `class Application` 闭合 `};`
- `main/application.cc`：实现 `XiaoClawStartAudioUpload()`（启用 voice processing + 设置上传标志）/ `XiaoClawStopAudioUpload()`（停止 voice processing + 禁用上传）
- `main/boards/openclaw-v2.7/openclaw_v2_7_board.cc`：BOOT PressDown 使用 `XiaoClawStartAudioUpload()`，失败进 error；BOOT Release 使用 `XiaoClawStopAudioUpload()`

### 复用音频链路

- INMP441 / I2S 采集（现有）
- PCM16 16kHz mono 转换（现有 resampler）
- Opus encoder（现有 `esp_opus_enc_*`）
- 现有 `audio_encode_queue_` → `audio_send_queue_` 路径保持不变（作为备份）

### BOOT start/stop 流程（补修后）

```
BOOT PressDown（idle 状态）
→ SetXiaoClawBootListening(true)
→ WakeupDetected
→ Listening
→ SendListenStart JSON
→ XiaoClawStartAudioUpload()
  → ResetUploadStats()
  → SetUploadingEnabled(true)
  → EnableWakeWordDetection(false)
  → EnableVoiceProcessing(true)
  → on_opus_frame 开始稳定触发

录音期间（Listening/UploadingAudio）
→ on_opus_frame 回调 → SendOpusFrame → WS binary 发送
→ 状态保持 UploadingAudio

BOOT Release（Listening/UploadingAudio 状态）
→ XiaoClawStopAudioUpload()
  → SetUploadingEnabled(false)
  → EnableVoiceProcessing(false)
  → EnableWakeWordDetection(false)
→ SendListenStop JSON
→ Recognizing
→ SetXiaoClawBootListening(false)
```

### 上传 frame 统计（只记录长度/数量，不记录音频内容）

- `uploaded_frame_count_`：递增计数
- `uploaded_bytes_`：累计 bytes
- 每 25 帧打印日志：`opus upload frames=X bytes=Y last_len=Z`
- listen stop 时打印：`listen stop frames=X bytes=Y`

### 单个 binary frame ≤2048 bytes 限制

- `SendOpusFrame()` 入口检查 `len > 2048` → 拒绝并打印错误
- `OnOpusFrameFromAudio()` 入口检查 `len > 2048` → 拒绝

### 构建命令与结果

```bash
idf.py build
# Project build complete. xiaozhi.bin binary size 0x2d5ea0 bytes
# openclaw_v2_7_board.cc.obj confirmed compiled
```

后端基线：`60 passed`（无新增 `base64_opus` / forbidden pattern）

### P5.2 验收标准通过项

- [x] ESP32 编译通过（`idf.py build` 成功，binary 0x2d5ea0 bytes，目标板 OpenClaw V2.7）
- [x] BOOT start 发送 `listen start` JSON（`XiaoClawSendListenStart()`）
- [x] BOOT stop 发送 `listen stop` JSON（`XiaoClawSendListenStop()` 返回 bool）
- [x] `XiaoClawStartAudioUpload()` 真实启动 voice processing，触发 `on_opus_frame`
- [x] `XiaoClawStopAudioUpload()` 真实停止 voice processing
- [x] 单个 binary frame ≤2048 bytes
- [x] Opus frame 不进入 JSON，不做 base64
- [x] 状态机能进入 `uploading_audio`，stop 后进入 `recognizing`
- [x] `uploading_enabled_` 标志控制上传开关
- [x] token / Authorization / API key / secret 不泄漏
- [x] 不播放服务端 binary（留 P5.3）
- [x] 不大规模清理旧架构（留 P6）
- [ ] 上板验证（未上板，后端是否收到 binary frame 未验证）

### 下一步

进入 P6：ESP32 旧架构清理与本地降级（移除旧 base64 TTS、本地 provider、bridge/mimiclaw 主链路依赖）。

---

## P5.2 首次验收失败记录

**验收日期：2026-05-21**

**阻塞问题 1**：`application.h` 类未闭合，`TaskPriorityReset` 被错误嵌套到 `Application` 内，导致所有包含头文件的编译单元报错：

```
expected '}' at end of input
main/application.h:44:19: note: to match this '{'
```

**阻塞问题 2**：BOOT PressDown 路径在 `HandleStateChangedEvent` 中执行 `EnableVoiceProcessing(false)` 后直接 `break`，录音处理器不输出 PCM，`on_opus_frame` 无真实数据源。

**补修措施**：见上方「补修记录」。

---

## P5.2 二次补修（2026-05-21 下午）

首次补修后仍不能通过，原因：

### 阻塞问题 1：OpenClaw 板型未进入 Kconfig，默认编译 bread-compact-wifi

`sdkconfig` 实际是 `CONFIG_BOARD_TYPE_BREAD_COMPACT_WIFI=y`，Kconfig 中没有 `BOARD_TYPE_OPENCLAW_V2_7` 选项，导致 `sdkconfig.defaults.esp32s3` 的设置无效。

**修复**：
- 在 `main/Kconfig.projbuild` 的 board choice 中添加 `BOARD_TYPE_OPENCLAW_V2_7`（ESP32S3 only）
- 手动修改 `sdkconfig`：`CONFIG_BOARD_TYPE_OPENCLAW_V2_7=y`，`# CONFIG_BOARD_TYPE_BREAD_COMPACT_WIFI is not set`
- `main/CMakeLists.txt` 已有 `elseif(CONFIG_BOARD_TYPE_OPENCLAW_V2_7)` 分支，无需重复

### 阻塞问题 2：`XiaoClawStartAudioUpload/StopAudioUpload` 在 private 区域，板级代码无法调用

**修复**：移到 `public` 区域，与 `XiaoClawSendListenStart/Stop` 并列。

### 阻塞问题 3：BOOT 路径仍被状态机关闭录音链路

`HandleStateChangedEvent` 中 BOOT 分支仍执行 `EnableVoiceProcessing(false)` + `break`。

**修复**：改为只关闭 wake word detection，不关闭 voice processing；如 audio processor 未运行则先启用：

```cpp
if (xiaoclaw_boot_listening_) {
    audio_service_.EnableWakeWordDetection(false);
    if (!audio_service_.IsAudioProcessorRunning()) {
        audio_service_.EnableVoiceProcessing(true);
    }
    break;
}
```

### 阻塞问题 4：`XiaoClawSendListenStart()` 声明为 `void`，但 BOOT 代码需要 bool 返回

**修复**：`XiaoClawSendListenStart()` 改为返回 `bool`，失败时进入 error 状态。

### 其他 sdkconfig 缺失配置

- `CONFIG_LV_FONT_FMT_TXT_LARGE=y`（字体文件过大需要）
- `CONFIG_COMPILER_CXX_EXCEPTIONS=y` / `CONFIG_COMPILER_CXX_RTTI=y`（dynamic_cast 需要）
- `CONFIG_PARTITION_TABLE_CUSTOM=y` + `openclaw_v2_7_16m.csv`（16MB 16MB flash）
- `CONFIG_ESPTOOLPY_FLASHSIZE_16MB=y`

### 改动文件（补修 2）

- `main/Kconfig.projbuild`：新增 `BOARD_TYPE_OPENCLAW_V2_7` 选项
- `main/application.h`：`XiaoClawSendListenStart()` 改为 `bool`；`XiaoClawStartAudioUpload()`/`XiaoClawStopAudioUpload()` 移到 `public`
- `main/application.cc`：`XiaoClawSendListenStart()` 返回 `bool`；`HandleStateChangedEvent` BOOT 分支不再关闭 voice processing
- `main/boards/openclaw-v2.7/openclaw_v2_7_board.cc`：BOOT PressDown 检查 `XiaoClawSendListenStart()` 返回值，失败进 error
- `sdkconfig`：手动设为 OpenClaw 板、16MB flash、启用 exceptions/rtti/large font、custom partition table

### 构建验证

```bash
idf.py build
# [2312/2321] Building CXX ... boards/openclaw-v2.7/openclaw_v2_7_board.cc.obj
# Generated xiaozhi.bin
# xiaozhi.bin binary size 0x2d5ea0 bytes. Smallest app partition is 0x400000 bytes. 0x12a160 bytes (29%) free.
# Project build complete.
```

`openclaw_v2_7_board.cc.obj` 确认编译，构建完成。

### P5.2 二次补修验收通过项

- [x] `idf.py build` 成功（binary 0x2d5ea0 bytes）
- [x] 编译目标为 OpenClaw V2.7（`openclaw_v2_7_board.cc.obj`）
- [x] `XiaoClawStartAudioUpload()` 在 `public`，板级代码可调用
- [x] BOOT 路径不再关闭 voice processing
- [x] `XiaoClawSendListenStart()` 返回 `bool`，失败进 error
- [x] 后端 60 passed
- [x] 静态红线无 forbidden pattern
- [ ] 上板验证（未上板，后端是否收到 binary frame 未验证）

---

## P5.3：ESP32 Opus binary 接收播放（代码完成，硬件验收待完成）

目标：后端 TTS 音频通过 WebSocket binary 下发，ESP32 接收并通过 MAX98357A 播放。

### 补修记录（2026-05-21）

修复问题：

1. **`HandleJsonMessage` 未解析真实 TTS 协议**：后端发送 `{"type":"tts","state":"start/stop/sentence_*"}`，ESP32 原来只识别 `tts_start`/`tts_end`，新增 `type=="tts"` 分支解析 `state` 字段。
2. **`PlayTtsBinaryFrame` 硬编码参数**：显式化 `sample_rate=24000`、`frame_duration_ms=60` 参数。
3. **`on_tts_binary_frame` callback 绕路**：从 `AudioServiceCallbacks` 中删除，保持单向链路 WS binary → Application → AudioService → decode queue。
4. **文档状态错误**：修正为"代码完成，硬件验收待完成"，协议描述改为真实后端协议。

### 改动文件

- `main/xiaoclaw_ws_client.h`：新增 `TtsBinaryCallback` + `SetTtsBinaryCallback()`
- `main/xiaoclaw_ws_client.cc`：`HandleJsonMessage` 新增 `type=="tts"` 分支解析 `state`；binary 接收触发 `tts_binary_callback_`
- `main/audio/audio_service.h`：`PlayTtsBinaryFrame()` 参数显式化为 `sample_rate`/`frame_duration_ms`
- `main/audio/audio_service.cc`：`PlayOpusData`/`PlayTtsBinaryFrame` 显式参数；删除 `on_tts_binary_frame` callback 绕路
- `main/application.h`：新增 `OnTtsBinaryFrame()`
- `main/application.cc`：注册 `SetTtsBinaryCallback`；实现 `OnTtsBinaryFrame()`（`PlayTtsBinaryFrame(data, len, 24000, 60)`）

### 播放链路

```
后端 TTS WS binary 帧
  → XiaoClawWsClient::HandleWsData(binary=true)
  → tts_binary_callback_(data, len)
  → Application::OnTtsBinaryFrame(data, len)
  → audio_service_.PlayTtsBinaryFrame(data, len, 24000, 60)
  → AudioService::PushPacketToDecodeQueue(packet)
  → OpusCodecTask() 解码
  → audio_playback_queue_
  → AudioOutputTask() → MAX98357A 播放
```

### 协议联动

- 收到 `{"type":"tts","state":"start"}` → `tts_start_()` → `Synthesizing`
- 收到 WS binary frame（`Synthesizing`/`Speaking`）→ 第一帧 → `Speaking`
- 收到 `{"type":"tts","state":"stop"}` → `tts_end_()` → `Idle`

### 构建验证

```bash
idf.py build
# Project build complete. openclaw_v2_7_board.cc.obj confirmed compiled
```

后端基线：60 passed

### P5.3 验收标准通过项

- [x] ESP32 编译通过（`idf.py build` 成功）
- [x] WS binary 接收 callback 已注册到 `XiaoClawWsClient`
- [x] 支持后端真实 `type=tts,state=start/stop/sentence_*` 协议
- [x] `PlayTtsBinaryFrame()` 参数显式化 24000Hz / 60ms / mono Opus
- [x] `OnTtsBinaryFrame()` 在 `Synthesizing`/`Speaking` 状态才处理
- [x] 不做 base64 编码的 TTS
- [x] token / Authorization / API key / secret 不泄漏
- [ ] 上板验证：未完成，TTS binary 实际播放未验证

### 下一步

P5.3 代码链路已完成，硬件（上板）验收待完成。建议先进入 P6 旧架构清理，P6 完成后或有条件时再上板验证 P5.3。

进入 P6：ESP32 旧架构清理与本地降级（移除旧 base64 TTS、本地 provider、bridge/mimiclaw 主链路依赖）。

---

## P5.1 补丁补修（2026-05-21）

### P5.1 首次验收发现的问题

1. 状态机 `Idle → WakeupDetected`、`Idle → WakeupDetected → Listening` 被拒绝，BOOT 无法完成状态流
2. `kDeviceStateListening` 处理中执行 `protocol_->SendStartListening()` 和 `EnableVoiceProcessing(true)`，会触发旧音频上传链路
3. `error` JSON 只记录日志，未驱动状态机和 TFT 显示
4. WS 连接失败时静默 `reset()`，无可见状态

### 补丁完成项

#### 1. 状态机转换补全

`device_state_machine.cc`：
- `Idle` 新增允许 → `WakeupDetected`、`Reconnecting`
- `Connecting` 新增允许 → `WakeupDetected`
- `Listening` 改为允许 → `UploadingAudio`、`Recognizing`、`Speaking`、`Thinking`、`Idle`
- `WakeupDetected` 改为允许 → `Idle`
- `UploadingAudio` 新增允许 → `Thinking`、`Synthesizing`
- `Recognizing` 新增允许 → `Synthesizing`
- `Thinking` 新增允许 → `Synthesizing`
- `Synthesizing` 新增允许 → `Idle`

#### 2. BOOT 路径隔离旧音频链路

`application.h`：新增 `SetXiaoClawBootListening(bool)` setter
`openclaw_v2.7_board.cc`：
- PressDown：设置 `xiaoclaw_boot_listening_ = true` 后再 `SetDeviceState` / `SendListenStart`
- Release：清 `xiaoclaw_boot_listening_ = false` 在 `SendListenStop` 之后

`HandleStateChangedEvent` 中 `kDeviceStateListening` case：
```cpp
if (xiaoclaw_boot_listening_) {
    audio_service_.EnableVoiceProcessing(false);
    audio_service_.EnableWakeWordDetection(false);
    break;
}
```
P5.1 BOOT 路径只更新 TFT/状态和发送 JSON，不启动真实音频处理。

#### 3. error JSON 驱动错误状态

`xiaoclaw_ws_client.h`：新增 `OnError(std::function<void()>)` 回调
`xiaoclaw_ws_client.cc`：`HandleErrorMessage()` 末尾调用 `on_error_()`
`application.cc`：`OnError` 注册 → `display->SetStatus("error")` + `SetDeviceState(kDeviceStateError)`

#### 4. 连接失败可见状态

`application.cc`：`InitializeXiaoClawWsClient()` 连接失败时设置 `display->SetStatus("reconnecting")` + `SetDeviceState(kDeviceStateReconnecting)` 后再 `reset()`

#### 5. error 状态独立化

`device_state.h`：新增 `kDeviceStateError`，不再用 `kDeviceStateFatalError` 承接协议 `error`
`device_state_machine.cc`：新增 `"error"` 状态字符串，允许非 fatal 状态进入 `error`，并允许 `error → idle/reconnecting/fatal_error`
`application.cc`：协议 `state=error` 和 `error` JSON 都切到 `kDeviceStateError`；TFT 显示 `"error"` 并关闭语音处理入口

#### 6. ESP32 分支对齐

ESP32 工作副本已从本地 `main` 切到项目目标分支 `xiaoclaw-ghproxycom`，未提交改动原样保留。

### 验证结果

```bash
idf.py build  # Project build complete. xiaozhi.bin binary size 0x2b9170 bytes
rg -n '"type":\s*"tts".*"audio"|base64_opus|Authorization: Bearer [A-Za-z0-9._~+/=-]{12,}...'
# No secrets found
```

---

## P4：后端持久化、日志与运维（已完成）

#### 验收失败原因

第一次 P4 验收时发现以下问题：
1. `sanitize_text("Authorization: Bearer abcdef1234567890")` 输出 `Authorization=SET abcdef1234567890`，token `abcdef1234567890` 仍然泄漏
2. SQLite session memory 只做字面截断，不脱敏敏感词
3. 日志轮转仍用 `tmp/server.log` + `10 MB`，未改为 `data/logs/` + 每日 + 14 天
4. ASR/用户文本日志直接打印完整内容，未用 `summarize_transcript()`
5. OTA handler 直接打印完整 headers 和请求体数据
6. TTS/ASR provider 的 response.text 完整记录到日志

#### P4 补修完成项

##### 1. `safe_logging.py` Bearer 泄漏修复

- 重写 `sanitize_text()`：对 `Authorization: Bearer <token>` 格式做特殊处理，用 `BEARER_PATTERN` 匹配后调用 `mask_secret()`
- `sanitize_authorization(value)`：专用函数处理 Authorization 头中的 Bearer token
- `sanitize_mapping(data)`：对 `Authorization` 字段专用 `sanitize_authorization()` 处理
- 验证：`sanitize_text("Authorization: Bearer abcdef1234567890")` → `Authorization: Bearer abc***890` ✅

##### 2. SQLite session memory 脱敏

- `session_memory_store.py` 的 `append_message()` 改用 `sanitize_text(str(content), max_len=2000)` 代替简单截断
- 新增测试 `test_session_memory_store_masks_secrets`：验证 token/api_key 被脱敏

##### 3. 日志轮转改为每日 + 14 天

- `config.yaml`：`log_dir: data/logs`，`log_file: "xiaoclawbrain.log"`
- `config/logger.py`：`rotation="00:00"`（每日），`retention="14 days"`，`diagnose=False`（避免异常诊断把局部变量里的密钥打进日志）

##### 4. ASR/用户文本日志脱敏

- `core/connection.py`：`f"大模型收到用户消息: {summarize_transcript(query)}"` 代替直接打印 query
- `core/providers/asr/base.py`：所有 `logger.bind(tag=TAG).info(f"识别文本: ...")` 改用 `summarize_transcript()`
- `core/utils/safe_logging` 通过 `summarize_transcript()` 统一处理

##### 5. OTA headers/请求体日志脱敏

- `core/api/ota_handler.py`：用 `sanitize_mapping(dict(request.headers))` 脱敏后打印，只记录方法、headers 脱敏结果、数据长度，不打印完整 data

##### 6. TTS/ASR provider response.text 日志脱敏

- `core/providers/asr/openai.py`：只记录 `status_code` 和 `response_len`，不打印 `response.text`
- `core/providers/tts/minimax_httpstream.py`、`index_stream.py`、`openai.py`：同样改为只记录 status_code 和 response_len

##### 7. Health handler 重构

- 新增 `core/health.py`：`health_payload()` 函数返回 dict，`health_handler()` async 函数调用它
- `core/http_server.py` 改为 `from core.health import health_handler`（删除本地的重复定义）
- 测试改为调用真实的 `health_payload()` 函数

##### 8. 嵌入式 Authorization Bearer 脱敏最终补修

- 第二轮验收发现 `Authorization: Bearer <token>` 出现在普通文本中间时仍可能泄漏完整 token
- `core/utils/safe_logging.py` 新增全局 `AUTH_BEARER_FIELD_PATTERN` 和 `AUTH_FIELD_PATTERN`
- `SECRET_PATTERN` 移除 `authorization`，避免把 `Authorization: Bearer ...` 二次误判为普通字段
- 新增测试 `test_sanitize_text_masks_embedded_authorization_bearer_token`
- 验证：`sanitize_text("headers Authorization: Bearer abcdef1234567890 done")` → `headers Authorization: Bearer abc***890 done` ✅

### P4 第二次补修验收命令

```bash
cd xiaozhi-esp32-server

# 60 测试全过
python3 -m pytest tests/test_connection_protocol.py tests/test_p3_protocol.py tests/test_p4_memory_logging_health.py -v

# 编译检查
PYTHONPYCACHEPREFIX=/private/tmp/xiaoclaw_pycache python3 -m py_compile main/xiaozhi-server/core/connection.py
PYTHONPYCACHEPREFIX=/private/tmp/xiaoclaw_pycache python3 -m py_compile main/xiaozhi-server/core/memory/session_memory_store.py
PYTHONPYCACHEPREFIX=/private/tmp/xiaoclaw_pycache python3 -m py_compile main/xiaozhi-server/core/utils/safe_logging.py

# Bearer token 脱敏验证
python3 -c "import sys; sys.path.insert(0, 'main/xiaozhi-server'); from core.utils.safe_logging import sanitize_text; print(sanitize_text('Authorization: Bearer abcdef1234567890'))"
# 输出必须包含 abc***890，且不得包含 abcdef1234567890

# 嵌入式 Bearer token 脱敏验证
python3 -c "import sys; sys.path.insert(0, 'main/xiaozhi-server'); from core.utils.safe_logging import sanitize_text; print(sanitize_text('headers Authorization: Bearer abcdef1234567890 done'))"
# 输出必须包含 headers Authorization: Bearer abc***890 done，且不得包含 abcdef1234567890

# 检查无 base64 TTS 残留
rg -n '"type":\s*"tts".*"audio"|base64_opus' main/xiaozhi-server/core
# 无输出

# 检查无长 token 硬编码泄漏（config.yaml 注释除外）
rg -n 'Authorization: Bearer|api_key=.*[A-Za-z0-9]{12,}|secret=.*[A-Za-z0-9]{12,}|token=.*[A-Za-z0-9]{12,}' main/xiaozhi-server | rg -v "config.yaml|api_key.*你"
# 预期：只显示 auth.py 中的示例注释，无实际 token
```

验收状态：已完成。60 测试全过（B2 修复 Bearer 泄漏 + SQLite 脱敏 + health handler 重构 + 嵌入式 Authorization Bearer 脱敏回归），下一步进入 P5。

#### A. 日志脱敏工具 `core/utils/safe_logging.py`

- `mask_secret(value)`：短 token（≤8 字符）返回 `SET`，长 token 显示首尾各 3 字符
- `sanitize_text(text)`：正则脱敏 Authorization / api_key / secret / token / password，Bearer 前缀单独替换
- `sanitize_mapping(data)`：递归脱敏 dict 中的敏感字段
- `summarize_transcript(text)`：ASR 文本默认截断 50 字，环境变量 `XIAOCLAW_DEBUG_FULL_TRANSCRIPT=true` 允许 1000 字
- `is_debug_full_transcript_enabled()`：通过环境变量开启完整 ASR 日志，默认关闭

#### B. SQLite session 级记忆 `core/memory/session_memory_store.py`

- 使用 sqlite3 标准库，`data/xiaoclawbrain.sqlite3` 默认路径
- `append_message(session_id, role, content)`：写入消息前先脱敏，再按 2000 字限制截断
- `get_recent_messages(session_id, limit)`：读取最近 N 条（默认 10 条）
- `prune_session(session_id, keep_last)`：每轮结束保留最近 50 条

#### C. ConnectionHandler 集成

- `ConnectionHandler.__init__` 初始化 `self.session_memory_store`
- `chat(depth=0)` 用户消息进入时 `append_message(..., "user", query)`
- LLM 流式输出结束（direct_answer 和 tool_calls 分支）`append_message(..., "assistant", streamed_text)`
- 每轮结束后 `prune_session(self.session_id, keep_last=50)`

#### D. `/health` endpoint

- 新增 `core/health.py`，提供 `health_payload()` 和 `health_handler()`
- `core/http_server.py` 导入并挂载 `health_handler()`
- `GET /health` 返回 `{"status": "ok", "service": "xiaoclawbrain", "time": <unix_timestamp>}`
- 挂载在 `SimpleHttpServer` 的 aiohttp app 上，与 OTA 和 vision handler 同一端口（8003）

#### E. Docker healthcheck

- `docker-compose.yml`：新增 `healthcheck` 配置，检查 `http://127.0.0.1:8003/health`，30s 间隔，5s timeout，3 次重试，20s 启动延迟
- `Dockerfile-server`：新增 `HEALTHCHECK` 指令，同一端口检查

#### F. 测试覆盖

- `tests/test_p4_memory_logging_health.py`：24 个测试覆盖 safe_logging、SessionMemoryStore、health endpoint、Docker 配置
- P3 真实路径测试（`TestChatRealSuccessPath`）已更新，加入 `conn.session_memory_store = MagicMock()` mock
- 所有 60 个测试（14 P2 + 22 P3 + 24 P4）全部通过

### 验收命令

```bash
cd xiaozhi-esp32-server

# 60 测试全过
python3 -m pytest tests/test_connection_protocol.py tests/test_p3_protocol.py tests/test_p4_memory_logging_health.py -v

# 编译检查
python3 -m py_compile main/xiaozhi-server/core/connection.py
python3 -m py_compile main/xiaozhi-server/core/memory/session_memory_store.py

# 日志脱敏验证
python3 -c "import sys; sys.path.insert(0, 'main/xiaozhi-server'); from core.utils.safe_logging import summarize_transcript; print(summarize_transcript('x'*80))"
# 输出：'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...'

# 检查无 base64 TTS 残留
rg -n '"type":\s*"tts".*"audio"|base64_opus' main/xiaozhi-server/core
# 无输出

# 启动服务后验证 healthcheck（需真实服务）
# curl -fsS http://127.0.0.1:8003/health
```

验收状态：已完成。60 测试全过，下一步进入 P5。

---

## P3：后端稳定听、稳定想、稳定说（已完成）

目标：后端主链路满足第一版"能对话且异常可恢复"的基本产品要求。最终验收确认：finally executor.shutdown(wait=False) 在正常成功路径也执行、真实 `ConnectionHandler.chat()` 测试通过、LLM 成功路径只调用一次、36 测试全过。

### P3 第三轮修复（2026-05-20）

#### A. `connection.py` LLM 重构 - 修复 NameError 和 ThreadPoolExecutor

- 将 `_llm_stream_once()` 改为 `_consume_llm_stream_once()`，返回 dict 而非依赖闭包变量泄漏
- `with` 改为手动 `executor.shutdown(wait=False, cancel_futures=True)`，确保 timeout 后不卡死
- 新增 `sentence_id` 作废机制 `_invalidate_sentence_id()`，防止 timeout 后旧线程输出污染当前轮
- 第一次 TimeoutError 重试 1 次，第二次 TimeoutError → `LLM_TIMEOUT`
- 普通异常 → `LLM_ERROR`

#### B. `providers/tts/base.py` TTS 错误处理重构

- `_run_tts_coro_with_timeout()` 改为返回 `("ok", value) | ("timeout", None) | ("error", exc)` tuple
- 新增 `_fail_round_from_tts(code, message)` helper
- 新增 `_handle_tts_result(status, value, original_text)` 统一处理，timeout → `TTS_TIMEOUT`，error/空 → `TTS_ERROR`
- `to_tts_stream()` 和 `to_tts()` 两个分支全部统一使用 `_handle_tts_result()`
- 删除所有 `max_repeat_time` 重试循环，tts_timeout 默认 8s

#### C. 状态机时机修正

- `providers/asr/base.py`：`handle_voice_stop()` 在调用 `speech_to_text_wrapper` 前发送 `SessionState.RECOGNIZING`
- `providers/tts/base.py`：`to_tts_stream()` 和 `to_tts()` 在调用 `_run_tts_coro_with_timeout` 前发送 `SessionState.SYNTHESIZING`

#### D. 新增真实测试

- `TestChatLLMSuccessPath`：验证 LLM result dict 结构（response_message、tool_call_flag 等）
- `TestLLMTimeoutNoWait`：验证 `shutdown(wait=False)` 后不卡住
- `TestTTSTimeoutSingleError`：验证 TTS timeout 只发 TTS_TIMEOUT
- `TestTTSErrorSingleError`：验证 TTS 异常只发 TTS_ERROR
- `TestASRRecognizingState`：验证 RECOGNIZING 在 ASR 调用前发送
- `TestChatRealSuccessPath`：真实导入并调用 `ConnectionHandler.chat()`，验证正常成功路径不报错、LLM 只调用一次、TTS 队列 sentence_id 正确

### P3 执行结果

#### 1. 新增 `core/protocol.py` 错误码和状态枚举

- 文件：`main/xiaozhi-server/core/protocol.py`
- 新增 `ErrorCode` 枚举：ASR_EMPTY、ASR_TIMEOUT、LLM_TIMEOUT、LLM_ERROR、TTS_TIMEOUT、TTS_ERROR、ROUND_TIMEOUT、JSON_TOO_LARGE、BINARY_FRAME_TOO_LARGE
- 新增 `SessionState` 枚举：idle、wakeup_detected、listening、uploading_audio、recognizing、thinking、synthesizing、speaking、error
- 新增 `make_state_message(state, session_id=None)` 函数
- 扩展 `make_protocol_error(code, message, session_id=None)` 支持可选 session_id

#### 2. 更新 `connection.py` 加入 P3 timeout 和状态辅助方法

- 新增 timeout 属性：`asr_timeout_seconds=5`、`llm_timeout_seconds=20`、`tts_timeout_seconds=8`、`round_timeout_seconds=35`（单位：秒）
- 新增 `send_state(state)`：发送状态消息到 WebSocket
- 新增 `send_error(code, message)`：发送错误消息到 WebSocket
- 新增 `fail_round(code, message)`：发送 ERROR 状态 + 错误消息，然后回到 IDLE
- 新增 `is_round_timed_out()`：检查本轮是否超过 35s
- 新增 `check_round_timeout()`：超时则调用 fail_round

#### 3. ASR timeout 和 ASR_EMPTY 处理

- `core/providers/asr/base.py` 的 `handle_voice_stop()` 加入 `asyncio.wait_for(asr_task, timeout=conn.asr_timeout_seconds)`
- 超时触发 `conn.fail_round(ErrorCode.ASR_TIMEOUT, "语音识别超时")`
- 空识别（text_len == 0）触发 `conn.fail_round(ErrorCode.ASR_EMPTY, "我没听清，你再说一遍")`，不进入 LLM

#### 4. LLM timeout 和重试

- `connection.py` 的 `chat()` 方法在 depth==0 时记录 `round_started_at = time.monotonic()`
- LLM 调用包在 `for attempt in range(2)` 重试循环中
- 第一次 TimeoutError 重试，第二次 TimeoutError 调用 `fail_round(ErrorCode.LLM_TIMEOUT, "大模型响应超时")`
- 其他异常调用 `fail_round(ErrorCode.LLM_ERROR, ...)`
- 每次迭代前发送 `SessionState.THINKING` 状态

#### 5. 状态发送

- `receiveAudioHandle.py`：`handleAudioMessage()` 发送 `SessionState.LISTENING`
- `connection.py` depth==0：`chat()` 开始时发送 `SessionState.RECOGNIZING`
- `sendAudioHandle.py`：`sendAudioMessage()` FIRST 句子开始时发送 `SYNTHESIZING`（不是 SPEAKING；SPEAKING 由客户端自行判断，无需服务端发送）
- LLM 循环：`SessionState.THINKING`（见上面第 4 条）

#### 6. 单轮总 timeout 35s

- `chat()` 开始时记录 `round_started_at`
- LLM 流式循环中每次迭代检查 `is_round_timed_out()`，超则调用 `fail_round(ErrorCode.ROUND_TIMEOUT, "本轮对话超时")`

#### 7. 新增 P3 测试

- 文件：`tests/test_p3_protocol.py`
- 36 个测试用例（P2 14 + P3 22），覆盖 ErrorCode、SessionState、make_protocol_error、make_state_message、timeout 配置、无 base64 TTS、ASR_EMPTY no-LLM、LLM timeout 1x retry、TTS timeout no retry、TTS provider 无重试循环、LLM result dict、shutdown(wait=False)、TTS timeout 仅 TTS_TIMEOUT、TTS 异常仅 TTS_ERROR、ASR RECOGNIZING 时机、真实 `ConnectionHandler.chat()` 成功路径

### P3 验收结果（第四次通过）

- [x] pytest 36 passed（14 P2 + 22 P3）
- [x] `config.config_loader` 导入正常
- [x] 无旧 TTS base64 下发路径
- [x] ErrorCode、SessionState 枚举完整
- [x] make_protocol_error 支持 session_id 参数
- [x] timeout 默认值正确（5/20/8/35 秒）
- [x] ASR_EMPTY 不进入 LLM，直接 fail_round
- [x] LLM timeout 可重试 1 次
- [x] TTS timeout 无重试循环
- [x] TTS provider base.py 无 max_repeat_time 重试循环
- [x] LLM result dict 结构正确，无 NameError
- [x] ThreadPoolExecutor timeout 后 shutdown(wait=False) 不卡死
- [x] TTS timeout 只发 TTS_TIMEOUT，TTS error 只发 TTS_ERROR
- [x] RECOGNIZING 在 ASR 调用前发送，SYNTHESIZING 在 TTS 调用前发送
- [x] LLM 正常成功路径 finally 块执行 executor.shutdown(wait=False)
- [x] 真实 `ConnectionHandler.chat()` 成功路径测试通过，LLM 正常成功路径只调用 1 次

### P3 下一步入口

P3 已验收通过，下一步进入 P4（后端持久化、日志与运维）。

---

## P1：仓库基线与开发环境固定（已完成）

目标：把后端和 ESP32 两条主线放到可追踪、可回滚、可验证的状态。

### P1 后端执行结果

#### 1. 后端当前状态

- `xiaozhi-esp32-server/` 已恢复为正式 Git 仓库 ✅
- 分支：`xiaoclawbrain-v0.1` ✅
- 基准 commit：`fb7e2e17a8340232155d35bb2f988522d3b3232b` ✅
- 仓库完整，包含 `main/xiaozhi-server/` 等所有子目录

#### 2. 备份保留

- `xiaozhi-esp32-server.zip`（155MB）：原始 ZIP 文件，未改动
- `xiaozhi-esp32-server_old_20260520/`：原解压目录备份，无 git commit

#### 3. .env 状态

- ZIP 备份中没有 `.env` 或 `.env.example` 文件
- config.yaml 中有 `server.auth.enabled: false` 默认配置
- 敏感配置通过 `data/.config.yaml` 覆盖机制保护

---

### P1 ESP32 调查结论

#### 1. ESP32 当前状态

- 分支：`main`（与文档目标分支 `xiaoclaw-ghproxycom` 不一致）
- 远程：`origin/main`
- 未提交改动：`19 个文件，+833 / -368 行`

#### 2. 未提交改动分类

**保留类**（需要保留到新架构）：
- `main/boards/common/wifi_board.cc`：+262 行，WiFi 网络事件回调重构
- `main/boards/common/wifi_board.h`：+4 行，头文件调整
- `main/audio/audio_service.cc`：+30 行，音频服务增强
- `main/audio/audio_codec.cc`：+9 行，音频编解码调整
- `main/mimi/agent/agent_loop.c`：+144 行，agent_loop 内存管理优化（internal/fallback PSRAM）
- `main/mimi/agent/context_builder.c`：+186 行，上下文构建器增强
- `main/application.cc` 部分改动：NetworkEvent 调度、callback 初始化、protocol 判空保护

**待迁移类**（需要重新设计后迁移到新架构）：
- `main/providers/`（新增未追踪目录）：包含 `baidu_asr_provider.cc/h`、`baidu_tts_provider.cc/h`、`openclaw_runtime_config` 等
  - 这些是旧本地 ASR/TTS provider，P6 需要清理或迁移到新协议适配层
  - 当前 `main` 改动已经移除了 `application.cc` 中对 `bridge_send_to_agent()` 的直接调用，说明主链路正在从旧架构迁移

**废弃类**（后续 P6 清理）：
- `main/bridge/bridge.cc` 改动：主要是内存分配调整（MALLOC_CAP_INTERNAL fallback）
- 任何涉及 `{"type":"tts","audio":"<base64_opus>"}` 旧协议的处理逻辑
- `main/mimi/` 下本地 provider 相关逻辑（确认前先保持不变）

#### 3. ESP32 当前分支问题

- 当前在 `main` 分支，与文档目标 `xiaoclaw-ghproxycom` 不一致
- 这可能是之前 beancookie/xinnan-tech 的开发分支
- 下一步需要确认是否切换分支，或将现有改动提交后重新基于 `xiaoclaw-ghproxycom` 开发

#### 4. P5/P6 清理要点（背景记录，不要现在实施）

- `main/providers/baidu_*`：本地 ASR/TTS，P6 时评估是否需要
- `main/bridge/`：`bridge_send_to_agent()` 在当前改动中已从主链路移除
- `main/mimi/`：MimicLaw Agent 循环相关代码，P6 时需要评估 ESP32 端的精简需求

---

### P1 验收结果

- [x] 后端 `xiaozhi-esp32-server/` 仓库已成功恢复
- [x] 分支 `xiaoclawbrain-v0.1` 已创建，基准 commit `fb7e2e1` 已 checkout
- [x] ESP32 当前在 `main` 分支，有 19 个文件未提交改动，已分类
- [x] 没有误删、回滚、覆盖用户已有改动
- [x] 项目开发进度文档已更新

**P1 下一步入口**：进入 P2 后端协议骨架与鉴权硬化。

---

## P2：后端协议骨架与鉴权硬化（已完成）

目标：把已经验证过的 WebSocket 链路变成可依赖的第一版协议入口。

### P2 执行结果

#### 1. 新建 `core/protocol.py` 轻量协议模块

- 文件：`main/xiaozhi-server/core/protocol.py`
- 常量：`MAX_JSON_MESSAGE_BYTES=4096`、`MAX_BINARY_FRAME_BYTES=2048`
- 函数：`sanitize_headers()`、`sanitize_header_value_for_log()`、`make_protocol_error()`、`validate_inbound_frame()`
- `validate_inbound_frame()` 返回 `None`（正常）或 `{"code": ..., "message": ..., "size": ...}`（超限）

#### 2. 更新 `connection.py` 使用 protocol 模块

- 移除 `connection.py` 中重复的常量和函数定义
- 改从 `core.protocol` 导入：`sanitize_headers`、`validate_inbound_frame`、`make_protocol_error`
- `_route_message()` 入口改用 `validate_inbound_frame()` 检查

#### 3. 实现真正的单设备 Token 鉴权

- `core/auth.py` 新增 `extract_bearer_token()`、`verify_single_device_token()`
- `core/websocket_server.py` 新增 `device_auth_mode`、`device_token` 配置读取
- `_handle_auth()` 支持两种模式：
  - `mode=single`：用 `verify_single_device_token()` 简单比对
  - `mode=legacy`（默认）：走原有的 HMAC token 验证逻辑

#### 4. 环境变量真正生效

- `config/config_loader.py` 新增 `_truthy()`、`apply_env_overrides()`
- 支持：`SERVER_AUTH_ENABLED`、`DEVICE_AUTH_MODE`、`DEVICE_TOKEN`、`SERVER_IP`、`SERVER_PORT`
- 在 `load_config()` 合并配置后调用

#### 5. 重写 smoke test，导入生产代码

- 文件：`tests/test_connection_protocol.py`
- 14 个测试用例，全部从生产模块导入（不是复制）
- 覆盖：`extract_bearer_token`、`verify_single_device_token`、`validate_inbound_frame`、`sanitize_headers`、`make_protocol_error`、`_truthy`、`apply_env_overrides`

### P2 验收结果

- [x] pytest 14 passed（导入生产模块，无复制常量）
- [x] `config.config_loader` 可在 Python 3.9 下正常导入（移除 `str | None` 注解）
- [x] 无旧 TTS base64 下发路径（`rg -n '"type":\s*"tts".*"audio"|base64_opus'` 无结果）
- [x] 环境变量通过 `apply_env_overrides()` 生效
- [x] 单设备 Token 鉴权 `mode=single` 实现

**P3 下一步入口**：进入 P4 后端持久化、日志与运维。

---

### P2 执行记录（2026-05-20）

#### 验证命令

```bash
cd /Volumes/软件/opencode/ESP32S3语音陪伴设备开发/xiaozhi-esp32-server

# pytest smoke test
python3 -m pytest tests/test_connection_protocol.py -v --tb=short
# 预期：14 passed

# config_loader import (Python 3.9 兼容)
python3 -c "import sys; sys.path.insert(0, 'main/xiaozhi-server'); import config.config_loader as c; print(c._truthy('true'))"
# 预期：True

# 检查无旧 TTS base64 下发
rg -n '"type":\s*"tts".*"audio"|base64_opus' main/xiaozhi-server/core tests
# 预期：（无输出）

# git status
git status --short --branch
# 预期：
# ## xiaoclawbrain-v0.1
# M  main/xiaozhi-server/config/config_loader.py
# M  main/xiaozhi-server/core/auth.py
# M  main/xiaozhi-server/core/connection.py
# M  main/xiaozhi-server/core/websocket_server.py
# ?? .env.example
# ?? main/xiaozhi-server/core/protocol.py
# ?? tests/
```

#### 改动文件清单

| 文件 | 改动 |
|------|------|
| `main/xiaozhi-server/core/protocol.py` | 新增：轻量协议模块（常量、脱敏、帧验证） |
| `main/xiaozhi-server/core/connection.py` | 改用 `core.protocol` 导入，移除重复定义 |
| `main/xiaozhi-server/core/auth.py` | 新增 `extract_bearer_token()`、`verify_single_device_token()` |
| `main/xiaozhi-server/core/websocket_server.py` | 支持 `device_auth_mode=single`，支持脱敏日志 |
| `main/xiaozhi-server/config/config_loader.py` | 新增 `apply_env_overrides()`，移除 `str | None` 注解（Python 3.9 兼容） |
| `.env.example` | 全空值模板 |
| `tests/test_connection_protocol.py` | 重写为导入生产模块的 14 个测试用例 |

#### 下一步

- P3：后端稳定听、稳定想、稳定说
- ASR/LLM/TTS timeout、错误协议、状态机、ASR_EMPTY
# 预期：## xiaoclawbrain-v0.1
# M main/xiaozhi-server/core/connection.py
# ?? .env.example
# ?? tests/
```

#### 下一步

- P3：后端稳定听、稳定想、稳定说
- ASR/LLM/TTS timeout、错误协议、状态机、ASR_EMPTY

---

### P1 执行记录（2026-05-20）

#### 后端仓库恢复步骤

1. 检测到 GitHub 访问需要代理（`127.0.0.1:7897`）
2. 通过 `git clone --depth=50 https://github.com/MadBull8994/xiaozhi-esp32-server.git` 克隆到工作区
3. 执行 `git checkout -b xiaoclawbrain-v0.1` 创建目标分支
4. 验证 HEAD = `fb7e2e17a8340232155d35bb2f988522d3b3232b` ✅

#### 目录结构

```
xiaozhi-esp32-server/           # 正式 Git 仓库
├── main/xiaozhi-server/         # 主要服务端代码
│   ├── docker-compose.yml       # Docker 编排（ghcr.nju.edu.cn/xinnan-tech 镜像）
│   ├── requirements.txt         # Python 依赖（43 个包）
│   └── config.yaml              # 主配置（server.auth.enabled=false）
├── main/manager-*/              # 管理端 Web 前端
├── main/digital-human/          # 数字人相关
└── docker-setup.sh             # 自动化部署脚本
```

#### 关键配置记录

| 项目 | 当前值 |
|------|--------|
| `server.auth.enabled` | `false`（默认，需在 P2 硬化） |
| `tts_timeout` | `10`（需在 P3 调整为 8） |
| `close_connection_no_voice_time` | `120` 秒 |
| `websocket ping` | `false` |

#### 网络环境

- 系统代理：`127.0.0.1:7897`（HTTP/SOCKS5）
- GitHub clone 需要通过代理访问

#### 备份记录

- 原始 ZIP：`xiaozhi-esp32-server.zip`（155MB，未改动）
- 旧备份目录：`xiaozhi-esp32-server_old_20260520/`（原 ZIP 解压目录，无 git commit）
- Git 仓库：`xiaozhi-esp32-server/`（新 clone，分支 xiaoclawbrain-v0.1）

---

## P2：后端协议骨架与鉴权硬化

- [ ] 单设备 Token 鉴权：`Authorization: Bearer <DEVICE_TOKEN>`
- [ ] 兼容并脱敏记录 `device-id` / `client-id`
- [ ] JSON 消息最大 4KB
- [ ] binary frame 最大 2048 bytes
- [ ] JSON 控制消息与 binary 音频帧明确分流
- [ ] smoke test：鉴权成功、鉴权失败、JSON 超限、binary 超限、binary 下发

---

## P3：后端稳定听、稳定想、稳定说

- [ ] Opus 音频接收与会话管理
- [ ] ASR timeout 5 秒
- [ ] ASR_EMPTY 第一次即提示“我没听清，你再说一遍”，不进入 LLM
- [ ] 声音小于 500ms 或能量过低时丢弃
- [ ] 单字/短词白名单：好、对、嗯、是、否、不、停、停止、继续、不要
- [ ] LLM timeout 20 秒，失败重试 1 次
- [ ] TTS timeout 8 秒，不重试
- [ ] TTS 长文本按句子分段
- [ ] TTS 只通过 WebSocket binary Opus 小包下发
- [ ] 单轮对话总 timeout 35 秒
- [ ] 基础状态机：`idle → wakeup_detected → listening → uploading_audio → recognizing → thinking → synthesizing → speaking → idle`

---

## P4：后端持久化、日志与运维

- [ ] SQLite session 级对话记忆
- [ ] 最近上下文读取
- [ ] 日志格式、级别、轮转
- [ ] 日志敏感信息脱敏
- [ ] 用户 ASR 文本默认只记录摘要或前 50 字
- [ ] 调试模式通过环境变量开启，默认关闭
- [ ] `/health` 或等价健康检查
- [ ] Docker healthcheck

---

## P5：ESP32 瘦客户端协议适配

- [ ] WS 连接携带 Token、device-id、client-id
- [ ] 发送 `hello` / `listen` / 状态 JSON
- [ ] BOOT 按键进入 `wakeup_detected` 或 `listening`
- [ ] Opus binary 上传
- [ ] 接收服务端 binary Opus 并播放
- [ ] 处理 `stt`、`sentence_*`、`tts_*`、`error`
- [ ] TFT 显示 idle/listening/thinking/speaking/error/reconnecting

---

## P6：ESP32 旧架构清理与本地降级

- [ ] 移除或禁用旧 `tts_response.audio` base64 播放路径
- [ ] 移除或弱化本地 Baidu ASR/TTS provider
- [ ] 移除 bridge / MimicLaw Agent / context_builder 对主链路的依赖
- [ ] 保留音频、显示、按键、WiFi、WebSocket、硬件抽象
- [ ] 网络断开或后端不可用时播放本地 Opus 降级提示音

---

## P7：端到端联调与异常恢复

- [ ] ESP32 BOOT → 录音 → WS → ASR → LLM → TTS → WS → ESP32 播放 → idle
- [ ] 断线重连验证
- [ ] ASR 空文本验证
- [ ] LLM 超时和重试验证
- [ ] TTS 失败验证
- [ ] 声音太短或能量过低验证
- [ ] 单轮总 timeout 验证
- [ ] 连续多轮稳定性测试

---

## P8：自定义 Skill 与 Agent 循环

- [ ] `skill_loader.py`：Skill 子进程隔离加载
- [ ] `skill_plugin.py`：Skill 接口与注册机制
- [ ] `agent_loop.py`：ASR → Skill 匹配 → LLM → TTS 调度
- [ ] 兼容 OpenClaw / agent 项目风格的 `SKILL.md` 元数据
- [ ] 支持本地自定义 Skill
- [ ] 支持 GitHub Skill 导入到 pending 目录
- [ ] 实现 `SKILL_SECURITY_LEVEL=high|low|off`
- [ ] `high` 默认：timeout 5 秒、limited_env、cwd 限制、stdout 64KB、stderr 写日志
- [ ] `low` 开发/实验：timeout/env/stdout/network 可按配置放宽
- [ ] `off` 用户自担：不主动限制 Skill 执行，但必须记录 `security_level=off`
- [ ] GitHub Skill 启用必须受安全等级和用户确认控制

---

## P9：ESP32 本地语音唤醒

- [ ] 评估 ESP-SR / WakeNet / 自定义轻量唤醒词
- [ ] ESP32 本地持续检测唤醒词
- [ ] 唤醒后进入 `idle → wakeup_detected → listening → uploading_audio`
- [ ] 预留或实现 0.5-1 秒环形音频缓冲
- [ ] 误唤醒记录与阈值调优
- [ ] BOOT 按键保留为调试/备用入口

---

## 完成记录

### 2026-05-20

- **P8 方案更新**：自定义 Skill 改为支持三档安全等级：`high` 默认、`low` 开发/实验、`off` 用户自担；支持 OpenClaw / agent 项目风格 `SKILL.md` 兼容和 GitHub Skill pending 导入。
- **文档同步**：同步更新路线图、速览、架构方案和本进度文档中的 Skill/P8 口径。
- **P0 完成**：新增 `项目开发路线图_XiaoClawBrain.md`，按 P0-P9 重新安排整体开发顺序。
- **文档同步**：更新 `START_HERE_项目速览.md`，加入当前执行路线和路线图链接。
- **架构同步**：更新 `架构方案_后端服务器_XiaoClawBrain_GPT修订定稿版.md`，将旧 MVP 里程碑改为 P0-P9。
- **进度同步**：重写本文件，明确当前下一步为 P1：仓库基线与开发环境固定。
- **问题记录**：本次未更新 `开发问题解决记录.md`，因为没有新增并解决开发问题。

### 2026-05-19

- **阶段一核心链路技术验证**：Docker 全模块部署 + oMLX ASR/LLM/TTS 全本地对接 + WebSocket 全链路闭环。
- **部署**：4 容器（server/web/MySQL/Redis）在 Docker Desktop (Apple Silicon) 稳定运行，配置文件在 `~/xiaozhi-server/`。
- **Provider 配置**：通过 MySQL `ai_model_config` 表直接配置，ASR→OpenAI(oMLX)、LLM→Ollama(oMLX)、TTS→OpenAI(oMLX)，均指向 `host.docker.internal:8009`。
- **认证**：在 `sys_params` 表中关闭 `server.auth.enabled`（设为 false），WebSocket 连接需带 `device-id` header。
- **超时**：`tts_timeout` 调整为 30s。
- **验证结果**：LLM 文字对话正常、TTS 生成语音并通过 29 个 binary Opus 帧下发、ASR provider 加载正确（openai.py，非 fun_local.py）。
- **ESP32 诊断**：设备连接正常（`/dev/cu.usbmodem5B5E1028821`），当前固件为旧 MimicLaw Agent，不兼容 xiaozhi 客户端协议，需烧录 `78/xiaozhi-esp32` v2.2.6 固件。
- **esptool**：v4.11.0 已安装（`python3 -m esptool`），可用于烧录。
- **问题解决**：FunASR model.pt 目录错误（移除 mount）、WebSocket device-id 缺失（添加 header）、认证错误（DB 关闭 auth）、TTS 冷启动超时（模型预热即可）。

### 2026-05-17

- 项目工作区资产整理完成：归档了旧版本架构文档、旧方案；恢复并保留了硬件相关资产（`hardware_bringup/`、`开发问题解决记录.md`、`测试用API.rtf`、`硬件验证闭环记录_2026-05-06.md`）。
- 创建本进度文档，初始化所有阶段和任务项。
- 更新 `AGENTS.md`：修正架构方案文档引用，补充开发工作流要求（进度更新 + 问题记录）。

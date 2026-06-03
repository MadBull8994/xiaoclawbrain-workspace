# P8 完整方案：自定义 Skill 与 Agent 循环

创建时间：2026-05-28

适用基底：`XCB-STABLE-P7-complete+20260528.142020`

## 目标

P8 的目标是在 P7 稳定语音主链路之上，增加后端自定义 Skill 能力和最小 Agent 调度能力。

P8 不是 Skill 市场，不是完整第三方 agent runtime，也不把 Skill 放到 ESP32 端执行。P8 只做 XiaoClawBrain 后端 Skill 兼容层：

```text
ASR 文本
-> bind / output limit / fast path
-> Agent loop
-> 全局默认 Skill（可选，每轮运行）
-> 显式 Skill 匹配
-> Skill runner 子进程执行
-> 结构化结果
-> LLM 整理或直接回复
-> TTS 播报
-> WebSocket binary Opus 下发
```

P8 完成后应具备：

- 兼容 `~/.agents/skills`
- 兼容导入已有 `SKILL.md` skill 包
- 支持项目内 `enabled` / `pending` / `disabled` 管理
- 支持默认全局运行 Skill，用于每轮对话都需要使用的能力
- 默认 `high` 安全等级
- Skill 失败、超时、输出过大时不影响主服务
- 未命中 Skill 时继续普通 LLM 对话

## 核心边界

### 直接复用

可以直接复用外部 Agent Skills 生态中的资产格式：

- `SKILL.md`
- YAML frontmatter 中的 `name`、`description`、`license`、`compatibility`、`metadata`
- 目录约定：`scripts/`、`references/`、`templates/`、`assets/`
- `~/.agents/skills/<name>/SKILL.md`

### 需要改造

已有 `SKILL.md` 主要是给 agent 读的说明，不等同于后端可执行插件。XiaoClawBrain 需要自己的执行声明文件：

```text
xiaoclaw.skill.json
```

只有同时存在 `SKILL.md` 和 `xiaoclaw.skill.json`，且被启用的 Skill，才允许进入后端子进程执行。

### 不复用

P8 不直接复用以下内容：

- OpenClaw / OpenCode / Hermes / Codex 的 agent runtime
- Hub / Tap / Marketplace / 自动更新系统
- 第三方 prompt 注入逻辑
- ESP32 旧 MimicLaw C skill loader

原因：这些系统主要服务于 coding agent 或本地 agent runtime，而 XiaoClawBrain 需要的是语音后端里的可控子进程执行层。

## 目录结构

后端建议结构：

```text
xiaozhi-server/
  core/skills/
    skill_manifest.py
    skill_discovery.py
    skill_registry.py
    skill_runner.py
    skill_matcher.py
    skill_importer.py
    skill_global.py
  core/handle/
    agent_loop.py
  skills/
    echo/
      SKILL.md
      xiaoclaw.skill.json
      scripts/run.py
    device-status-mock/
      SKILL.md
      xiaoclaw.skill.json
      scripts/run.py
    persona-context-mock/
      SKILL.md
      xiaoclaw.skill.json
      scripts/run.py
  data/skills/
    enabled/
    pending/
    disabled/
```

扫描路径：

```text
data/skills/enabled/
skills/
.agents/skills/
~/.agents/skills/
data/skills/pending/
```

优先级：

```text
data/skills/enabled > skills > .agents/skills > ~/.agents/skills
```

`pending` 只展示和校验，不参与自动匹配和执行。

## Skill 包格式

### SKILL.md

`SKILL.md` 保持兼容 Agent Skills 生态，负责说明这个 skill 是什么、适合何时使用、需要哪些资源。

最小示例：

```markdown
---
name: echo
description: Echo input text for XiaoClawBrain P8 smoke testing.
---

# Echo

Return the input text unchanged. This skill is only for local smoke tests.
```

要求：

- `name` 必填
- `description` 必填
- 目录名必须等于 `name`
- `name` 使用 `^[a-z0-9]+(-[a-z0-9]+)*$`
- 长度 1-64

### xiaoclaw.skill.json

`xiaoclaw.skill.json` 是 XiaoClawBrain 的可执行声明。

最小示例：

```json
{
  "version": 1,
  "name": "echo",
  "runtime": "python",
  "entrypoint": "scripts/run.py",
  "enabled": true,
  "security_level": "high",
  "triggers": {
    "explicit_names": ["echo", "回声"],
    "keywords": ["复述", "重复"]
  },
  "auto_run": {
    "enabled": false
  }
}
```

字段规则：

- `version`: 当前固定为 `1`
- `name`: 必须等于 `SKILL.md` frontmatter name 和目录名
- `runtime`: P8 第一版只支持 `python`
- `entrypoint`: 相对 skill 目录的脚本路径，不能包含 `..`
- `enabled`: 是否允许执行
- `security_level`: `high` / `low` / `off`
- `triggers`: 显式触发配置
- `auto_run`: 全局默认运行配置

没有 `xiaoclaw.skill.json` 的 skill 可以被发现，但不可执行。

## 全局默认 Skill

新增全局默认 Skill 能力的原因：有些能力不是偶尔调用，而是每轮对话都要参与，例如固定人格上下文、家庭规则、设备状态摘要、用户偏好整理、输入规范化或长期使用的业务逻辑。

最终是否全局默认运行，必须由后端配置决定。`xiaoclaw.skill.json` 只能声明某个 Skill 支持 `auto_run`，不能单独让外部导入的 Skill 自动进入全局默认运行。这样可以在后端集中选择：

```text
- 哪些 Skill 每轮默认运行
- 哪些 Skill 只在显式触发或匹配命中时运行
- 哪些 Skill 只允许被发现/导入，不允许执行
```

全局默认 Skill 不等于后台常驻进程。P8 第一版采用“每轮对话按阶段运行一次”的模型：

```text
每轮有效 ASR 文本
-> Agent loop
-> 按配置运行 global auto_run skill
-> 把结果作为上下文、直接回复或过滤结果
-> 继续显式 Skill / LLM / TTS
```

### auto_run 能力声明

`xiaoclaw.skill.json` 中的 `auto_run` 是能力声明，不是最终启用开关。示例：

```json
{
  "version": 1,
  "name": "persona-context",
  "runtime": "python",
  "entrypoint": "scripts/run.py",
  "enabled": true,
  "security_level": "high",
  "auto_run": {
    "enabled": true,
    "scope": "global",
    "phase": "before_llm",
    "order": 50,
    "failure_policy": "continue",
    "include_fast_path": false,
    "max_runs_per_turn": 1
  }
}
```

字段含义：

- `enabled`: 表示该 Skill 允许被配置为自动运行，但最终是否运行由后端配置决定
- `scope`: P8 只支持 `global`
- `phase`: P8 第一版优先实现 `before_llm`
- `order`: 多个全局 Skill 的运行顺序，数字小的先运行
- `failure_policy`: `continue` / `block_llm` / `reply_error`
- `include_fast_path`: 是否也影响 fast path，默认 `false`
- `max_runs_per_turn`: 默认 `1`，防止循环调用

### 后端选择策略

后端配置是最终生效来源。建议使用 allowlist 方式选择默认全局 Skill：

```yaml
skills:
  global_defaults:
    enabled: true
    selected:
      - name: persona-context
        phase: before_llm
        order: 10
        failure_policy: continue
      - name: device-status-context
        phase: before_llm
        order: 20
        failure_policy: continue
```

执行规则：

- 只有 `global_defaults.selected` 中列出的 Skill 才会每轮默认运行。
- Skill 包自己的 `auto_run.enabled=true` 只是允许被选中；未被后端选中时，仍然只按需运行。
- 如果后端选中了某个 Skill，但该 Skill 未声明 `auto_run.enabled=true`，则启动时记录配置错误，并跳过该 Skill。
- 如果同名 Skill 存在多个来源，按发现优先级解析到最终 Skill 后，再判断是否可全局运行。
- pending Skill 永远不能进入 `global_defaults.selected` 的运行结果，即使配置里写了也要跳过并记录。

按需运行 Skill 的配置建议：

```yaml
skills:
  on_demand:
    enabled: true
    allow_all_enabled: false
    selected:
      - name: echo
        trigger_mode: explicit_only
      - name: device-status
        trigger_mode: explicit_or_keywords
      - name: weather-helper
        trigger_mode: llm_tool_call
        enabled: false
```

规则：

- `allow_all_enabled=true` 时，所有 enabled 且有 `triggers` 的 Skill 都可以按其 manifest 触发。
- `allow_all_enabled=false` 时，只有 `on_demand.selected` 列出的 Skill 可以按需运行。
- 全局默认 Skill 也可以同时按需运行，但必须显式配置或满足 `allow_all_enabled=true`。
- `trigger_mode=explicit_only`：只有用户明确说“运行技能 / 使用技能 / 调用技能”时才运行。
- `trigger_mode=explicit_or_keywords`：显式触发或命中保守关键词时运行。
- `trigger_mode=llm_tool_call`：保留给后续 LLM 自动判断，P8 第一版默认不启用。
- 显式触发命中后，skill 名匹配可以不区分大小写，但传给 runner 的 `args` 必须保留用户原始输入，不得在匹配阶段被 lower-case 或改写。

按需判断顺序：

```text
1. 显式触发：运行技能 / 使用技能 / 调用技能
2. 关键词/别名匹配：来自 xiaoclaw.skill.json 的 triggers.keywords
3. LLM tool-call 判断：后续阶段可选，P8 第一版默认关闭
```

关键词匹配必须保守：

- 优先使用完整短语，例如 `检查设备状态`
- 不使用 `设备`、`状态` 这类泛词
- 命中后仍要确认该 Skill 在后端 `on_demand` 范围内
- pending / disabled / metadata-only Skill 不参与匹配

### 支持阶段

P8 按阶段逐步支持：

```text
P8-Global-1: before_llm
P8-Global-2: after_llm
P8-Global-3: after_asr
```

第一版必须先做 `before_llm`，因为它最安全：只给 LLM 增加上下文，不直接抢答。

`after_asr` 和 `after_llm` 可以后置，因为它们更容易影响 fast path、显式 Skill 和最终播报内容。

### 全局 Skill 输出

全局 Skill stdout：

```json
{
  "ok": true,
  "context": "用户偏好：回答尽量简短，语气温柔。",
  "speak": "",
  "data": {
    "tags": ["persona"]
  }
}
```

字段规则：

- `context`: 可追加到 LLM 输入的上下文
- `speak`: 如果非空，表示该全局 Skill 想直接回复
- `data`: 结构化数据，供后续 Agent loop 使用

P8 第一版建议：

- `before_llm` 阶段只使用 `context`
- 默认忽略 `speak`，除非 manifest 明确允许 `can_reply: true`
- 多个全局 Skill 的 `context` 按 `order` 拼接，并做长度上限

### 全局 Skill 运行限制

全局 Skill 必须比普通显式 Skill 更保守：

- 默认 `security_level=high`
- 单个全局 Skill timeout 默认 3 秒，可低于普通 Skill 的 5 秒
- 单轮所有全局 Skill 总预算默认 5 秒
- 单轮全局 Skill 最多 3 个
- 任一全局 Skill 失败，默认 `failure_policy=continue`
- 失败只写日志，不直接打断对话
- 全局 Skill 不能默认读取完整密钥
- 全局 Skill 不应默认影响 fast path

### 全局 Skill 配置

`config.yaml` 建议：

```yaml
skills:
  enabled: true
  security_level: high
  paths:
    project_enabled: data/skills/enabled
    project_builtin: skills
    project_agents: .agents/skills
    home_agents: ~/.agents/skills
    pending: data/skills/pending
  global_defaults:
    enabled: true
    phase: before_llm
    selected:
      - name: persona-context
        phase: before_llm
        order: 10
        failure_policy: continue
    max_skills_per_turn: 3
    total_timeout: 5
    context_char_limit: 1000
    include_fast_path: false
  on_demand:
    enabled: true
    allow_all_enabled: true
    selected: []
```

## Skill 输入输出协议

Skill stdin：

```json
{
  "version": 1,
  "utterance": "运行技能 echo 你好",
  "args": {
    "text": "你好"
  },
  "session_id": "session-id",
  "device_id": "device-id",
  "phase": "explicit",
  "metadata": {
    "source": "voice"
  }
}
```

Skill stdout：

```json
{
  "ok": true,
  "speak": "你好",
  "context": "",
  "data": {}
}
```

失败输出：

```json
{
  "ok": false,
  "error_code": "SKILL_TIMEOUT",
  "message": "技能执行超时"
}
```

如果 stdout 不是 JSON，P8 第一版可以把 stdout 当作纯文本 `speak`，但仍受 stdout 限制。

## 错误码

P8 增加 Skill 错误码：

```text
SKILL_NOT_FOUND
SKILL_DISABLED
SKILL_TIMEOUT
SKILL_OUTPUT_TOO_LARGE
SKILL_INVALID_OUTPUT
SKILL_EXEC_ERROR
SKILL_SECURITY_BLOCKED
SKILL_IMPORT_PENDING
SKILL_GLOBAL_FAILED
```

默认策略：

- 显式 Skill 失败：播报友好错误，然后回到普通对话收口
- 全局默认 Skill 失败：默认只记录日志，继续普通对话
- `failure_policy=block_llm` 时，全局 Skill 失败可以阻止 LLM，但必须返回明确错误
- `failure_policy=reply_error` 时，可以播报“技能暂时不可用”

## 安全等级

### high 默认

用于生产环境和未知来源 Skill：

- 子进程执行，不直接 `import` / `exec` 未知脚本
- timeout 默认 5 秒；全局 Skill 默认 3 秒
- cwd 限制到当前 skill 目录
- limited_env，不传完整系统环境
- 不传 `.env`
- 不传 API Key、Secret、Token
- stdout 最大 64KB
- stderr 写入日志，不直接返回给用户
- 非 0 退出码视为失败
- GitHub / imported Skill 默认只能进 pending

### low 开发/实验

用于用户自己写的 Skill：

- 仍通过子进程执行
- timeout 默认 30 秒，可配置
- env allowlist 可配置
- stdout 默认 256KB，可配置
- 可允许网络访问
- 必须记录 `security_level=low`

### off 用户自担

用于完全信任的本地实验：

- 用户确认后运行
- 不主动限制 timeout/env/cwd/stdout
- 必须记录 `security_level=off`
- UI / CLI / 配置中必须明确提示风险

P8 第一轮可以先识别 `off` 并拒绝执行，后半段再决定是否开放真正执行。

## 导入和启用

### 本地目录导入

本地已有 skill 包导入流程：

```text
输入本地路径
-> 校验存在 SKILL.md
-> 校验 name 和目录名
-> 复制到 data/skills/pending/<name>
-> 记录 source=local
-> 不自动启用
```

### 远程归档导入（兼容 GitHub / 镜像 / archive URL）

远程归档 skill 导入流程：

```text
输入 URL（GitHub repo / 镜像 repo / 直接 archive URL）
-> 归一化 URL（repo → archive 下载；archive 直用）
-> 下载到临时目录
-> 解包 zip/tar.gz（含路径逃逸保护）
-> 找到 skill 根目录（兼容外层 wrapper 目录）
-> 校验 xiaoclaw.skill.json
-> 复制到 data/skills/pending/<name>
-> 记录 source=remote、来源 URL、导入时间
-> 不自动启用
```

不写死 `github.com`，兼容：
- `https://github.com/user/repo`
- `https://gitmirror.example.com/user/repo`
- `https://example.com/path/to/skill.zip`
- `https://example.com/path/to/skill.tar.gz`

### 启用

pending 启用流程：

```text
pending/<name>
-> 校验 SKILL.md
-> 校验 xiaoclaw.skill.json
-> 校验 security_level
-> 用户确认
-> 移动或复制到 data/skills/enabled/<name>
```

没有 `xiaoclaw.skill.json` 的已有 skill 包可以导入，但需要用户补齐执行声明后才能启用。

## Agent Loop 设计

P8 第一版 Agent loop 只做必要编排：

```text
startToChat(actual_text)
-> bind/output limit/abort 现有逻辑
-> fast path 现有逻辑
-> agent_loop.handle_text(conn, actual_text)
   -> run global before_llm skills
   -> explicit skill matcher
   -> keyword skill matcher
   -> if on-demand skill hit: run skill and return reply
   -> else: return enriched LLM context
-> 未被 Skill 直接处理时，继续 conn.chat(...)
```

为了不破坏 P7：

- fast path 默认优先于全局 Skill
- bind 和 output limit 仍优先于 Skill
- 显式 Skill 优先于关键词 Skill，关键词 Skill 优先于普通 LLM
- global before_llm Skill 默认只提供上下文
- 未命中 Skill 时必须走原普通 LLM
- LLM 自动选择 Skill 在 P8 第一版默认关闭，只保留配置字段

## P8 小任务拆分

### P8-0 文档和配置冻结

目标：固定 P8 方案、配置字段、目录结构、manifest、错误码、安全等级、全局默认 Skill 规则。

验收：

- 本方案文档保存
- `START_HERE` / 架构方案 / 进度文档同步
- P8 不提前启动 P9

### P8-1 Skill Manifest 与发现

目标：发现并解析 `SKILL.md` 和 `xiaoclaw.skill.json`。

范围：

- `core/skills/skill_manifest.py`
- `core/skills/skill_discovery.py`
- 支持 `~/.agents/skills`
- 支持 pending 只展示不执行

验收：

- 可扫描项目内 skill
- 可扫描 home `~/.agents/skills`
- 没有 manifest 的 skill 标记为 metadata-only，不可执行
- 同名冲突按优先级处理

### P8-2 Skill Runner high 模式

目标：实现高安全默认子进程执行。

范围：

- `core/skills/skill_runner.py`
- stdin/stdout JSON
- timeout、cwd、limited_env、stdout 限制、stderr 日志

验收：

- 正常脚本返回 speak/data
- 超时返回 `SKILL_TIMEOUT`
- 非 0 返回 `SKILL_EXEC_ERROR`
- stdout 超限返回 `SKILL_OUTPUT_TOO_LARGE`
- 非法 JSON 返回 `SKILL_INVALID_OUTPUT`

### P8-3 内置样例 Skill

目标：提供不依赖外网和密钥的验收 skill。

样例：

- `echo`
- `device-status-mock`
- `persona-context-mock`（用于全局默认 Skill 验收）

验收：

- 三个样例都能被发现
- `echo` 可显式触发
- `persona-context-mock` 可作为 global before_llm context 运行

### P8-4 全局默认 Skill

目标：支持每轮默认运行 Skill。

范围：

- `core/skills/skill_global.py`
- `auto_run.enabled`
- `phase=before_llm`
- `order`
- `failure_policy=continue`
- 总预算和数量限制

验收：

- 全局 Skill 每轮普通 LLM 前运行
- 只有后端配置选中的全局 Skill 会每轮运行
- 未被后端选中的 Skill 即使声明 `auto_run.enabled=true`，也只能按需运行
- 结果作为 LLM context 注入
- 默认不影响 fast path
- 失败不打断普通对话
- 日志记录 global skill 调用、耗时、安全等级

### P8-5 Skill Matcher

目标：实现按需运行 Skill 的判断。

触发方式：

```text
运行技能 echo 你好
使用技能 device-status
调用技能 weather-helper
```

验收：

- `explicit_only` 只响应显式触发
- `explicit_or_keywords` 可响应显式触发和保守关键词
- 关键词未命中时正常 LLM
- `llm_tool_call` 字段保留但默认关闭，不做 LLM 自动选 Skill
- pending / disabled / metadata-only Skill 不参与按需匹配

### P8-6 LLM Tool-Call Skill 接入

目标：把 `trigger_mode=llm_tool_call` 的 Skill 注册为 LLM function/tool，LLM 选中后用 `SkillRunner` 执行，结果回灌当前对话轮。

范围：

- `core/skills/skill_tool_adapter.py` — 薄注册层，筛选 `llm_tool_call` Skill 映射为 OpenAI function schema
- `core/connection.py` `chat()` — 追加 skill tools 到 `functions` 列表；分发时检测 `skill__` 前缀，走 `SkillRunner`
- `skills/device-status-tool-mock/` — `llm_tool_call` 样例 skill，返回 `data` 无 `speak`
- `config.yaml` — `skills.llm_tool_call.enabled` 开关

设计要点：

- **注册点**：`get_llm_skill_tools(config, registry)`，只筛选 `on_demand.selected` 中 `trigger_mode=llm_tool_call` 的 skill
- **工具 schema**：统一前缀 `skill__<name>`，单参数 `text`，不扩展复杂 JSON schema
- **分发点**：`chat()` 中工具调用循环，`is_skill_tool_call()` → `_execute_skill_tool()` → `SkillRunner.run(phase="llm_tool_call")`
- **结果回灌**：有 `speak` → `Action.RESPONSE`；无 `speak` 有 `data` → `Action.REQLLM`
- **失败策略**：timeout / exec-error / not-found → `Action.REQLLM` 返回 tool error，不打断整轮
- **不改 P8-5**：`match_explicit()` / `match_keywords()` 不变；`llm_tool_call` 不出现于显式/关键词路径

不做的：

- 不改 P8-5 的显式/关键词 matcher
- 不把 `llm_tool_call` 再接回 P8-5 matcher
- 不做多步 agent 编排
- 不做复杂 planner/reasoner
- 不自动串多个 skill

验收：

- `llm_tool_call` skill 被注册给 LLM，`explicit_only` / `explicit_or_keywords` 不会
- LLM 返回 skill tool_call 时，后端调用 `SkillRunner.run(phase="llm_tool_call")`
- 有 `speak` 的结果直接播报
- 无 `speak` 的结果作为 tool 回灌给 LLM 继续生成
- skill 失败/timeout 不会卡死本轮
- 配置开关 `llm_tool_call.enabled=false` 时不注册 skill tools
- P8-5 测试全绿无回归

### P8-7 远程归档导入到 pending（兼容 GitHub / 镜像 / archive URL）

目标：兼容已有 skill 包导入。支持本地目录导入与远程归档 URL 导入，远程来源不限 `github.com`。

实际实现：

- `import_from_local()` — 本地目录导入 pending，校验 `xiaoclaw.skill.json`
- `import_from_archive_url()` — 远程归档导入（zip/tar.gz），兼容 GitHub URL、GitHub 镜像 URL、直接 archive URL
- `import_from_github()` — 薄封装，委托 `import_from_archive_url`
- URL 规范化不写死 `github.com`，repo 类 URL 自动追加 `/archive/HEAD.tar.gz`
- 不自动启用
- 没有 `xiaoclaw.skill.json` 的包不能执行
- pending 隔离：discovery 不注册、registry.get() 不返回、runner 拒绝执行、matcher 不命中

### P8-8 pending 启用流程

目标：让用户确认后启用 skill。

验收：

- pending 可启用到 enabled
- 启用时校验 manifest
- 记录 source、security_level、enabled_at
- unknown/GitHub 来源默认 high 或 blocked

### P8-9 low/off 安全等级

目标：补齐安全等级策略。

验收：

- low 支持 timeout/env/stdout 配置
- off 至少能识别并记录风险
- 如开放 off 执行，必须有明确配置和日志

### P8-10 回归与实机验收

目标：确认 P8 不破坏 P7 主链路。

验收：

- 单测全绿
- 后端启动正常
- 普通 LLM 正常
- fast path 正常
- `echo` 语音触发并播报
- `device-status-mock` 语音触发并播报
- `persona-context-mock` 每轮默认运行并影响 LLM context
- Skill 超时/失败不拖死主服务
- 连续多轮对话无状态残留

## 配置建议

`config.yaml` 建议新增：

```yaml
skills:
  enabled: true
  security_level: high
  allow_home_agents: true
  enable_github_import: false
  require_review: true
  paths:
    project_enabled: data/skills/enabled
    project_builtin: skills
    project_agents: .agents/skills
    home_agents: ~/.agents/skills
    pending: data/skills/pending
    disabled: data/skills/disabled
  runner:
    high_timeout: 5
    high_global_timeout: 3
    high_stdout_limit: 65536
    low_timeout: 30
    low_stdout_limit: 262144
    env_allowlist: []
  global_defaults:
    enabled: true
    phase: before_llm
    selected:
      - name: persona-context
        phase: before_llm
        order: 10
        failure_policy: continue
    max_skills_per_turn: 3
    total_timeout: 5
    context_char_limit: 1000
    include_fast_path: false
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

## 最终验收标准

P8 完成标准：

- `~/.agents/skills` 可扫描
- 已有 `SKILL.md` 包可导入到 pending
- 没有 `xiaoclaw.skill.json` 的包不会被误执行
- enabled skill 可被显式语音触发
- global default skill 可配置为每轮默认运行
- 后端可选择哪些 Skill 默认全局运行，哪些 Skill 只按需运行
- high 模式隔离规则全部生效
- Skill 超时/失败不拖死主服务
- 未命中 Skill 仍正常 LLM
- fast path、bind、output limit 不受破坏
- 文档、进度、问题记录同步

## 推荐执行顺序

推荐按以下顺序推进：

```text
P8-0 文档和配置冻结
P8-1 Skill Manifest 与发现
P8-2 Skill Runner high 模式
P8-3 内置样例 Skill
P8-4 全局默认 Skill
P8-5 Skill Matcher
P8-6 Agent Loop 接入
P8-7 远程归档导入到 pending
P8-8 pending 启用流程
P8-9 low/off 安全等级
P8-10 回归与实机验收
```

这个顺序先把资产兼容和执行安全打稳，再接入语音 Agent loop，最后处理导入和安全等级扩展。

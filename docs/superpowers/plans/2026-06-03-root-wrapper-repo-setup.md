# Root Wrapper Repo Setup Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Initialize the workspace root as a lightweight documentation/meta Git repository without absorbing the backend repo or the ESP32 firmware repo.

**Architecture:** The root repository acts as a wrapper repo for shared project documents, planning artifacts, and workspace-level notes. The backend and firmware remain independent child repositories with their own histories, while the root `.gitignore` explicitly excludes those child repos plus bulky backups and generated artifacts.

**Tech Stack:** Git, workspace Markdown documentation, `.gitignore`.

---

### Task 1: Define The Root Repo Boundary

**Files:**
- Create: `.gitignore`
- Inspect: `AGENTS.md`
- Inspect: `START_HERE_项目速览.md`
- Inspect: `CLAUDE.md`

- [ ] **Step 1: Confirm the root repo is a wrapper repo, not a monorepo**

Keep this exact scope:

```text
Track root-level project documentation and planning artifacts
Do not absorb xiaozhi-esp32-server/.git history
Do not absorb xiaoclaw-ghproxycom/.git history
Do not stage backup snapshots, build outputs, or sensitive runtime data
```

- [ ] **Step 2: Create a root `.gitignore` that enforces the boundary**

Use ignore rules in this shape:

```gitignore
.DS_Store
.pytest_cache/
.omx/logs/

xiaozhi-esp32-server/
xiaoclaw-ghproxycom/

backup_sensitive_*/
backup_snapshot_*/
stable_version_assets/
data/
tmp/

**/build/
**/managed_components/
**/releases/
```

Also ignore large workspace-only artifacts such as local zip bundles and old extracted working copies when they are not intended to be versioned by the root repo.

### Task 2: Initialize The Root Git Repository

**Files:**
- Create: `.git/` (via `git init`)
- Modify: `.gitignore`

- [ ] **Step 1: Verify the root is not already a Git repository**

Run:

```bash
git rev-parse --show-toplevel
```

Expected:

```text
fatal: not a git repository (or any parent up to mount point /Volumes)
```

- [ ] **Step 2: Initialize the wrapper repo**

Run:

```bash
git init
```

Expected:

```text
Initialized empty Git repository in /Volumes/软件/opencode/ESP32S3语音陪伴设备开发/.git/
```

- [ ] **Step 3: Confirm the two child repos still resolve to themselves**

Run:

```bash
git rev-parse --show-toplevel
```

Inside:

```text
/Volumes/软件/opencode/ESP32S3语音陪伴设备开发/xiaozhi-esp32-server
/Volumes/软件/opencode/ESP32S3语音陪伴设备开发/xiaoclaw-ghproxycom
```

### Task 3: Validate The New Root Repo And Sync Project Notes

**Files:**
- Modify: `项目开发进度_XiaoClawBrain.md`
- Inspect: `.gitignore`

- [ ] **Step 1: Check the root repo only sees intended files**

Run:

```bash
git status --short
```

Expected:

```text
Shows root docs and config files only
Does not recurse into xiaozhi-esp32-server or xiaoclaw-ghproxycom contents
Does not show backup_snapshot_* or backup_sensitive_* trees
```

- [ ] **Step 2: Record the workflow change in the project progress log**

Add a new top entry covering:

```text
Root wrapper repo initialized
Child backend repo remains independent
Child firmware repo remains independent
Daily workflow is repo-by-repo
GitHub publishing stays repo-by-repo unless a later submodule or monorepo migration is chosen
```

- [ ] **Step 3: Keep the repo uncommitted unless the user explicitly asks for an initial commit**

Do not run:

```bash
git commit
```

This setup task ends with the repository ready for future commits, not with an automatic history decision.

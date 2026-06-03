# XiaoClawBrain Phase 2 Roadmap And Configuration Design

> Scope: restructure all currently documented unfinished items into a unified Phase 2 roadmap, define stable configuration ownership for default persona/voice/skill, and align project documents to the new post-P9 stage.

## Background

Phase 1 is considered complete in the current project documents:

- P0-P9 core loop is complete
- P9 local wake word acceptance is complete
- current firmware and backend runtime have been aligned to the latest local workspace state

The project now needs a clean post-P9 roadmap instead of leaving remaining work split across:

- `P10` candidate upgrades
- "first version explicitly not doing" items
- ad hoc future ideas
- newly requested persistent customization features

This design consolidates all of those items into a single Phase 2 roadmap and defines where long-lived user-facing defaults should live so that routine backend or firmware updates do not wipe them.

## Goal

Create a coherent Phase 2 document set that:

1. lists every currently documented unfinished or deferred item
2. adds the newly requested customization features
3. reorganizes the whole set into Phase 2 `P1/P2/P3...` tasks by importance and difficulty
4. defines stable storage rules for:
   - default global role skill
   - default cloned Baidu voice
   - default agent persona
5. updates all project docs so they share one consistent post-P9 story

## Non-Goals

This pass does not implement new runtime features. It only:

- restructures roadmap and progress documentation
- records recommended configuration locations
- records persistence rules
- records UI alignment as a candidate Phase 2 item, not an immediate implementation

## Source Pools To Consolidate

Phase 2 backlog should be built from three pools:

### Pool A: currently documented unfinished or candidate items

- existing `P10` candidate upgrades
- any explicit "next step", "future", "candidate", or "remaining" items already present in project docs

### Pool B: previously out-of-scope Phase 1 items that may become Phase 2 work

Items explicitly marked "not doing in first version" should not remain invisible. They should move into a Phase 2 candidate pool when still relevant, for example:

- user interruption of AI
- re-listening during TTS playback
- OTA
- multi-device backend/admin
- complex long-term memory
- skill marketplace
- large-scale MCP integration
- backend Cron based reminders/tasks

These remain Phase 2 backlog items, not retroactive Phase 1 defects.

### Pool C: newly requested Phase 2 items

- personal role skill distilled elsewhere and installed as default global skill
- Baidu cloned voice used as device default voice
- explicit location for default agent persona
- guarantee that these settings survive updates
- backend UI page alignment review based on actual current system capabilities

## Phase 2 Roadmap Structure

Phase 2 should be rewritten as a complete roadmap, not a single `P10`.

### P1: Configuration And Persistence Baseline

Priority: highest

Purpose:

- define which settings must survive updates
- define the authoritative storage location for long-lived defaults
- define safe update rules for backend and firmware

Includes:

- persistent config ownership rules
- update SOP for backend and device
- documentation of what survives normal update vs destructive erase

### P2: Persona, Voice, And Default Skill Customization

Priority: very high

Purpose:

- make user-facing defaults intentional and maintainable

Includes:

- default global role skill workflow
- default cloned Baidu voice workflow
- default agent persona workflow
- recommended storage and activation paths

### P3: Session Mode And Memory Strategy Upgrade

Priority: high

Purpose:

- absorb the old `P10` candidate into Phase 2 proper

Includes:

- default single-turn plus optional continuous conversation
- memory save/query strategy for long-lived sessions

### P4: Interaction Experience Enhancements

Priority: medium-high

Purpose:

- collect previously deferred interaction improvements

Includes:

- user interruption of AI
- listening again during TTS playback
- other conversation experience upgrades

### P5: System And Platform Enhancements

Priority: medium

Purpose:

- collect heavier infrastructure features that were intentionally excluded from Phase 1

Includes:

- OTA
- multi-device backend/admin
- backend Cron/task execution
- complex long-term memory
- broader external integrations

### P6: Backend UI Alignment Candidate

Priority: medium-low

Purpose:

- review whether current backend UI pages still match actual system capability and configuration ownership

Important constraint:

- this is recorded as a candidate Phase 2 workstream
- it should not outrank persistence/customization basics

## Configuration Ownership Rules

This is the key stabilization layer for Phase 2.

### Rule 1: long-lived user defaults must not depend only on repository default files

Repository defaults are allowed as bootstrap values, but not as the only home for:

- user-selected persona
- user-selected default voice
- user-installed default global skill

### Rule 2: persistent backend customization should live in mounted data or database

Preferred persistent homes:

- mounted backend data directory
- MySQL database
- optionally mounted local config file under `data/`

Avoid treating image-bundled default files as the only authoritative home for long-lived user configuration.

### Rule 3: normal update must mean rebuild/redeploy without deleting mounted state

Backend normal update:

- allowed: rebuild image and recreate container
- forbidden: deleting `data/` or `mysql/data/` as part of normal update

Firmware normal update:

- allowed: flash application image
- forbidden for normal update: full erase, explicit `nvs` wipe, explicit `fatfs` wipe, partition moves without migration planning

## Recommended Storage Locations

### A. Default global role skill

Recommended file location:

- `xiaozhi-esp32-server/main/xiaozhi-server/data/skills/enabled/<skill-name>/SKILL.md`

Reason:

- `data/skills/enabled` is inside the project data area intended for project-level enabled skills
- it survives backend image rebuilds when `data/` is preserved
- it avoids coupling long-lived user skill content to repository-bundled sample skills

Recommended activation location:

- `skills.global_defaults.selected` in backend config

### B. Default cloned Baidu voice

Recommended ownership:

- primary runtime default should be represented through manager/database-backed agent or template configuration
- local `data/.config.yaml` should serve as local fallback/bootstrap, not the only home

Reason:

- current system already has database entities for TTS model and voice relationships
- user-selected default voice should survive backend image rebuilds through database persistence

### C. Default agent persona

Recommended ownership:

- primary runtime default persona should be represented through agent/template `system_prompt`
- local file-based prompt template should be retained as fallback or bootstrap

Recommended file fallback:

- `data/prompts/default_persona.txt`

Recommended config pointer:

- `prompt_template` should point to the persistent file when local-file mode is desired

Reason:

- repository prompt files can be updated with code
- user-defined long-lived persona should not be stored only in a code-tracked default file

## Update Safety Rules To Record In Project Docs

The following should be stated explicitly after this redesign:

### Backend

Normal backend update must preserve:

- `xiaozhi-esp32-server/main/xiaozhi-server/data/`
- `xiaozhi-esp32-server/main/xiaozhi-server/mysql/data/`

Therefore the following survive normal update when stored correctly:

- manager-side model configuration
- agent/persona configuration
- TTS voice configuration
- local mounted config files
- enabled data skills

### Device

Normal firmware flash should not erase:

- `nvs`
- `fatfs`

Destructive actions that can wipe device-side persistent state must be called out explicitly:

- full chip erase
- explicit partition wipe
- partition table changes without migration

## UI Alignment Positioning

Backend UI alignment should be recorded in Phase 2, but as a lower-priority candidate.

Scope:

- compare what current pages expose vs what backend/runtime actually uses
- identify stale, misleading, missing, or split configuration pages
- decide later whether changes are doc-only, backend-only, or frontend+backend

It should not block:

- persistence rules
- default skill placement
- default voice placement
- default persona placement

## Documents To Update

### `START_HERE_项目速览.md`

Update to:

- clearly state Phase 1 is complete
- introduce Phase 2 as the new active stage
- summarize Phase 2 focus areas
- summarize persistence rules for user defaults and update safety

### `项目开发路线图_XiaoClawBrain.md`

Update to:

- replace the old scattered post-P9 wording with a full Phase 2 roadmap
- convert old P10 and future candidates into Phase 2 `P1/P2/P3...`
- include newly requested customization and persistence items

### `项目开发进度_XiaoClawBrain.md`

Update to:

- add a new top entry saying Phase 2 roadmap and persistence strategy were reorganized
- state current stage has shifted from Phase 1 completion into Phase 2 planning/alignment
- record next actionable Phase 2 starting point

### `CLAUDE.md`

Update if needed to add a guardrail:

- long-lived user customization should prefer `data/` and database persistence over repository default files

## Open Decisions Already Resolved In This Design

- UI page alignment belongs to Phase 2 candidate backlog, not immediate top priority
- Phase 2 should be a complete roadmap, not a short "near-term only" list
- old "explicitly not in Phase 1" items should be visible in Phase 2 backlog instead of disappearing

## Acceptance Criteria For This Documentation Pass

This documentation update is complete when:

1. all related project documents share the same statement that Phase 1 is complete
2. Phase 2 is the new active planning stage
3. the entire remaining backlog is reorganized into a complete Phase 2 roadmap
4. default skill, default voice, and default persona locations are explicitly documented
5. update-safe persistence rules are clearly documented
6. backend UI alignment is included as a candidate Phase 2 workstream

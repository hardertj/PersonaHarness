# Personal AI Harness — Phased Implementation Plan

This plan maps directly onto Modules 1–7 of the [Curriculum & Course Plan](./curriculum_and_course_plan.md) and implements the architecture in the [System Design Document](./design_document.md).

> [!IMPORTANT]
> **Teaching & Code-Review Rule:** We will **never** dump a big batch of code without explaining it first. Every phase builds one small, testable piece, followed by a line-by-line review and a live check in Antigravity 2.0 before moving on.

---

## Phase 0 — Module 1: Foundations Lesson (No Code Yet)

* **Objective:** Build solid mental models of context windows, compaction, and Antigravity's customization layers before creating any files.
* **Activities:**
  1. Walk through tokens, context windows ("RAM"), compaction (`/compact`), and external memory ("disk") with concrete examples.
  2. Map out how Antigravity 2.0 discovers `.agents/`, `GEMINI.md`, `SKILL.md`, `hooks.json`, and `mcp_config.json`.
  3. Quick 3-question mini-quiz to lock in the concepts before we start building.
* **Deliverable:** Conceptual readiness (zero files written yet).

---

## Phase 1 — Module 2: Repo Setup, Docs & Persona v0.1 (`GEMINI.md`)

* **Objective:** Initialize the clean Git repository at `d:/repos/PersonaHarness`, save all generated course/design/session documents into `documents/`, and create your working persona (`GEMINI.md`).
* **Planned Files:**
  * `[NEW]` `d:/repos/PersonaHarness/.gitignore` — Excludes `.venv/`, `harness/logs/`, `memory/brain.db`, and `__pycache__/`.
  * `[NEW]` `d:/repos/PersonaHarness/documents/` — Stores copies of `curriculum_and_course_plan.md`, `design_document.md`, `implementation_plan.md`, and `conversations/` (starting with this founding session).
  * `[NEW]` `d:/repos/PersonaHarness/GEMINI.md` — Defines persona name (unnamed initially), voice, intellectual honesty, mentoring style, and formatting preferences.
* **Review & Verification:**
  1. Read through every paragraph of `GEMINI.md` and `.gitignore` together and tune any details.
  2. Open `d:/repos/PersonaHarness` as a Project in Antigravity 2.0, start a fresh conversation, and test with 2–3 thinking-partner prompts (e.g., asking for honest pushback on an idea).

---

## Phase 2 — Module 3: On-Demand Skills v0.2 (`SKILL.md`)

* **Objective:** Add reusable workflows using progressive disclosure so they don't bloat every prompt.
* **Planned Files:**
  * `[NEW]` `d:/repos/PersonaHarness/.agents/skills/session-wrapup/SKILL.md`
  * `[NEW]` `d:/repos/PersonaHarness/.agents/skills/socratic-deep-dive/SKILL.md`
* **Review & Verification:**
  1. Inspect YAML frontmatter (`name`, `description`) and see how Antigravity decides when to load a skill.
  2. Test triggering `socratic-deep-dive` on a real question you want to explore, then trigger `session-wrapup` at the end.

---

## Phase 3 — Module 4: Human-Readable Memory Vault v0.5

* **Objective:** Give your persona inspectable, cross-conversation memory using plain Markdown files.
* **Planned Files:**
  * `[NEW]` `d:/repos/PersonaHarness/memory/profile.md` — Initial seed profile drafted together with you.
  * `[NEW]` `d:/repos/PersonaHarness/memory/active_context.md` — Current learning goals and open threads.
  * `[NEW]` `d:/repos/PersonaHarness/memory/episodes/README.md` — Naming convention & template for dated summaries.
  * `[MODIFY]` `d:/repos/PersonaHarness/GEMINI.md` — Add concise memory-hygiene rules (when to read/update vault files).
  * `[MODIFY]` `d:/repos/PersonaHarness/.agents/skills/session-wrapup/SKILL.md` — Point wrap-up directly at `memory/active_context.md` and `memory/episodes/`.
* **Review & Verification:**
  1. Have a short real conversation, run a wrap-up, and inspect the updated Markdown files yourself.
  2. Start a **brand-new conversation** in Antigravity 2.0 (in `d:/repos/PersonaHarness`) and confirm the persona accurately picks up where you left off.

---

## Phase 4 — Module 5: Automated Context Stitching v1.0 (`hooks.json`)

* **Objective:** Automatically inject your memory snapshot at the start of every fresh chat and log session metadata at turn end—using pure Python standard library scripts.
* **Pre-Flight Check:**
  * Confirm `python --version` (or `py --version`) works in PowerShell/CMD on your Windows machine.
* **Planned Files:**
  * `[NEW]` `d:/repos/PersonaHarness/harness/hooks/session_start.py` — Reads `stdin` JSON, compiles capped snapshot from `memory/`, writes `injectSteps` JSON to `stdout`.
  * `[NEW]` `d:/repos/PersonaHarness/harness/hooks/session_stop.py` — Reads `stdin` JSON (`transcriptPath`, `terminationReason`), appends lightweight metadata to `memory/episodes/_session_index.jsonl`, writes `{}` to `stdout`.
  * `[NEW]` `d:/repos/PersonaHarness/harness/hooks/test_hooks.py` — Feeds sample Antigravity JSON payloads into both scripts and validates output schema and runtime latency.
  * `[NEW]` `d:/repos/PersonaHarness/.agents/hooks.json` — Registers `SessionStart` and `Stop` hooks (using Windows-safe relative commands).
* **Code Review & Verification Gate:**
  1. Line-by-line Python walkthrough of `session_start.py` and `session_stop.py` (JSON I/O, `pathlib`, fail-open logging).
  2. Run `python harness/hooks/test_hooks.py` locally and verify both scripts pass in `< 100 ms`.
  3. Open a fresh conversation in Antigravity 2.0 and verify the startup context is injected automatically without asking.

---

## Phase 5 — Module 6: Custom Memory MCP Server v1.5

* **Objective:** Build and wire a real, queryable local Memory MCP Server backed by SQLite FTS5.
* **Pre-Flight Check:**
  * Create a workspace Python virtual environment (`d:/repos/PersonaHarness/.venv`) and install the official Python `mcp` SDK.
* **Planned Files:**
  * `[NEW]` `d:/repos/PersonaHarness/harness/mcp_memory/db.py` — SQLite + FTS5 schema initialization and 5 clean functions (`remember`, `recall`, `list_recent`, `update_memory`, `forget`).
  * `[NEW]` `d:/repos/PersonaHarness/harness/mcp_memory/server.py` — Stdio MCP server wrapping `db.py` with clear tool docstrings and type annotations.
  * `[NEW]` `d:/repos/PersonaHarness/harness/mcp_memory/test_memory.py` — Unit tests verifying insert, BM25 keyword search, update, and soft-archive behavior.
  * `[NEW]` `d:/repos/PersonaHarness/.agents/plugins/personal-harness/plugin.json` — Minimal workspace plugin manifest.
  * `[NEW]` `d:/repos/PersonaHarness/.agents/plugins/personal-harness/mcp_config.json` — Launches `.venv/Scripts/python.exe` running `server.py` over Stdio.
  * `[MODIFY]` `d:/repos/PersonaHarness/GEMINI.md` — Update memory protocol so the persona uses `recall` / `remember` tools alongside the Markdown vault.
* **Code Review & Verification Gate:**
  1. Line-by-line Python walkthrough of `db.py` (SQL queries, FTS5 triggers, parameterized queries) and `server.py` (MCP decorators & Stdio loop).
  2. Run `test_memory.py` and verify all assertions pass.
  3. In Antigravity 2.0, inspect **Additional Options (...) > MCP Servers** to confirm the `memory` server is connected and its 5 tools are discovered.
  4. Save a memory in Chat A, open a brand-new Chat B, and verify the persona retrieves it via `recall`.

---

## Phase 6 — Module 7: Hardening, Subagents & Portable Plugin v2.0

* **Objective:** Consolidate everything into a clean, toggleable Antigravity Plugin and explore next-level enhancements.
* **Planned Work:**
  1. Consolidate rules (`rules/AGENTS.md`), skills (`skills/`), hooks (`hooks.json` at plugin root), and `mcp_config.json` cleanly inside the plugin structure (using `PLUGIN_ROOT` / `PLUGIN_DATA` where appropriate).
  2. Teach and test **subagent context isolation** (e.g., offloading web/file research so your main thinking-partner context stays clean).
  3. Review optional future upgrades together:
     * Adding semantic/embedding search alongside FTS5.
     * Setting up recurring weekly reflection check-ins via `/schedule`.
     * Exporting or installing the plugin globally under `~/.gemini/config/` so your persona follows you into other workspaces.
  4. Final end-to-end multi-session test across fresh threads.

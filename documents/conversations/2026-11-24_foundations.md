# Founding Session Log — Personal AI Harness (2026-11-18 to 2026-11-24)

## Session Purpose
Explore whether a continuous, person-like AI harness can be built natively inside Google Antigravity, understand how context windows and memory work, design a beginner-friendly curriculum and architecture, and kick off the repository at `d:/repos/PersonaHarness`.

---

## Key Concepts Covered

### 1. Context Windows: Switching vs. Stitching
- **Context Window = Working RAM:** Each conversation re-sends system instructions + chat history on every turn; opening a new conversation starts with clean RAM.
- **Switching Context Windows (Native):**
  - Separate conversations in Antigravity 2.0 sidebar (or `/resume`, `/switch`, `/fork` in Antigravity CLI `agy`).
  - Subagents run in isolated context windows so heavy searches/browsing don't pollute the main conversational thread.
  - `/btw` runs an ephemeral side query without adding clutter to permanent thread history.
- **Stitching Context Windows Together:**
  - `/compact` compresses older history in-place (helpful for long sessions, though repeated summaries lose fine detail).
  - `@` mentions in Antigravity 2.0 attach previous conversations, files, or artifacts into a fresh thread.
  - Lifecycle hooks (`hooks.json`) automatically inject startup context (`SessionStart`) and log turn metadata (`Stop`).
  - External memory via Markdown files and a local **Memory MCP Server** provides long-term storage ("disk") shared across every window.

### 2. What Is a Memory MCP Server?
- A local (Stdio) or remote (Streamable HTTP) program registered in `mcp_config.json` that exposes read/write tools (e.g., `remember`, `recall`, `list_recent`, `update_memory`, `forget`) backed by persistent storage (JSONL, Markdown, or SQLite).
- Separates short-term working RAM (active chat) from shared long-term disk memory.

### 3. Orchestrator (Neon) vs. Gemini 4 Neon
- **Gemini 4 Neon:** A single model handling planning, reasoning, edits, and responses on every turn—ideal for nuanced conversation and persona continuity.
- **Orchestrator (Neon):** A dual-model mode pairing **Gemini 4 Neon** (planner & reviewer) with **Gemini 4 Hydrogen** (fast executor) in the same session, with automatic handbacks on errors.

### 4. Module 1 Foundations — Antigravity's 5 Building Blocks
- **Rules (`GEMINI.md` / `AGENTS.md`):** Always-on identity and boundaries (`24 KB`/file, `20,000`-token aggregate budget).
- **Skills (`.agents/skills/<name>/SKILL.md`):** On-demand workflows using progressive disclosure (only `name` & `description` load upfront).
- **Hooks (`.agents/hooks.json`):** Synchronous lifecycle scripts (`SessionStart`, `PreInvocation`, `Stop`, etc.) for guaranteed plumbing.
- **MCP Servers (`mcp_config.json`):** Active tools exposed via a plugin or global `~/.gemini/config/mcp_config.json`.
- **Plugins (`plugins/<name>/plugin.json`):** Portable bundles packaging Rules + Skills + Hooks + MCP config.

**Module 1 Check-In Score:** 3/3 — Rules vs. Skills vs. Hooks mental models verified.

---

## Architecture & Course Decisions Made

| Decision | Choice |
| :--- | :--- |
| **Language & Pacing** | Python — beginner-friendly, readable, with line-by-line walkthroughs |
| **Primary Surface** | Antigravity 2.0 Desktop App |
| **Persona Role** | Thinking partner, sounding board & learning mentor |
| **Persona Name** | Unnamed for now (will emerge naturally in conversation) |
| **Voice & Pushback** | Adaptive blend of warm mentor, Socratic coach, and direct sparring partner; mix of guiding questions and direct callouts |
| **Repository Location** | `d:/repos/PersonaHarness` (dedicated Git repo for private GitHub backup) |
| **Docs & Privacy** | `documents/` stores all course, design, plan, and session logs; personal Markdown files tracked in private repo while `.venv/`, `harness/logs/`, and `memory/brain.db` are `.gitignored` |

---

## Next Step
- Complete Module 2 review of `GEMINI.md` and `.gitignore`, initialize Git in `d:/repos/PersonaHarness`, and test Persona v0.1 in a fresh Antigravity 2.0 chat before advancing to **Module 3 (On-Demand Skills)**.

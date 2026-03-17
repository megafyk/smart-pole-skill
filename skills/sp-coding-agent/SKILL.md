---
name: sp-coding-agent
description: Use when a coding task is vague or under-specified. Extracts missing context across 9 code-native categories before the agent executes file changes. Designed for OpenAI Codex, Anthropic Claude Code, and Google Gemini Code Assist.
---

# SMART POLE Coding Agent Skill

This skill implements the **SMART POLE** framework adapted for **AI Coding Agents**. Unlike the chat versions (Instructor/Enforcer), this skill operates on **codebases** — scanning project files, auto-extracting context, and ensuring the agent has enough information before writing a single line of code.

---

## How to Load This Skill

1. **Set system prompt**: Load `references/system-prompt.md` as the agent's system prompt (e.g., paste into `AGENTS.md`, `.claude/system_prompt.md`, or the agent's system role configuration).
2. **Provide reference files**: Make `references/logic.md`, `references/overlap-rules.md`, and `references/coding-agent-categories.md` available in the agent's context or knowledge base.
3. **Invoke**: Give the agent a vague or specific coding task. The agent will ORIENT (scan the codebase), CLASSIFY the task type, EXTRACT SP-categories, and check execution gates before touching any file.

---

## Reference Files

| File | Purpose |
|------|---------|
| `references/system-prompt.md` | 🔴 **Required** — Full Coding Agent system prompt (v4.0). Load as the agent's system instructions. |
| `references/logic.md` | Framework logic: category definitions, weighted scoring, task-type classification, generic-to-coding task mapping. |
| `references/overlap-rules.md` | Atom overlap rules, conflict detection, Functional Gravity principle, and the One Atom One Slot rule. |
| `references/coding-agent-categories.md` | Code-native sub-dimensions for all 9 SP-categories with auto-detection sources and hard-stop gate definitions. |

---

## When to Use This Skill

- A user gives a **vague coding task** ("fix the login bug", "add pagination", "refactor this module")
- Before an agent starts **multi-file code changes**
- When a task touches **unfamiliar parts** of a codebase
- **Migration or refactoring** tasks where scope control is critical

---

## The 7-Step Agentic Workflow

| Step | Name | Action |
|------|------|--------|
| 1 | **ORIENT** | Scan project root: `README.md`, `AGENTS.md`, `package.json`, `Dockerfile`, etc. |
| 2 | **CLASSIFY** | Determine task type: Bug Fix / Feature / Refactor / Migration / Infra |
| 3 | **EXTRACT** | Map request to 9 code-native SP-categories |
| 4 | **DETECT FLAWS** | Identify missing context, overlaps, and conflicts; ask user if critical |
| 5 | **PLAN** | Create implementation plan with file-level scope |
| 6 | **EXECUTE** | Apply file changes within approved scope |
| 7 | **VERIFY** | Run tests, lint, type-check; self-heal on failures (max 3 attempts) |

---

## The 9 Code-Native Categories

| Abbrev | Category | Code Meaning | Priority |
|--------|----------|-------------|----------|
| **S** | Style | Code standards, linting, architecture pattern | 🟢 Auto-detect |
| **M** | Mastery | Reviewer expertise, explanation depth | 🟡 Contextualizer |
| **A** | Aim | Definition of Done, acceptance criteria | 🔴 **CORE** |
| **R** | Resource | Allowed/forbidden deps, API access, compute limits | 🟡 Contextualizer |
| **T** | Time | Hotfix vs refactor, urgency level | 🟢 Accelerator |
| **P** | People | Team conventions, reviewer preferences | 🟡 Contextualizer |
| **O** | Outline | Authorized file scope, what NOT to touch | 🔴 **CORE** |
| **L** | Locale | Runtime ecosystem, language/framework version | 🟡/🔴 **CONDITIONAL** |
| **E** | Example | Reference code patterns, anti-patterns | 🟡/🟢 **CONDITIONAL** |

---

## Auto-Extraction Sources

| Source File | SP-Categories Extracted |
|-------------|------------------------|
| `.eslintrc` / `ruff.toml` | **S** (Style) |
| `package.json` / `pyproject.toml` | **R** (Resource), **L** (Locale) |
| `Dockerfile` / `docker-compose.yml` | **L** (Locale), **R** (Resource) |
| `AGENTS.md` / `SKILL.md` | **P** (People), **S** (Style) |
| CI/CD configs (`.github/workflows/`) | **R** (Resource), **A** (Aim quality gates) |

---

## Execution Gates (Hard Stops)

Do **not** execute code changes until all 5 gates pass:

1. **Gate A (Aim)**: At least one testable acceptance criterion exists.
2. **Gate O (Outline)**: Authorized scope is explicit (or defaults to minimal); forbidden scope is respected.
3. **Gate Conflict**: All `SP-conflict` items resolved by user decision.
4. **Gate Overlap**: `One Atom, One Slot` enforced — no double-counted atoms. (see `references/overlap-rules.md`)
5. **Gate Score**: Weighted readiness score ≥ 67% of applicable max. (see `references/logic.md`)

If any gate fails → Stop → Ask targeted clarification → Re-plan after user confirmation.

---

## Task Type → Coding Task Mapping

| Generic Type (`references/logic.md`) | Coding Task Type(s) | Agent Behavior |
|--------------------------------------|---------------------|----------------|
| Deterministic | Bug Fix, Refactor | Minimal change + regression test |
| Generative | Feature | Plan → implement → test |
| Advisory | Refactor, Migration, Infra | Convert advice to measurable acceptance criteria first |
| Discovery | Feature spike, Migration assessment | Explore minimally; keep changes reversible |
| Compliance | Infra, Migration, Bug Fix | Legal/security constraints = mandatory acceptance criteria |

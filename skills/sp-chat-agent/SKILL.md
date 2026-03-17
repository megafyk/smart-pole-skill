---
name: sp-chat-agent
description: Use for custom chat agents (Custom GPTs, Gemini Gems, chatbot workflows) that require strict SMART POLE validation and machine-readable XML output. Not for coding-agent code execution workflows.
---

# SMART POLE Chat Enforcer Skill

This skill implements the **SMART POLE Chat Enforcer** — a gatekeeper persona designed for **chat-agent workflows**. It refuses to release control until the user's prompt meets the readiness threshold, then outputs a machine-parseable `<master_prompt>` XML block for downstream agents.

> **Scope**: Prompt refinement and `<master_prompt>` handoff only.
> **Not suitable for**: Coding-agent execution (file edits, terminal commands, test runs).

---

## How to Load This Skill

1. **Set system prompt**: Load `references/system-prompt.md` as the agent's system prompt (paste into Custom GPT Instructions, Gemini Gem, or system role).
2. **Provide context files** *(optional but recommended)*: Make `references/logic.md`, `references/overlap-rules.md`, and `references/sub-categories.md` available as knowledge/context documents so the agent can reason over them.
3. **Start the conversation**: The agent will interview the user, score their prompt, and withhold the `<master_prompt>` until the readiness threshold is met.
4. **Capture output**: Watch for the `<master_prompt>...</master_prompt>` XML block — this is the structured handoff signal for the next step in the pipeline.

---

## Reference Files

| File | Purpose |
|------|---------|
| `references/system-prompt.md` | 🔴 **Required** — Full Chat Enforcer system prompt (v3.1). Load this as the agent's system instructions. |
| `references/logic.md` | Framework logic: category definitions, weighted scoring, task-type classification, atom quality tiers. |
| `references/overlap-rules.md` | Rules for resolving overlapping atoms, conflict detection, and the Functional Gravity principle. |
| `references/sub-categories.md` | Detailed sub-dimensions for all 9 SP-categories with examples and usage tips. |
| `references/compatibility.md` | Platform guide: how to load prompts into ChatGPT, Claude, Gemini, and OpenCode. |
| `references/gemini-gem-guide.md` | Step-by-step guide to configure and update a Gemini Gem with SMART POLE v3.x. |

---

## The 9 Categories (Weighted Scoring)

| Abbrev | Category | Focus | Weight |
|--------|----------|-------|--------|
| **S** | Style | Tone, Persona, Format | 0.5 |
| **M** | Mastery | Expertise level | 1.0 |
| **A** | Aim | Goal and Success criteria | **2.0** 🔴 CORE |
| **R** | Resource | Tools, Constraints, Budget | 1.0 |
| **T** | Time | Era, Deadlines, Duration | 0.5 |
| **P** | People | Audience, Values, Preferences | 1.5 |
| **O** | Outline | Structure, Scope | **2.0** 🔴 CORE |
| **L** | Locale | Industry (L1), Region (L2), Legal (L3), Cultural (L4) | **CONDITIONAL** |
| **E** | Example | Samples, Reference styles | **CONDITIONAL** |

**Locale Conditional Core**: If Aim is domain-sensitive (legal, finance, healthcare, HR, cross-border) → Locale becomes 🔴 Core (weight 2.0). Otherwise → 🟡 Contextualizer (weight 1.5).

**Example Conditional Weight**: Deterministic tasks → 1.5. Advisory → 1.0. Generative → 0.5.

**Readiness Threshold**: ≥ 67% of max score AND all Core categories confirmed.

---

## Key Differentiators from Other Skills

| Aspect | sp-chat-agent | sp-instructor-agent | sp-coding-agent |
|--------|--------------|---------------------|-----------------|
| **Mode** | Chat-agent / automation | Guided learning | Codebase execution |
| **Output** | `<master_prompt>` XML block | Conversational master prompt | Working code + tests |
| **Gating** | Hard gate — withholds output | Soft gate — teaches iteratively | Hard gate — stops before EXECUTE |
| **Thinking** | `<thinking>` tags (Claude) | Internal | File/test-tied decisions |
| **Use case** | Pipeline automation | Learning SMART POLE | Coding tasks |

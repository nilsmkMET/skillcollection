---
name: agentic-coding-optimizer
description: Guides agentic coding workflows, token optimization, and context window management across AI coding harnesses like OpenCode, Claude Code, and Copilot. Use when configuring agent environments, optimizing token budget and cache, drafting AGENTS.md, or setting up multi-agent engineering workflows.
---

# Agentic Coding & Token Optimizer

Guide and optimize purposeful, human-controlled agentic coding workflows while maximizing token budget efficiency and context health.

## Core Philosophy: Agentic Coding vs. "Vibe Coding"

- **Human in Control:** Never blindly accept unread diffs or surrender engineering oversight. You define goals, constraints, and architecture; agents execute plans, tool calls, and tests.
- **Intentional Delegation:** Treat agents as autonomous engineering assistants operating under strict supervision, boundaries, and validation loops.

## When to Use

Apply this skill when:
- Designing or auditing AI coding harnesses (OpenCode, Claude Code, GitHub Copilot).
- Optimizing context windows, reducing token consumption, or debugging cache misses.
- Structuring repositories for agent compatibility (`AGENTS.md`, design docs, task commands).
- Setting up multi-agent team roles (Lead, Scout, Builder, Reviewer).
- Establishing automated test/learn feedback loops for coding sessions.

---

## 1. Context Window Budget & Management

Keep active context usage **below 70–80%** to prevent reasoning degradation and hallucination.

### Context Anatomy & Optimization

| Layer | Impact | Best Practice |
|---|---|---|
| **System Prompt** | Fixed overhead | Choose lean harnesses (e.g., OpenCode) over bulky IDE prompt injections. |
| **Tools & MCPs** | Medium to High | Avoid monolithic MCP servers when a native CLI exists (e.g., `gh`, `rg`, `himalaya`). |
| **Skills Index** | Low | Keep active skills lean (typically 8–10 max). Use progressive disclosure. |
| **Project Rules** | Low to Medium | Consolidate instructions in a concise `AGENTS.md` at repo root. |
| **Conversation & Diffs** | High growth | Request concise unified diffs; suppress celebratory summaries. |

### Context Strategies
1. **Compaction Caution:** Compacting (`/compact`) triggers lossy summarization. Compact only at logical task milestones rather than relying on automatic triggers.
2. **Progressive Disclosure:** Expose high-level indices or tables of contents first. Load deep file contents or documentation only when explicitly queried.
3. **Externalize State:** Store long backlogs, issue trackers, and large schema dumps in external files or GitHub Issues rather than loading them into working memory.
4. **Never Expand Raw Data:** Avoid dumping raw logs, database dumps, or raw binary fields into context. Always filter or extract metadata before passing output to the agent.

---

## 2. Token Cost & Cache Optimization

Output generation and internal thinking tokens are roughly 3–5x more expensive than input tokens. Cache hits reduce input costs by up to 80–90%.

### Caching Rules
- **Pin Models Per Task:** Avoid switching models mid-session. Switching moves execution to a different GPU/server node, invalidating cached context.
- **Mind Cache Expirations:** Most provider prompt caches (e.g., Anthropic) have short inactivity timeouts (~5 minutes). Plan prompts to maintain cache warmth.
- **Minimize Tool Output:** Wrap tool outputs to return concise responses. Mute verbose build logs or success banners; surface only failures and errors.
- **Deterministic Operations:** Route deterministic operations (formatting, linting, building) to CLI tools (`prettier`, `ruff`, `make`) instead of paying LLM tokens to reformat text.

---

## 3. Repository Architecture for Agents

Structure the repository so agents orient quickly without scanning hundreds of files:

```text
├── AGENTS.md        # Normative rules, hard limits, testing standards
├── Makefile         # Standardized, predictable verbs (build, test, lint)
├── README.md        # Purpose, architecture summary, quickstart
├── docs/            # Module designs, API specs, schemas
└── tests/           # Executable verification test suite
```

### Writing Effective `AGENTS.md`
- **Use Normative Language:** Use precise directives: `ALWAYS`, `NEVER`, `DO NOT TOUCH`. Avoid ambiguous phrases like `Try to`.
- **Define Danger Zones:** Explicitly forbid touching generated files, build artifacts, migrations, or sensitive production configs.
- **Enforce TDD:** Mandate writing reproducing or unit tests before code changes to establish immediate self-correction signals.
- **Keep It Concise:** Modern frontier models require clear boundaries, not lengthy explanations of basic programming paradigms.

---

## 4. The Virtuous Development Loop

Implement a 4-stage self-correcting development cycle:

1. **Plan:** Draft steps, identify affected files, and specify edge cases before editing.
2. **Implement:** Apply targeted, minimal edits (diff-based).
3. **Test:** Run automated verification commands via CLI immediately.
4. **Learn & Record:** When a mistake occurs, record the root cause into a project `lessons.md` or append to `AGENTS.md` so future sessions inherit persistent memory.

---

## 5. Multi-Agent Team Topologies

For non-trivial projects, delegate responsibilities across specialized roles:

- **Lead (Frontier / Reasoning):** Plans architecture, delegates tasks, and interfaces with the human operator.
- **Scout (Lightweight / Fast):** Explores repository files, navigates docs, and reports summaries to the Lead without cluttering main context.
- **Builder (Mid-tier / Coding):** Generates implementations and targeted diffs against the established plan.
- **Reviewer (Cross-Model / Critical):** Reviews changes against standards. Use a different model family than the Builder (e.g., cross-reviewing between Claude and GPT) and frame the code as model-generated to avoid sycophantic approval.

---

## Gotchas & Safety Rules

- **Never Hand Over Credentials:** Keep credentials, API keys, and auth sessions on the host machine. Never pass raw passwords or authorization tokens into agent prompt contexts.
- **Guarded Web Navigation:** If granting browser or web access, execute manual authentication yourself before delegating navigation tasks.
- **Prompt Injection Defense:** Watch out when agents ingest untrusted web pages, issues, or documents that might contain prompt injection instructions.
- **Early Steer over Late Rewrites:** Monitor agent thoughts and actions. Interrupt immediately (`Ctrl+C` or prompt redirect) when the agent deviates down a wrong path—reversing deep errors burns exponential tokens.

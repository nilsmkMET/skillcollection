---
name: agentic-coding-optimizer
description: Guides agentic coding workflows, token optimization, and context window management across AI coding harnesses like OpenCode, Claude Code, and Copilot. Use when configuring agent environments, optimizing token budget and cache, drafting AGENTS.md, or setting up multi-agent engineering workflows.
---

# Agentic Coding & Token Optimizer

Guide and optimize purposeful, human-controlled agentic coding workflows while maximizing token budget efficiency, context health, and operational safety.

## Core Philosophy: Agentic Coding vs. "Vibe Coding"

- **"Vibe Coding" (Avoid for Production):** Describing what you want, blindly accepting model outputs, and barely reading the code. Adequate only for throwaway prototypes; introduces catastrophic operational and maintainability risks in production codebases.
- **Agentic Coding:** Employing an AI agent inside a structured harness to plan, edit, run tools, and execute automated test loops—while **the human engineer sets goals, defines constraints, steers execution, and conducts final reviews.**
- **Human in Control:** Never blindly accept unread diffs or surrender engineering oversight. Treat tokens as a finite engineering budget and orchestrate intelligent sub-systems within predictable boundaries.

## When to Use

Apply this skill when:
- Designing, selecting, or auditing AI coding harnesses (OpenCode, Claude Code, GitHub Copilot).
- Optimizing context windows, reducing token consumption, or debugging cache misses.
- Structuring repositories for agent compatibility (`AGENTS.md`, `lessons.md`, design docs, task commands).
- Setting up multi-agent team roles (Lead, Scout, Builder, Reviewer).
- Establishing automated test/learn feedback loops and sandboxing controls for coding sessions.

---

## 1. Core Architecture & Nomenclature

Standardize the anatomy of agentic frameworks to reason clearly about where tokens and permissions live:

```text
+-------------------------------------------------------------------+
| Harness (Execution Environment: Permissions, Terminal, Sandbox)   |
|                                                                   |
|   +-------------------+       +-------------------------------+   |
|   | Tools & MCPs      | <---> | Model (LLM Engine)            |   |
|   | (CLI, Git, Files) |       | (System Prompt + Context)     |   |
|   +-------------------+       +-------------------------------+   |
|             ^                                   ^                 |
|             |                                   |                 |
|   +-------------------+       +-------------------------------+   |
|   | Commands (Macros) |       | Skills (Packaged Rules)       |   |
|   +-------------------+       +-------------------------------+   |
+-------------------------------------------------------------------+
```

- **Model (The Engine):** Foundational LLM (cloud-hosted or local) performing raw inference (`text-in -> text-out`).
- **Harness (The Environment):** Client layer encapsulating the model; manages security policies, system prompts, disk access, tool invocations, and workspace boundaries.
- **Agent:** A model paired with structured operating instructions (e.g., *"Review code for security vulnerabilities; do not edit directly"*).
- **Tools:** Explicit utilities exposed by the harness (`ripgrep`, bash commands, `gh` CLI).
- **Skills:** Modular, packaged domain instructions that the model autonomously chooses to consult when relevant.
- **Commands:** Deterministic, user-triggered prompt macros that force specific sequential behaviors.
- **Model Context Protocol (MCP):** Open standard for connecting models to local/remote service backends, tools, and data stores.

---

## 2. Harness Selection & Dual-Pane Setup

Optimize cost and control by choosing an open, configurable CLI harness (e.g., OpenCode v2) over opaque IDE extensions or bloated commercial wrappers:

1. **Transparent Reasoning:** Displays intermediate thought processes (`<thinking>` blocks), letting you detect misinterpretations early and steer before tokens are wasted on invalid implementations.
2. **Lean System Prompts:** Smaller baseline system prompt footprint than heavier commercial CLIs (e.g., Claude Code), permanently reclaiming context tokens on every turn.
3. **Mid-Flight Steering:** Send asynchronous steer prompts while sub-agents are actively working to cut off unproductive branches.
4. **Model Agnostic & Anti-Lock-In:** Seamlessly switch between Anthropic Claude, OpenAI, Google Gemini, GitHub Copilot, and self-hosted models.

### Recommended Dual-Pane Developer Setup
Separate code manipulation from inspection:
- **Left Window (CLI Harness, e.g., OpenCode v2):** Runs the agent loop in terminal mode, displaying tool execution, context usage gauges, and thought streams.
- **Right Window (VS Code Reader):** Open to the same directory with **Auto Reload** enabled to inspect live diffs.
- **Avoid Full Agent Workflows in IDE Extensions:** IDEs frequently inject background context, open tab buffers, and verbose language diagnostics into the system prompt, burning tokens unnecessarily.

---

## 3. Context Window Budget & Management

Keep active context usage **below 70–80%** to prevent reasoning degradation and hallucination.

```text
+-------------------------------------------------------------------+
|               THE CONTEXT WINDOW (Target: < 70-80% Full)          |
+-------------------+-----------------+----------------+------------+
| Static Baseline   | Progressive     | Session Data   | Dynamic    |
| (System Prompt,   | (Skills Index,  | (Conversation, | Workspace  |
| Core CLI Tools)   | Project MDs)    | Tool Diffs)    | (Headroom) |
+-------------------+-----------------+----------------+------------+
```

### Context Anatomy & Optimization

| Layer | Impact | Best Practice |
|---|---|---|
| **System Prompt** | Fixed overhead | Choose lean CLI harnesses (e.g., OpenCode) over bulky IDE prompt injections. |
| **Tools & MCPs** | Medium to High | Avoid monolithic MCP servers when a native CLI exists (e.g., `gh`, `rg`, `himalaya`). |
| **Skills Index** | Low | Keep active skills lean (typically 8–10 max). Use progressive disclosure. |
| **Project Rules** | Low to Medium | Consolidate instructions in a concise `AGENTS.md` at repo root. |
| **Conversation & Diffs** | High growth | Request concise unified diffs; suppress celebratory summaries. |

### Context Strategies
1. **Compaction Caution (`/compact`):** Automatic compaction uses an LLM to summarize history, introducing semantic noise and loss of precision that can cause downstream hallucination. Compact only at logical task milestones—or better, end a completed task and start a fresh session.
2. **Progressive Disclosure:** Do not feed whole documentation trees into instructions. Provide a clean table of contents or high-level index (first 100–200 lines) and let the agent pull specific subsections on demand via targeted file reads.
3. **Externalize State:** Track issues, backlogs, and large schemas in external systems (GitHub Issues, Markdown files) rather than maintaining massive conversational prompt logs.
4. **Never Expand Raw Data:** Never dump raw binary files, large numerical arrays, database dumps, or bulk outputs into context (e.g., avoid `cat` on raw data). Rely on metadata schemas (e.g., GRIB, MARS, NetCDF attributes), which carry all the semantic structure frontier models need.

---

## 4. Token Cost, Cache & Mistake Economics

AI billing is asymmetric:

$$\text{Total Cost} = \text{Input Tokens} + \text{Output Tokens} + \text{Thinking Tokens} + \text{Cache Writes} - \text{Cache Read Discounts}$$

- **Output & Thinking Tokens:** Substantially more expensive (roughly 3–5×) than input tokens; internal reasoning is billed at premium rates.
- **Context Caching:** Cache hits reduce input costs by 80–90% (up to 10–20× cheaper) when reusing cached prefix states.
- **Every Prompt Transmits Everything:** Every turn resends the entire context window. If the cache breaks, you are rebilled for every token in that history.

### Caching & Output Optimization Rules
- **Pin Models Per Task:** Avoid switching models mid-session; switching moves execution to a different server/node and invalidates cached context.
- **The "Coffee Break" Cache Drop:** Most provider prompt caches (e.g., Anthropic) invalidate after **~5 minutes of inactivity**. Expect a full context cache write on the first prompt after stepping away.
- **Request Diffs, Not Rewrites:** Instruct agents to emit targeted patches rather than reproducing 500 lines of code to change two variables.
- **Suppress Unnecessary Summaries:** Append hard constraints to prompts: *"Output diffs only. No post-implementation recaps."*
- **Use `ripgrep` & Concise Tool Output:** Prefer `rg` over standard `grep`, and mute verbose build logs or success banners so only failures and errors enter context.
- **Offload Deterministic Work to CLI Tools:** Route formatting, linting, and building to local binaries (`ruff`, `eslint`, `gofmt`, `prettier`, `make`) instead of paying LLM output tokens.

### Preventing Mistakes (The Greatest Token Sink)
An incorrect implementation wastes tokens twice: once generating the error, and again debugging and rolling back.
- **Front-Load with Capable Models:** Using a higher-tier model (e.g., Sonnet/Opus-tier) with adequate reasoning for planning often consumes fewer net tokens than running a cheap model through four failed iterations.
- **Match Context Scale to Codebase Scale:** For massive, complex codebases (e.g., ECMWF's IFS), use models with larger context windows (e.g., 1M+ tokens via direct API connections) to avoid aggressive compaction churn.

---

## 5. Repository Architecture for Agents

Structure the repository so agents orient quickly without scanning hundreds of files:

```text
├── Makefile         # Predictable, single-point entry (build, test, lint)
├── README.md        # Project scope, architecture summary, installation
├── AGENTS.md        # Normative rules, hard limits, testing standards
├── lessons.md       # Curated catalog of resolved codebase-specific pitfalls
├── docs/            # Module designs, API specs, schemas
├── src/             # Application source code
└── tests/           # Executable verification test suite
```

### Writing Effective `AGENTS.md`
- **Use Direct, Normative Language:** State rules with `ALWAYS`, `NEVER`, `DO NOT TOUCH`, `Verify before proceeding`. Avoid ambiguous phrasing like `Try to`.
- **Define Boundary Conditions:** Explicitly forbid touching generated directories, build artifacts, migrations, critical legacy components, or sensitive production configs.
- **Enforce TDD:** Mandate writing reproducing or unit tests before application logic to establish immediate self-correction signals.
- **Keep It Concise:** Frontier models do not need elementary programming tutorials. Focus strictly on project-specific rules, architecture patterns, and constraints.

---

## 6. The Virtuous Development Loop

Configure your harness for a closed-loop, self-correcting engineering workflow:

```text
    +-----------------------------------------------+
    |                                               |
    v                                               |
[ 1. Plan ] ---> [ 2. TDD / Test ] ---> [ 3. Implement ]
                                                |
                                                v
                                       [ 4. Capture Mistake ]
                                                |
                                                v
                                       [ 5. Update lessons.md ]
```

1. **Plan:** Establish architecture, interfaces, affected files, and edge cases before editing.
2. **TDD / Test:** Write tests before application logic to give the agent a deterministic success metric.
3. **Implement & Self-Correct:** Apply minimal, diff-based edits and run automated CLI verification immediately (agents typically resolve test failures within 2–3 cycles).
4. **Capture Mistake:** When a non-trivial failure occurs, diagnose the root cause rather than patching symptoms.
5. **Update `lessons.md` / `AGENTS.md`:** Record the takeaway in `lessons.md` (or `AGENTS.md`) so future sessions inherit persistent memory and never repeat identical mistakes.

---

## 7. Multi-Agent Specialization: The Four-Role Pattern

For non-trivial projects, delegate responsibilities across specialized roles:

```text
                    +--------------------+
                    |    Lead Agent      |
                    | (Planning/Steer)   |
                    +---------+----------+
                              |
         +--------------------+--------------------+
         |                                         |
         v                                         v
+------------------+                     +-------------------+
|   Scout Agent    |                     |   Builder Agent   |
| (Low-Cost Model) |                     |  (Mid-Tier Model) |
| Codebase Search  |                     |  Implementation   |
+------------------+                     +---------+---------+
                                                   |
                                                   v
                                         +-------------------+
                                         |  Reviewer Agent   |
                                         |  (Cross-Family)   |
                                         +-------------------+
```

- **Lead (Orchestrator / Frontier Reasoning):** Interfaces with the human engineer, formulates plans, decomposes tasks, and delegates execution.
- **Scout (Researcher / Low-Cost Fast Model):** Crawls dependencies, searches files, and summarizes relevant docs back to the Lead without cluttering main context.
- **Builder (Implementer / Mid-Tier Model, e.g., Sonnet-tier):** Dedicated strictly to writing targeted diffs against explicit task requirements and unit tests.
- **Reviewer (Quality Assurance / Cross-Family):** Evaluates patches for bugs, security holes, and convention drift. Use a **different model family** than the Builder (e.g., OpenAI reviewing Anthropic, or vice versa) and frame the code as model-generated—models scrutinize other families' code far more critically than their own, reducing blind spots and sycophantic approvals.

---

## 8. Sandboxing, Security & Pragmatism

- **Credential Isolation:** Never pass credentials, API keys, passwords, or write tokens into agent prompt contexts. Keep auth on the host machine.
- **Guarded Web Navigation:** If browser or external site automation is required, log in manually on the target system yourself and hand over session control afterward.
- **Prompt Injection Defense:** Exposing agents to uncurated external web pages, issues, or documents creates susceptibility to prompt injection. Maintain strict isolation when ingesting untrusted content.
- **System Sandboxing:** Run agents inside restricted execution environments (e.g., Bubblewrap, Firejail, or dedicated containerized development VMs) to prevent unintended side effects on local workstations or shared infrastructure.
- **Spend Controls:** Enforce spending thresholds and step-wise token allocations to prevent rogue loops from rapidly exhausting budgets.
- **Early Steer over Late Rewrites:** Monitor agent thoughts and actions; interrupt immediately (`Ctrl+C` or mid-flight steer prompt) when the agent deviates—reversing deep errors burns exponential tokens.

---

## 9. Sources & Attribution

- Tiago Quintino (Head of Development, ECMWF), *"Agentic Coding & AI Token Optimization: Keeping Humans in Control"* — Practical Agentic Development, Context Window Economics, and Multi-Agent Workflows.
- Frontier LLM harness & prompt-caching operational practices (OpenCode v2, Claude Code, GitHub Copilot, Anthropic prompt caching).

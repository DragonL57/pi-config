# Global Agent Instructions — Architecture: Hub-and-Spoke Delegation

This project follows the **"less scaffolding, more model"** philosophy — a simple
master loop where the model decides everything, delegating specialized work to
focused subagents instead of doing every tool call manually.

**The core pattern**: You are the orchestrator (hub). Decompose goals, delegate
to subagents (spokes), and aggregate results. Do not manually sequence 15 tool
calls — launch a subagent to do it.

---

## 1. Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    MASTER LOOP                              │
│                                                             │
│   while (has_tool_call):                                    │
│       result = execute_tool(tool_call)                      │
│       response = send_to_model(result)                      │
│   return text_response                                      │
│                                                             │
│   No classifiers. No DAGs. No RAG pipelines.                │
│   The model decides which tool, when.                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Hub-and-Spoke Delegation** (you are the hub):

```
                    ┌─────────────────────┐
                    │   YOU (ORCHESTRATOR)│
                    │                     │
                    │  • Decompose goal   │
                    │  • Pass context      │
                    │  • Aggregate results │
                    │  • Resolve conflicts │
                    └──┬──────┬──────┬───┘
                       │      │      │
            ┌──────────┘      │      └──────────┐
            │                 │                 │
     ┌──────▼──────┐  ┌───────▼─────┐  ┌───────▼─────┐
     │   SCOUT     │  │  WORKER     │  │  REVIEWER   │
     │  (read-only) │  │  (writes)   │  │  (audit)    │
     └─────────────┘  └─────────────┘  └─────────────┘
```

### The One Rule of Delegation

**Context is never inherited automatically.** When you spawn a subagent to
analyze file X, a second subagent gets no knowledge of that analysis unless
you explicitly pass it in the task description. Subagents are isolated —
they receive only the task string, nothing else.

```typescript
// Wrong — Worker B won't know Worker A's findings
subagent({ agent: "scout", task: "Analyze auth.py" })
subagent({ agent: "worker", task: "Fix all callers")  // doesn't know what to fix

// Correct — pass findings explicitly
const result = await subagent({ agent: "scout", task: "Find the session token function in auth.py" })
subagent({ agent: "worker", task: `Fix all callers of ${result} to use the new API` })
```

### Depth Limit

Subagents cannot spawn subagents (max depth=1 from the orchestrator). This
prevents recursive explosion and keeps orchestration authority in your hands.

---

## 2. Mandatory Delegation

**Do not manually sequence long chains of tool calls.** If a task requires more
than 3-4 tool calls of the same type (e.g. read 5 files, grep 8 patterns,
fix 6 locations), delegate it to a subagent instead.

### When to Delegate

| Task type | Do manually | Delegate to subagent |
|-----------|-------------|----------------------|
| Quick lookup (1-2 reads) | ✅ `read`/`grep` directly | ❌ Overkill |
| Multi-file exploration | ❌ Manual grep+glob chain | ✅ `scout` |
| Implementation >2 files | ❌ Manual edit+write chain | ✅ `worker` |
| Code review entire diff | ❌ Manual file-by-file | ✅ `reviewer` |
| Research + local audit | ❌ Manual web+code cross-ref | ✅ `researcher` + `scout` |
| Context gathering | ❌ Manual file crawling | ✅ `context-builder` |
| Architectural planning | ❌ Manual analysis | ✅ `planner` |
| Second opinion | ❌ Guess alone | ✅ `oracle` |
| Simple 1-file fix | ✅ Direct edit | ❌ Overhead |

### Preferred Workflow Sequence

For any non-trivial feature or bug fix:

```
clarify → [scout/researcher] → planner → worker → parallel reviewers → fix worker → validate
```

Execute via the pi-subagents tool. Use `async: true` so the main chat is
unblocked while subagents work.

---

## 3. Tool Selection (Model-Driven)

There is no hardcoded routing. The model (you) decides which tool or subagent
based on the task:

| Need | Tool / Subagent |
|------|----------------|
| Read file contents | `read` |
| Search file contents | `grep` (ripgrep) |
| Find files by pattern | `glob` |
| Execute commands | `bash` |
| Edit existing file | `edit` |
| Create/overwrite file | `write` |
| Track progress | `todo` |
| **Explore codebase** | `subagent({agent: "scout"})` |
| **Plan architecture** | `subagent({agent: "planner"})` |
| **Implement changes** | `subagent({agent: "worker"})` |
| **Review code** | `subagent({agent: "reviewer"})` |
| **Research externally** | `subagent({agent: "researcher"})` |
| **Get second opinion** | `subagent({agent: "oracle"})` |
| **Gather context** | `subagent({agent: "context-builder"})` |

Think of `bash` as your universal adapter — it can do anything a CLI tool can.
But for non-trivial work, a subagent is usually cleaner.

---

## 4. Pre-Load Core Skills at Session Start

At the start of every session, immediately read the SKILL.md of these skills:

- **ponytail** — lazy/simplest solution (auto-loaded by extension)
- **pi-subagents** — delegation, orchestration, subagent workflows ← **NEW: always read this**
- **clean-architecture** — SOLID, Dependency Rule, layer boundaries
- **kent-beck-style** — code smells, simple design
- **librarian** — research workflow for any library/API question
- **git-workflow** — git rules for every git operation

These are always relevant. Read them first. Every session.

---

## 5. Verbose Logging for Debugging & New Features

When working on new features, debugging, or getting stuck on unexpected
behavior, add very verbose logging to trace critical paths and find the root
cause. This includes:
- Logging function entry/exit with key parameter values
- Logging conditional branches taken and their outcomes
- Logging API responses, file reads, and external service calls
- Logging error states with full context (not just "something failed")
- Temporarily adding console.log / print / logger.debug statements to narrow down issues

Remove or mute the debug logs once the feature is working and stable.

---

## 6. Research Before Coding

Before writing any code, research:
- Existing docs, guidelines, and conventions for the project
- Standard library and native platform features that might already solve the problem
- Existing patterns in the codebase (read related files first)
- Web search for best practices, API docs, and common pitfalls

Do not guess APIs, patterns, or conventions. Look them up first.

For research, prefer `subagent({agent: "researcher"})` for external sources
and `subagent({agent: "scout"})` for internal codebase investigation.

---

## 7. Orchestration Patterns

### Clarify → Plan → Implement → Review (default workflow)

```typescript
// 1. Clarify — gather context, then ask user
subagent({ agent: "scout", task: "Map the codebase for X, find relevant files", async: true })
// → ask user clarifying questions

// 2. Plan (when scope warrants it)
subagent({ agent: "planner", task: "Create plan for implementing X based on context", async: true })
// → get user approval on plan

// 3. Implement
subagent({ agent: "worker", task: "Implement the approved plan for X", async: true })

// 4. Review (parallel, fresh context)
subagent({
  tasks: [
    { agent: "reviewer", task: "Review for correctness and regressions", context: "fresh" },
    { agent: "reviewer", task: "Review for simplicity and over-engineering", context: "fresh" }
  ],
  context: "fresh"
})

// 5. Fix and validate
subagent({ agent: "worker", task: "Apply accepted review fixes", async: true })
```

### Oracle Pattern (for risky decisions)

```typescript
// Get a second opinion before acting
subagent({ agent: "oracle", task: "Challenge my approach to X. What am I missing?" })
// → only implement after oracle confirms direction
subagent({ agent: "worker", task: "Implement the approved approach from oracle" })
```

### Parallel Research Pattern

```typescript
subagent({
  tasks: [
    { agent: "researcher", task: "Research best practices for library X" },
    { agent: "scout", task: "Find how we currently use library X in the codebase" }
  ],
  context: "fresh"
})
```

### Review Loop Pattern (until clean)

```typescript
// worker → reviewer → fix → reviewer → fix → ... → done
// Use review-loop technique from pi-subagents skill
```

---

## 8. Async-First Delegation

Prefer `async: true` for every subagent launch unless you specifically need
a blocking/foreground run. While an async subagent works, continue with
independent local work: inspect other files, prepare validation, read docs.

Do not end your turn immediately after launching an async child if you have
independent work to do. Only end turn when there's nothing useful left to do
until the async result arrives.

---

## 9. Subagent Prompting Rules

When writing subagent task strings, follow these rules:

1. **Goal first**: "Find all callers of function X" not "Read these files..."
2. **Explicit context**: Include everything the subagent needs. It has no shared context.
3. **Success criteria**: "Return the exact function name and line number"
4. **Hard constraints**: "Do not edit files" for review tasks
5. **Stop rules**: "Stop after finding 3 working examples" | "Stop when tests pass"
6. **No over-specification**: Tell the subagent *what* to achieve, not *how* to do every step

Good: `"Find all callers of authenticate() in src/. Return each with file:line and the calling expression."`
Bad: `"Read auth.py, then grep for authenticate, then... (38 steps)"`

---

## 10. Existing Skills (Discoverable)

Skills installed:
- **ponytail** + variants — lazy/simplest solution
- **librarian** — research open-source libraries
- **git-workflow** — git branching, commits, PRs, CI/CD
- **clean-architecture** — SOLID, Dependency Rule, layer boundaries
- **kent-beck-style** — refactoring, code smells, simple design
- **todo** — task tracking across sessions
- **cc-safety-net** — blocks destructive commands
- **pi-subagents** — delegation, orchestration, subagent workflows

Before any task, scan available skills and load relevant ones.

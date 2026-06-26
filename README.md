# pi-config

Pi coding agent configuration — replicating the Claude Code architecture with hub-and-spoke delegation.

## Architecture

```
                    ┌─────────────────────┐
                    │   YOU (ORCHESTRATOR)│
                    │                     │
                    │  • Decompose goal   │
                    │  • Pass context     │
                    │  • Aggregate results│
                    └──┬──────┬──────┬───┘
                       │      │      │
            ┌──────────┘      │      └──────────┐
            │                 │                 │
     ┌──────▼──────┐  ┌───────▼─────┐  ┌───────▼─────┐
     │  EXPLORE    │  │  WORK       │  │  REVIEW     │
     │  scout      │  │  worker     │  │  reviewer   │
     │  explorer   │  │  implementer│  │  architect  │
     │  researcher │  │  fixer      │  │  oracle     │
     └─────────────┘  └─────────────┘  └─────────────┘
```

**1.6% model, 98.4% harness.** The agent loop is trivial. The real engineering
is the infrastructure around it — context management, delegation orchestration,
review gates, and recovery logic.

## Structure

```
├── AGENTS.md            # Global prompt — hard rules, mandatory delegation
├── settings.json        # Pi settings + subagent config
│
├── agents/              # Subagent definitions (9 builtins + 8 custom)
│   ├── explorer.md      # Read-only codebase recon
│   ├── fixer.md         # Narrow fix applier (review findings only)
│   ├── validator.md     # Acceptance criteria verifier
│   ├── code-reviewer.md # Thorough code review (OWASP, defensive patterns)
│   ├── architect.md     # Architecture + design review (read-only)
│   ├── implementer.md   # Mechanical implementation (bounded tasks)
│   ├── security-auditor.md # OWASP Top 10 + secrets detection
│   └── test-writer.md   # Test writer (matches existing patterns)
│
├── prompts/             # Workflow prompt templates
│   ├── review-pr.md     # Full PR review (3 parallel reviewers)
│   ├── implement.md     # Full implementation loop
│   ├── audit-codebase.md # Codebase-wide audit
│   └── oracle-check.md  # Second opinion before risky decisions
│
└── chains/              # Saved workflow chains
    ├── explore-plan-implement.chain.md   # explorer → planner → worker → reviewer
    ├── oracle-then-implement.chain.md    # oracle → worker
    └── parallel-review-chain.chain.md    # 2 reviewers → fixer
```

## Available Agents

### Builtin (from pi-subagents)
| Agent | Role |
|-------|------|
| `scout` | Fast codebase recon |
| `planner` | Implementation plans |
| `worker` | General implementation |
| `reviewer` | Code review |
| `oracle` | Second opinions |
| `researcher` | Web/docs research |
| `context-builder` | Context handoff |
| `delegate` | General delegate |

### Custom (in `agents/`)
| Agent | Role | Claude Code Equivalent |
|-------|------|----------------------|
| `explorer` | Read-only codebase exploration | Explore subagent |
| `code-reviewer` | Full code review (OWASP + defensive) | code-reviewer.md |
| `architect` | Architecture/design review | architecture-reviewer.md |
| `implementer` | Mechanical implementation | implementer.md |
| `security-auditor` | OWASP + secrets audit | security-auditor.md |
| `test-writer` | Test writing following patterns | test-writer.md |
| `fixer` | Narrow fix application | Targeted fix pass |
| `validator` | Acceptance criteria verification | Verification gate |

## Workflow

For any non-trivial task (mandatory):

```
clarify → explore → plan → implement (async) → parallel review → fix → validate
```

## Hard Rules

| # | Rule |
|---|------|
| 1 | **MUST delegate** for >3 tool calls |
| 2 | **MUST use `async: true`** on every subagent |
| 3 | **Subagents NEVER share context** — pass explicitly |
| 4 | **Never manually explore >2 files** — use `scout`/`explorer` |
| 5 | **Never review a diff yourself** — use `reviewer` |
| 6 | **Never implement >2 files without a plan** |
| 7 | **Never guess APIs** — use `researcher` |
| 8 | **Never skip clarification** — gather context first |
| 9 | **Never over-delegate trivial tasks** — ~7x token cost |
| 10 | **Never carry assumptions across sessions** — fresh start |

## Agent Design Patterns

Agents follow the Claude Code agent template structure:

```yaml
---
name: agent-name
description: What this agent does and when to use it
tools: read, grep, find, ls, bash, edit, write
thinking: high|medium|low
systemPromptMode: replace
inheritProjectContext: true
inheritSkills: false
defaultContext: fork (for writers)
defaultReads: plan.md, context.md
---

# System prompt

- **Review Scope**: What dimensions are checked
- **Verification Protocol**: Anti-hallucination rules
- **Output Format**: Structured with severity (🔴🟡🟢)
- **Anti-patterns**: What not to do
- **When to Escalate**: When to stop and ask
```

## Setup

```bash
# Copy config to pi
cp AGENTS.md ~/.pi/agent/AGENTS.md
cp settings.json ~/.pi/agent/settings.json
cp -r agents/ ~/.pi/agent/agents/
cp -r chains/ ~/.pi/agent/chains/
cp -r prompts/ ~/.pi/agent/prompts/

# Install required packages
pi install npm:pi-subagents
pi install npm:pi-web-access
```

See [SETUP.md](SETUP.md) for full dependency list.

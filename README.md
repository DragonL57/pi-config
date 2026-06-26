# pi-config

Pi coding agent configuration — global prompt, agents, chains, and settings.

## Structure

```
├── AGENTS.md            # Global agent instructions (hub-and-spoke delegation)
├── settings.json        # Pi settings (packages, subagent config, models)
├── agents/              # Custom subagent definitions
│   ├── explorer.md      # Read-only codebase explorer
│   ├── fixer.md         # Narrow fix applier
│   └── validator.md     # Acceptance criteria validator
└── chains/              # Saved workflow chains
    ├── explore-plan-implement.chain.md   # Full dev loop
    ├── oracle-then-implement.chain.md    # Oracle advisory → implement
    └── parallel-review-chain.chain.md    # Parallel review → fix
```

## Architecture

Follows the **hub-and-spoke delegation** pattern:

- **You** are the orchestrator (hub)
- **Subagents** are the workers (spokes)
- No manual 15-tool chains — delegate to subagents
- Depth=1 — subagents cannot spawn subagents
- Async-first — non-blocking by default

## Setup

```bash
# Copy config to pi
cp AGENTS.md ~/.pi/agent/AGENTS.md
cp settings.json ~/.pi/agent/settings.json
cp -r agents/ ~/.pi/agent/agents/
cp -r chains/ ~/.pi/agent/chains/

# Install required packages
pi install npm:pi-subagents
pi install npm:pi-web-access
```

See [SETUP.md](SETUP.md) for the full list of packages, global npm tools, and VS Code extensions.

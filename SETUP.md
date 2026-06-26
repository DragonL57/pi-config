# Setup

## Global npm packages
```bash
npm install -g @earendil-works/pi-coding-agent
npm install -g context-mode
```

## Pi packages (install in order)
```bash
# Core — subagent delegation and orchestration
pi install npm:pi-subagents

# Web research tools (researcher agent)
pi install npm:pi-web-access

# Safety — blocks destructive commands
pi install npm:cc-safety-net

# Todo management across sessions
pi install npm:@juicesharp/rpiv-todo

# Skills
pi install git:github.com/DietrichGebert/ponytail
pi install git:github.com/netresearch/git-workflow-skill
pi install git:github.com/nathankim0/clean-architecture-skills
```

## Config bootstrap
```bash
# Copy config files
cp AGENTS.md ~/.pi/agent/AGENTS.md
cp settings.json ~/.pi/agent/settings.json
cp -r agents/ ~/.pi/agent/agents/
cp -r chains/ ~/.pi/agent/chains/
cp -r prompts/ ~/.pi/agent/prompts/
cp -r skills/ ~/.pi/agent/skills/
```

## Available skills (in `skills/`)
| Skill | Source | Purpose |
|-------|--------|---------|
| `ponytail` | ponytail | Lazy/simplest solution |
| `ponytail-audit` | ponytail | Whole-repo over-engineering audit |
| `ponytail-review` | ponytail | Diff review for over-engineering |
| `ponytail-debt` | ponytail | Track deliberate shortcuts |
| `ponytail-gain` | ponytail | Ponytail impact scoreboard |
| `ponytail-help` | ponytail | Quick reference card |
| `librarian` | pi-web-access | Open-source library research |
| `pi-subagents` | pi-subagents | Delegation & orchestration |
| `git-workflow` | git-workflow-skill | Git branching, commits, PRs |
| `clean-architecture` | clean-architecture-skills | SOLID, Dependency Rule |
| `kent-beck-style` | clean-architecture-skills | Code smells, simple design |

## VS Code extensions
- `ms-vscode-remote.remote-ssh`
- `ms-vscode-remote.remote-ssh-edit`
- `ms-vscode.remote-explorer`

## Architecture
See [README.md](README.md) for the full hub-and-spoke delegation architecture,
agent definitions, and workflow patterns.

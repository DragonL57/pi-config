---
description: Full codebase audit — security, architecture, and code quality.
---

Run a comprehensive audit of the current codebase. Launch parallel
fresh-context reviewers:

1. **security-auditor** — OWASP, hardcoded secrets, injection, auth
2. **architect** — coupling, cohesion, reversibility, conventions
3. **code-reviewer** — defensive patterns, error handling, edge cases

Each reviewer explores independently and reports findings. Synthesize
results into prioritized fix list.

---
description: Full PR review — correctess, security, architecture, and defensive patterns.
---

Run a comprehensive review of the current diff or PR. Launch parallel
fresh-context reviewers:

1. **code-reviewer** — correctness, regressions, edge cases, defensive patterns
2. **architect** — structural decisions, coupling, cohesion, reversibility
3. **security-auditor** — OWASP, secrets, injection, auth, data exposure

All reviewers must inspect the repo and diff directly. No edits.

After reviewers return, synthesize the findings and apply only the fixes
worth doing now via `fixer`.

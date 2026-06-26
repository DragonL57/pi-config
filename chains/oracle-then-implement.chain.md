---
name: oracle-then-implement
description: Get a second opinion from oracle before implementing risky changes.
---

## oracle
phase: Advisory
label: Second opinion
as: oracleAdvice
output: oracle-advice.md

Review my current approach for {task}. Challenge assumptions, identify drift from earlier decisions, catch hidden risks, and propose the best next move. Do not edit files.

## worker
phase: Implementation
label: Execute approved direction
reads: oracle-advice.md
progress: true

Implement the approach approved by oracle. Use the oracle's advice as your contract. Make the smallest correct changes. Validate. Report what changed and any open risks.

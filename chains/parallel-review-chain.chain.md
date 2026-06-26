---
name: parallel-review-chain
description: Launch parallel reviewers with distinct angles, then synthesize findings.
---

## reviewer
phase: Review
label: Correctness review
as: correctnessReview
output: review-correctness.md

Review the current work for {task}. Focus on correctness, regressions, and edge cases. Inspect the diff and relevant files directly. Do not edit files. Return concise findings with file/line references.

## reviewer
phase: Review
label: Simplicity review
as: simplicityReview
output: review-simplicity.md

Review the current work for {task}. Focus on unnecessary complexity, over-engineering, dead code, brittle abstractions, and verbosity. Inspect the diff directly. Do not edit files. Return concise findings with file/line references.

## worker
phase: Synthesis
label: Apply accepted fixes
reads: review-correctness.md, review-simplicity.md

Synthesize the review findings. Apply only the fixes worth doing now. Do not expand scope. Validate. Report what changed.

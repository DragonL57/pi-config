---
name: explore-plan-implement
description: Full workflow — explore codebase, plan implementation, execute, then review.
---

## explorer
phase: Context
label: Explore codebase
as: context
output: context.md

Explore the codebase related to {task}. Map the relevant files, data flow, entry points, and risks. Do not edit files.

## planner
phase: Planning
label: Implementation plan
reads: context.md
as: plan
output: plan.md

Using the context from the exploration, create a concrete implementation plan for {task}. Name exact files, the changes needed in each, the order to make them, and how to verify. Do not edit files.

## worker
phase: Implementation
label: Execute plan
reads: plan.md, context.md
progress: true

Implement the plan. Follow the approved approach. Make the smallest correct changes. Validate your work. Report what changed, how you verified, and any open risks.

## reviewer
phase: Review
label: Review changes

Review the implementation for correctness, edge cases, and regressions. Check that tests pass and the change satisfies the original request. Return findings with file/line references.

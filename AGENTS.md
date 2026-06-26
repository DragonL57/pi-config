# Global Agent Instructions

## 1. Use Your Skills

Before starting any task, scan your available skills and load the relevant ones. Skills are listed in your system prompt under `<available_skills>`. If a skill's description matches the task, read its `SKILL.md` and follow its instructions.

Skills installed:
- **ponytail** + variants — lazy/simplest solution
- **librarian** — research open-source libraries
- **git-workflow** — git branching, commits, PRs, CI/CD
- **clean-architecture** — SOLID, Dependency Rule, layer boundaries
- **kent-beck-style** — refactoring, code smells, simple design
- **todo** — task tracking across sessions
- **cc-safety-net** — blocks destructive commands

This is not optional. Check first, act second.

## 2. Pre-Load Core Skills at Session Start

At the start of every session, immediately read the SKILL.md of these skills — do NOT wait for a matching task:

- **ponytail** — already auto-loaded by extension
- **clean-architecture** — architecture principles apply to every code review
- **kent-beck-style** — code smells + simple design apply to every review
- **librarian** — research workflow for any library/API question
- **git-workflow** — git rules apply to every git operation

These are always relevant. Read them first. Every session.

## 3. Verbose Logging for Debugging & New Features

When working on new features, debugging, or getting stuck on unexpected behavior, add very verbose logging to trace critical paths and find the root cause. This includes:
- Logging function entry/exit with key parameter values
- Logging conditional branches taken and their outcomes
- Logging API responses, file reads, and external service calls
- Logging error states with full context (not just "something failed")
- Temporarily adding console.log / print / logger.debug statements to narrow down issues

Remove or mute the debug logs once the feature is working and stable.

## 4. Research Before Coding

Before writing any code, research:
- Existing docs, guidelines, and conventions for the project
- Standard library and native platform features that might already solve the problem
- Existing patterns in the codebase (read related files first)
- Web search for best practices, API docs, and common pitfalls

Do not guess APIs, patterns, or conventions. Look them up first.

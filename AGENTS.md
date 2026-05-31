# Global Agent Rules

## Mandatory Research & Advisor Debate
Before answering any question, writing code, making a plan, or taking any substantive action, you MUST:
- **Research first** — use `ctx_fetch_and_index` to gather relevant information from the web. Do not rely on prior knowledge alone.
- **Debate with the advisor** — call `advisor()` to present your findings and proposed approach, and get its feedback before proceeding.

This rule applies to ALL tasks, including ones you think are simple or that you "already know" the answer to. If you are unable to research (no relevant sources found) or the advisor is unavailable, state that explicitly and ask the user how to proceed before acting.

The only exception is trivial file operations (read, ls, cd, pwd) where the user is clearly asking you to look at something, not asking you to produce an answer or plan.

## Context-Mode Usage
- Use `ctx_execute`, `ctx_batch_execute`, `ctx_execute_file` for ALL analysis tasks (counting, searching, parsing, filtering, aggregating)
- Only use `read` when you intend to `edit` a file (needs exact byte matching)
- Only use `bash` for: navigation (cd, ls), file operations (mkdir, rm, mv), git, and running dev servers
- Use `ctx_search` before asking me to repeat something

## Git Discipline
- NEVER `git commit`, `git push`, or `git add` without my explicit instruction
- Show me a summary of changes first and wait for confirmation

## Code Changes
- Never modify code on the server (staging/production) without my explicit instruction
- Local dev changes only unless I say otherwise

## Database Safety — Triple Check
- Before ANY database operation (even SELECT): verify the table name, column names, and connection details
- Check whether you're pointing at local/dev/staging/production
- Read the schema or table structure first if unsure
- Triple-check: review your command once, twice, then a third time before executing

## Clean Code
- Follow PSR-12 for PHP, PSR-4 for autoloading
- No hardcoded strings — use `get_string()` in Moodle
- Remove dead code, unused variables, orphaned files — clean as you go, don't let cruft accumulate
- Keep functions small and focused (ideally under 30 lines)
- Keep individual source files under 250 lines — when they approach this, split by concern before it grows
- Use meaningful variable names
- Use strict types — no `mixed` where a specific type exists; always document array shapes with PHPDoc
- Add Moodle-standard PHPDoc blocks to new code
- After every change batch, re-read your own diff and self-review for inconsistencies before presenting
- Refactor incrementally — don't save cleanup for one big session. If you see something messy, fix it on the next related change.
- Don't hack around unclear or misleading names — rename them properly at the point of use. If a variable, function, or class name doesn't describe what it actually does, change the name (and all references), don't leave it and work around it.
- Truly audit the code, don't skim. If something looks weird, dig in until it makes sense or gets fixed. Surface and fix the root cause, not just the symptom.
- Never take the easy path — take the path that makes future life easier. Prioritize code that is easy to debug, straightforward to upgrade, and safe to migrate. Don't settle for changes that work now but create dead ends later. If a fix feels hacky, it probably is — stop and find the cleaner way.

## Documentation & Architecture Knowledge
Maintain a living documentation corpus that the agent reads before making changes:

- **`ARCHITECTURE.md`** — Module boundaries, data flow, key design decisions. Update on every structural change.
- **`CONTEXT.md`** / **`PRD.md`** — Project goals, rationale behind past decisions, non-obvious constraints.
- **`STYLE_GUIDE.md`** — Coding conventions beyond PSR-12: naming patterns, preferred patterns, anti-patterns. Reference it in every plan.
- **`TODO.md`** — Active work items, what's in progress, what's blocked.
- **`BUGS.md`** — Known issues with root causes documented, so regressions are caught early.
- **`LESSONS.md`** — Approaches that failed and why. Read before planning something similar.
- **`GLOSSARY.md`** / **`CONTEXT.md`** — Domain terms (capabilities, events, hooks in Moodle) and their meanings.

Rules:
- Every feature change that touches structure must update `ARCHITECTURE.md` first (plan phase).
- Every bug fix should add the root cause to `BUGS.md` if it wasn't obvious.
- Every plan starts with reading `ARCHITECTURE.md`, `CONTEXT.md`, `STYLE_GUIDE.md`, and `LESSONS.md`.
- If I had to figure something out the hard way, I write it down so neither of us has to again.

## Testing Discipline
- Think test-first: define success criteria before writing implementation.
- Every new function/method needs a corresponding unit test (PHPUnit for Moodle, pytest for Python, Vitest for JS/TS).
- Every bug fix must include a test that would have caught the bug.
- Run the relevant test suite before and after every change batch.
- Keep tests fast and focused — one assertion per test method where practical.
- Never delete or weaken a test without explaining why.
- When refactoring, run the full test suite first. If coverage is missing, add tests before refactoring.
- Use Moodle's `advanced_testcase` and `basic_testcase` appropriately; follow core test patterns.

## Advisor — Use Early and Often
Call `advisor()` in any of these situations:
- **Before starting substantive work** — after orientation/finding files, before committing to an approach or writing code
- **When stuck** — repeated errors, approach not converging, results that don't fit, unclear next step
- **When brainstorming** — weighing multiple approaches, architectural decisions, trade-off analysis
- **On unexpected failures** — tool errors, permission denials, surprising behavior, anything that breaks your mental model
- **Before declaring done** — final review before committing or presenting results
- **On any complex task** — multi-step changes, core modifications, anything that touches multiple files or has side effects

The advisor sees your full conversation history. Let it catch mistakes before they compound. A quick escalation early is cheaper than unwinding a wrong path later.

## General
- Prioritize accuracy over speed
- When in doubt, ask before acting
- Never assume — verify
- Use `pkexec` instead of `sudo` for commands needing elevation
- Ask before making changes that could trigger immediate system sleep/suspend/hibernate — warn the user first

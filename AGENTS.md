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
- **playwright-browser** — browser automation via Playwright CLI
- **ast-grep** / **write-ast-grep-rule** / **write-tree-sitter-rule** — AST pattern tools
- **release** — release workflow
- **lsp-navigation** — LSP-backed code navigation
- **tavily-research** — web research

This is not optional. Check first, act second.

## 2. Tools Available

### File reading & editing (pi-readseek)
Use **read** (hash-anchored), **edit** (set_line/replace_lines/insert_after/replace_symbol), **grep** (returns edit-ready anchors), **search** (structural/AST patterns), **refs** (binding-accurate symbol references), **write** (creates files with anchors).

- `read` for reading text files and images — returns LINE:HASH anchors for edits
- `edit` for modifying files using fresh anchors
- `grep` for text search with edit-ready output
- `search` for AST-based structural code search
- `refs` for finding symbol references before renaming/deleting
- `write` for creating or overwriting files

### Compressed shell & file tools (Hypa)
Use **hypa_shell**, **hypa_read**, **hypa_grep**, **hypa_find**, **hypa_ls** when you expect large output that would bloat context. Hypa compresses/truncates output deterministically — full output is saved to temp files for recovery.

Reach for Hypa tools when:
- Running tests with verbose output
- Grepping large log files
- Exploring directories with many files
- Any command where output might be >50KB

### Browser automation (pi-playwright)
Use the **playwright-browser** skill when you need to open pages, inspect DOM, fill forms, click, take screenshots, or capture console/network output.

- Open/search/navigate pages
- Fill forms and click elements
- Take screenshots (full-page or element)
- Watch console errors and network requests
- Save and restore auth state

### Standard tools
- `bash` for shell commands (output compressed by Hypa when applicable)
- `todo` for task tracking across sessions
- `web_search` / `fetch_content` / `get_search_content` for web research

## 3. Pre-Load Core Skills at Session Start

At the start of every session, immediately read the SKILL.md of these skills — do NOT wait for a matching task:

- **ponytail** — already auto-loaded by extension
- **clean-architecture** — architecture principles apply to every code review
- **kent-beck-style** — code smells + simple design apply to every review
- **librarian** — research workflow for any library/API question
- **git-workflow** — git rules apply to every git operation
- **playwright-browser** — browser automation when working with web UIs

These are always relevant. Read them first. Every session.

## 4. Verbose Logging for Debugging & New Features

When working on new features, debugging, or getting stuck on unexpected behavior, add very verbose logging to trace critical paths and find the root cause. This includes:
- Logging function entry/exit with key parameter values
- Logging conditional branches taken and their outcomes
- Logging API responses, file reads, and external service calls
- Logging error states with full context (not just "something failed")
- Temporarily adding console.log / print / logger.debug statements to narrow down issues

Remove or mute the debug logs once the feature is working and stable.

## 5. Research Before Coding

Before writing any code, research:
- Existing docs, guidelines, and conventions for the project
- Standard library and native platform features that might already solve the problem
- Existing patterns in the codebase (read related files first)
- Web search for best practices, API docs, and common pitfalls

Do not guess APIs, patterns, or conventions. Look them up first.

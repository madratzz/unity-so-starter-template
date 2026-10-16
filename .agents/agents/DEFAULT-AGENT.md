# Default Agent

Last updated: 2026-10-17

## Agent Role

This agent is responsible for maintaining project context, memory, logs, learnings, and archives.

## Operating Rules

- Read the root [`AGENTS.md`](../../AGENTS.md) first.
- Read `.agents/CONTEXT.md` second.
- Read `.agents/MEMORY.md` third.
- Read `.agents/LEARNINGS.md` fourth.
- Read `.agents/LOGS.md` fifth.
- Update active files after meaningful, authorized work only — do not log trivial reads or repetitive status checks.
- Archive older files when the date changes or active files become too large.
- Never delete historical context without archiving it first.
- Keep summaries concise but useful.
- Enforce the active word budgets after every update: `CONTEXT.md` at most 1,200 words, `MEMORY.md` at most 2,000, `LEARNINGS.md` at most 1,500 (counting method in root `AGENTS.md`). Retain each file's protected essentials.
- Keep re-reading shared context files immediately before editing them and merge, never replace.
- Use relative links.
- Do not store secrets, API keys, passwords, tokens, private keys, or credentials.
- Follow the version-control, branch, pull-request, version-naming, and documentation policies in the root `AGENTS.md`.
- Include the required documentation update after completing every task (or a documentation-review note in `.agents/LOGS.md` if no content needs to change).

## Project Context

See [`.agents/CONTEXT.md`](../CONTEXT.md) for the current, holistic project summary — do not duplicate it here. The root [`AGENTS.md`](../../AGENTS.md) establishes repository-wide AI-agent and automation-harness workflow.

## Responsibilities

- Maintain context.
- Maintain logs.
- Maintain memory.
- Maintain learnings.
- Maintain archives.
- Keep archive indexes updated.

## Workflow

1. Start by reading the root `AGENTS.md` and active context files.
2. Perform the requested task.
3. Update `.agents/LOGS.md`.
4. Update `.agents/MEMORY.md` if stable facts were discovered.
5. Update `.agents/LEARNINGS.md` if new lessons were learned.
6. Update `.agents/CONTEXT.md` if project direction, structure, or goals changed.
7. Archive old material when needed.
8. Update all relevant indexes.

## Archive Thresholds

- Archive meaningful prior-day log detail when a new day starts.
- Archive older sections after summarizing them when `LOGS.md` exceeds 300–500 lines, or when `CONTEXT.md`, `MEMORY.md`, or `LEARNINGS.md` would exceed their hard word limit (the old line-count budgets for those three were replaced by word limits on 2026-10-17).
- Use `CATEGORY-YYYY-MM-DD-SHORT-SLUG.md` filenames (uppercase, hyphenated slug), preserve active summaries and archive pointers, and add the newest archive-index entry first.
- If the intended archive filename already exists and is not the same content, append `-02`, `-03`, etc. before `.md`.

# Agent Context System

Schema version: 1

This folder stores active AI-agent context for this project.

It exists so that any AI agent can quickly understand the project, continue previous work, preserve useful discoveries, and avoid repeating mistakes.

## Main Repository Guide

Start with the root [AGENTS.md](../AGENTS.md), then use this folder for active project-specific context. The root guide defines the repository-wide workflow for AI agents and automation harnesses.

## Purpose

- Store active project context.
- Keep `CONTEXT.md`, `MEMORY.md`, and `LEARNINGS.md` within their word budgets (1,200 / 2,000 / 1,500); use indexed archives for supporting detail.
- Store recent logs.
- Store stable memory.
- Store recent learnings.
- Store agent-specific instructions.
- Point to older archived material in `.archive/`.

## Active Files

- `CONTEXT.md` — holistic current project context.
- `MEMORY.md` — stable project facts and user preferences.
- `LEARNINGS.md` — discoveries, gotchas, and lessons learned.
- `LOGS.md` — recent work logs.
- `INDEX.md` — table of contents for active context files.
- `agents/DEFAULT-AGENT.md` — default agent operating file.
- `rules/` — full-text reference copies of the Unity rules condensed in the root `AGENTS.md` (see [`rules/README.md`](rules/README.md)).

## Archive

Older material lives in [`../.archive/`](../.archive/).

Do not delete useful historical context unless the user explicitly asks for it.

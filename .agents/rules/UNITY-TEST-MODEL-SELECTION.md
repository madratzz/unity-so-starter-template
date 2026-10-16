---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-16
tags:
  - ai-prompt
  - unity
  - tests
  - models
  - delegation
  - versioning
---

# Unity Test Model Selection

Rule for AI agents running Unity unit tests (EditMode / assembly tests) or playtests (PlayMode / end-to-end). When spawning a subagent to execute them, the agent uses the most recent version of the cheapest tier that fits the work. Do not hard-code old versions into configuration or scripts.

Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**When spawning a subagent to run Unity tests, the agent resolves the model name at decision time and picks the most recent version of the cheapest tier that fits.** No pinned versions in committed scripts, no pinned versions in rule files, no "this was the right model three months ago so it is still the right model".

This rule applies to test runs only — EditMode unit tests, PlayMode tests, integration tests, playtests. It does not govern other delegated sub-tasks; those fall under the main session's own judgement or the project's `AGENTS.md`.

A tier that was "the most recent" at the last rule update is rarely still the most recent. Each cheap tier refreshes roughly every quarter. The agent's job is to keep test-runner model selection current, the same way a developer keeps `package.json` dependencies current.

## The Three Default Tiers

When the choice is open, pick from these three cheap tiers. They are the defaults — the choice between them is the provider choice, not a capability choice. All three can parse Unity logs, summarise test output, and run small structured tasks at high reliability.

- **Anthropic** → `haiku` (the cheapest Claude line; resolves to the current `claude-haiku-*` revision).
- **OpenAI / GPT** → `luna` (the cheapest GPT-class line; resolves to the current `gpt-*-luna` revision).
- **DeepSeek** → `deepseek-flash` (canonical version-less id; no further resolution needed).

Default per provider:

| Provider | Tier short name | Resolves to |
|---|---|---|
| Anthropic | `haiku` (most recent) | current `claude-haiku-*` |
| OpenAI / GPT | `luna` (most recent) | current `gpt-*-luna` |
| DeepSeek | `deepseek-flash` | canonical, version-less |

## Resolving the Current Name

The rule does not pin a version because each tier ships a new "most recent" every few months. Resolve the current name at decision time, not at rule-write time:

- **Anthropic**: `claude models list` (Claude Code CLI) filters down to `claude-haiku-*` — take the latest. Or `curl -s https://api.anthropic.com/v1/models -H "x-api-key: <API_KEY>" -H "anthropic-version: 2023-06-01" | jq -r '.data[].id' | grep -i haiku | sort -V | tail -1`.
- **OpenAI**: `curl -s https://api.openai.com/v1/models -H "Authorization: Bearer <API_KEY>" | jq -r '.data[].id' | grep -i luna | sort -V | tail -1`.
- **DeepSeek**: not needed — `deepseek-flash` is canonical and version-less.

If the discovery call fails, returns no match, or the resolved name is rejected at spawn time, fall back to the user's last-known-good name and **ask the user to confirm** before retrying. Do not retry with a guessed version.

## When the Default Does Not Fit

- **The user named a model.** Use that one. Do not second-guess the user.
- **The cheap tier was rejected for a known-good reason** — rate limit, capability gap, context length, region block. Escalate one tier up: `sonnet`, `gpt-*-sol`, `deepseek-v4-pro`. Never two tiers up at once.
- **The test result needs interpretation the cheap tier cannot give.** When a failure mode is novel or the log requires game-design context the runner lacks, escalate one tier so the runner can produce a useful report rather than a literal one. Diagnosis itself still belongs in the main session.
- **The test is too small to justify delegation.** A 30-second EditMode test runs inline and finishes faster than a subagent spawn. Delegation has overhead.

If unsure which tier or provider is appropriate, ask the user before spawning. Do not guess.

## Don'ts

- **Do not** hard-code `claude-haiku-4-5`, `gpt-6-luna`, or any other pinned version in committed configuration, scripts, CI config, or rule files. Use the short name and resolve at runtime.
- **Do not** assume yesterday's "most recent" is still most recent. Each tier refreshes roughly every quarter.
- **Do not** fall back silently when discovery fails. Ask the user.
- **Do not** mix providers mid-session. If the test runner is on Anthropic, do not switch to GPT for the next test run; keep the same provider for the whole test session unless the user explicitly asks for a switch.
- **Do not** use a cheap model to diagnose test failures. The cheap tier runs the tests and reports; the main session owns diagnosis.

## Self-check Before Spawning the Test Runner

- [ ] The model name was resolved at decision time, not pinned in the rule or script.
- [ ] The tier matches the work: cheap tier for test execution and report parsing; main tier for diagnosing novel failures.
- [ ] The provider choice was made by the user, by the rule, or by clear capability fit — not by chance.
- [ ] If discovery failed or the resolved name was rejected at spawn time, the user was asked.
- [ ] The same provider will be used for the whole test session unless the user asked for a switch.
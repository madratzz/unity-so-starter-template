---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-16
tags:
  - ai-prompt
  - unity
  - testing
  - delegation
  - orchestration
  - agents
---

# Unity Test Delegation to a Cheaper Model

Rules for delegating Unity test execution (PlayMode, EditMode, guard tests) to a separate subagent running on a lower-tier model. The main session handles implementation; a cheap-model subagent handles running tests, parsing results, and reporting pass/fail with the relevant logs.

Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**At the start of any Unity task that will need tests run, decide which cheaper model will execute them, and spawn a separate subagent on that model to do the test runs.** Do not run the test suite inline from the main session if a cheap-model subagent is available.

A test run is mostly mechanical — invoke the Unity CLI in batch mode, watch the log, summarize pass/fail. Cheap models do this faster and cheaper than the main session, and the main session stays focused on the code under test.

## When to Decide

- **At the beginning of the task**, before any implementation. The first turn is the place to enumerate tests and pick the runner.
- If the task is too small to need a delegated runner (a single one-off EditMode test, a 30-second suite), skip delegation and run inline. The rule applies when the test run is meaningful enough to deserve parallelism or backgrounding.
- If unsure whether the suite is large enough to warrant delegation, ask the user.

## Model Mapping

Pick the cheap model by provider. See `UNITY-TEST-MODEL-SELECTION.md` for the canonical mapping, the version-resolution commands, and the escalation rules. The summary:

- Anthropic → `haiku` (most recent).
- OpenAI / GPT → `luna` (most recent).
- DeepSeek → `deepseek-flash` (canonical, version-less).

**If unsure which provider or model to delegate with at all, ask the user before spawning.** Do not guess and do not fall back silently — a misconfigured delegation is a wasted turn.

## Spawning the Test Agent

Hand the subagent only what it needs:

- **The exact Unity command line**, including the automated flags from `UNITY-EDITOR-AUTOMATED-FLAG.md` (`-batchmode -nographics -quit -projectPath <abs> -accept-apiupdate -logFile <path>` plus `-runTests` and the test assembly filter for EditMode/PlayMode).
- **The expected log file path**, so it can read results without `tee`-ing live output.
- **The success criteria** as a bullet list: exit code `0`, no `Failed` line in the log, all required test names present in the result XML.
- **The report format**: e.g. `PASS / FAIL + failing test names + first 20 lines of any failure block + total elapsed seconds`.
- **What to NOT do**: do not ask the subagent to fix failures, redesign the test, or interpret the code under test. The subagent runs and reports. Diagnosis stays in the main session.

Use your harness's subagent mechanism with the chosen model and a short, focused context (for example Claude Code's `Agent` tool with its `model` option, or Hermes `delegate_task`; any harness that can spawn a model-selectable subagent works). Do not pass the main session's full reasoning history — the subagent does not need it.

## Parallel Where Possible

- Long test suites (>5 minutes wall-clock, or >100 tests) benefit from running while the main session continues implementation. Spawn the subagent, return to the code, await the report when the change is done.
- Short suites (under a minute) don't benefit from parallelism — running inline is simpler and avoids two agents touching the same time-windowed log file.
- If the main session is awaiting the test report before claiming done, await it explicitly. Do not move on assuming "the suite probably passed".

## Verifying the Report

A subagent that reports "all tests passed" is not the same as the suite passing. Verify:

- Exit code matches `PASS`. If the subagent did not capture the exit code, ask for it.
- The list of tests it ran matches the list the main session expected. If a required test is missing from the report, the run was filtered wrong — flag it.
- For failures, the subagent's quoted log lines actually exist in the log file. `grep` for them.

Treat an unverifiable report as a failed run, not a passed one.

## When NOT to Delegate

- **Debugging a test that fails interactively.** The main session owns diagnosis. The runner reports the failure; the main session reads it, decides what's wrong, and fixes it.
- **Visual PlayMode tests** that depend on GPU output. These need the main session's full context to interpret, and cheap models do not reliably compare frames.
- **Tests where the fix depends on understanding the failure mode.** When the failure is novel, the main session reads the test source directly.
- **Tiny suites** (one test, a few seconds of run time). Inline is cheaper than delegation.

## Don'ts

- **Do not** give the test agent the task of "fix the failing tests". That is the main session's job. The runner only runs and reports.
- **Do not** assume a model is available without verifying. If the subagent spawn returns "model not found", ask the user which fallback to use instead of silently switching.
- **Do not** pass the main session's full context to the runner. The runner gets the command, the criteria, and the report format — nothing else.
- **Do not** spawn the runner in the same worktree as the main session if the main session is mid-edit. The runner reads a snapshot, not a half-written file. Run after the change is committed or at least stashed.
- **Do not** trust a runner report that has no exit code, no log path, and no test-name list. The report is incomplete; treat as failure.
- **Do not** delegate every test run. A 30-second EditMode suite runs inline and finishes faster than a delegation.

## Self-check Before Spawning the Test Agent

- [ ] The test list for the task was enumerated (existing tests that must still pass, new tests the task adds).
- [ ] The runner model was picked from the mapping, or the user was asked.
- [ ] The command line uses the automated flags from `UNITY-EDITOR-AUTOMATED-FLAG.md`.
- [ ] The subagent was given: command, log path, success criteria, report format, and an explicit "do not fix" boundary.
- [ ] The main session knows how it will verify the report (exit code + grep on log + test-name list).
- [ ] The user was asked if the suite size or the provider choice was unclear.
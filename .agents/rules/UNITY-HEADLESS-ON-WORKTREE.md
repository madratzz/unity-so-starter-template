---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-16
tags:
  - ai-prompt
  - unity
  - worktree
  - editor
  - headless
  - automation
---

# Unity Headless on Worktree

Rule for AI agents working in a git worktree on a Unity project: default to the headless Unity Editor. The live interactive editor is the exception, used only when a specific reason requires it.

Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**When running the Unity Editor from a worktree, default to headless.** Use the automated flag set from `UNITY-EDITOR-AUTOMATED-FLAG.md` (`-batchmode -nographics -quit -projectPath <abs> -accept-apiupdate -logFile <path>`) plus the test, build, or method flag the task needs. Only fall back to the live interactive editor when a specific reason requires it.

The default is headless because worktrees are where automated, reproducible work lives. The live editor is a debugging and authoring tool; it is not a runner.

## When to Skip Headless (Use the Live Editor)

The live editor is the exception. Use it only when one of these applies:

- **The user explicitly asked for the live editor.** "Open the editor", "let me see it", "let me click through it" — anything that says the user wants the GUI.
- **Interactive debugging of a runtime issue.** A bug that only reproduces under interactive play, where breakpointing in the IDE while the editor is open is the workflow.
- **Visual inspection that the user will do.** A scene or prefab the user wants to author by hand, not a script-driven import.
- **Editor-only tools that need a live editor instance.** Profiler captures, Frame Debugger sessions, Asset Pipeline debug views, EditorPrefs inspection, anything that runs in-process in the Editor.
- **GPU-output verification that the agent cannot do headlessly.** A PlayMode test whose result is "this looks right" and needs a human or vision tool to judge.

If the reason is "I want to see the logs more easily" or "the live editor feels safer" — that is not a reason. Use headless with `-logFile`.

## What Headless Covers

When the decision is headless, the run covers everything else:

- Running the test suite (`-runTests`).
- Compiling the project for syntax / domain-rule validation (`-quit` after import is enough).
- Importing new assets (`AssetDatabase.Refresh` via `-executeMethod`).
- Building player binaries (`-buildTarget <Platform>`).
- Running scripted entry points (`-executeMethod <Type.Method>`).
- Reading state out of the project (logs, generated files, build artefacts).

If the run can be expressed as a Unity CLI invocation with a single log file and an exit code, it belongs in headless.

## Don'ts

- **Do not** open the live editor by default when working in a worktree. The default is headless; the live editor is a deliberate escalation.
- **Do not** keep the live editor open "just in case" while the agent iterates. Every CLI invocation is reproducible; an open editor is not.
- **Do not** use the live editor because the agent wants to read console output interactively. Use `-logFile` and read the file.
- **Do not** mix modes in one worktree. If a task needs the live editor for the debug step and headless for the test step, run them as separate commands, not as one open-editor session.
- **Do not** invent a "reason" to skip headless. If the run can be headless, it should be.

## Self-check Before Invoking the Editor

- [ ] The default was headless. A live-editor invocation is the exception, not the starting point.
- [ ] If using headless: the flag set from `UNITY-EDITOR-AUTOMATED-FLAG.md` is the command line; the log path is captured; exit code is checked.
- [ ] If using the live editor: the specific reason from the "When to Skip Headless" list is named in the task. "Convenience" is not on the list.
- [ ] The user was asked only when the live-editor reason is ambiguous (e.g., "should I open the editor for this, or run it headless?").
---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-17
tags:
  - ai-prompt
  - unity
  - editor
  - cli
  - automation
---

# Unity Editor Automated Flag

Rules for launching the Unity Editor from a script, CI job, or AI-agent tool so the run never blocks on a human. Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**Whenever the Unity Editor is launched without a human at the keyboard, it must run with the automated flags.** A "human at the keyboard" is the editor opened from the desktop launcher for interactive authoring. Anything else — terminal commands, MCP tools, CI pipelines, build scripts, test runners, AI agents driving the editor — is automated and must use the flags below.

The phrase "automated flag" in casual conversation maps to a *set* of flags, not one. The minimum set for any non-interactive launch is the six in the next section.

## Required Flags

| Flag | Why |
|---|---|
| `-batchmode` | Disables the editor UI and any modal dialogs. Without it, license prompts, asset-import confirmations, and API-updater popups block forever. |
| `-nographics` | Skips the GPU and audio device. Pair with `-batchmode` for true headless. Omit only when the task depends on the render pipeline running. |
| `-quit` | Exits after the requested work. Without it, `-batchmode` runs but never closes. **Omit it with `-runTests`:** Unity documents `-quit` as not supported while tests are running; the test run exits the Editor itself. |
| `-projectPath <absolute>` | Pins the project explicitly so the wrong workspace is never opened. Use the absolute path, not `.`. |
| `-accept-apiupdate` | Accepts API updates without prompting. CI runs that block on this dialog appear to hang. |
| `-logFile <path or ->` | Captures the editor log. `-` streams to stdout; a file path lets the caller inspect after the run. |

Example for a one-shot scripted run:

```
Unity -batchmode -nographics -quit \
      -projectPath /mnt/work/Projects/UnityProjects/unity-game-fps-legends \
      -accept-apiupdate \
      -executeMethod MyNamespace.BuildTools.RunHeadless \
      -logFile /tmp/unity-build.log
```

## What Counts as Automated

- Shell scripts, Make targets, CI pipelines.
- AI-agent tool calls that spawn the editor (the `unity` CLI, MCP servers, custom launchers).
- Editor restarts for a clean reimport driven by a script.
- Batch import, asset-bundle builds, addressables builds, IL2CPP builds, PlayMode test runs.
- Anything that fires on a timer, a file-system watch, or a hook.

## What Does Not Count

- The user double-clicking the Unity icon on the desktop.
- The user running `unity open .` themselves in a terminal they are reading.
- The user clicking *Play* in an editor they have in front of them.

These are interactive. The flag set does not apply.

## Don'ts

- **Do not** launch Unity bare and assume it will figure itself out. A bare launch opens the GUI, steals focus, and waits for input. On headless servers there is no input to give.
- **Do not** rely on Editor focus, modal dialogs, or a human "OK" click in an automated run. They never come.
- **Do not** pass `-batchmode` without `-quit` unless a follow-up step depends on the editor still being open.
- **Do not** use `xvfb-run` or a virtual display as a substitute for `-nographics`. The flags are cheaper, faster, and unambiguous.
- **Do not** use `~`, `$VAR`, or relative paths in `-projectPath`. Always resolve to an absolute path before invoking the editor.

## Verifying the Run

- Exit code is `0` on success. Non-zero means the run failed; surface it to the caller instead of swallowing it. **Exception — `-runTests`:** Unity states there is no common definition for exit codes from test runs, so judge pass/fail from the `-testResults` NUnit XML and the log, not the exit code alone.
- The log file contains a `BatchMode: Beginning` line and a final summary. Parse for it; do not eyeball a multi-megabyte log.
- For `-executeMethod`, the method is `public static`, returns `void`, and its exit signal is the process exit code, not a return value.
- A run that "completes" with no log and exit code `0` is suspicious — the editor may have skipped the work. Treat silence as failure until proven otherwise.

## Self-check Before Launch

- [ ] The command line includes `-batchmode`, `-quit` (except with `-runTests`), and `-projectPath <absolute>`.
- [ ] `-nographics` is set unless the task depends on rendering.
- [ ] `-accept-apiupdate` is set so API updates do not block.
- [ ] `-logFile` points at a path the caller can read, or `-` for stdout.
- [ ] If a method entry point is used, `-executeMethod Type.Method` names a `public static` method on a type the assembly can resolve.
- [ ] The caller checks exit code and parses the log, instead of "running and trusting".
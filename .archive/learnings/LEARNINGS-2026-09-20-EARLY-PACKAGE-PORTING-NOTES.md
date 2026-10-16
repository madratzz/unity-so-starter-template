# Archived Learnings — early package porting and baseline notes (to 2026-09-20)

Schema version: 1

Archived on 2026-10-17 from `.agents/LEARNINGS.md` (verbatim bullets, immutable). Includes one superseded lesson: the `[SerializeField]` naming note was resolved on 2026-09-20 — PascalCase is now the project-wide rule in `AGENTS.md` ("Unity and Package Work").

---

- **`[SerializeField]` naming is inconsistent across the repo, not a settled convention.** The ported `scriptableobject.*` family (time.machine, statemachine.core, variables.extensions, variables.database) uses PascalCase for serialized private/protected fields (`TickInterval`, `BootState`, `Key`, `Value`); the newer `scriptableobject.architecture` package (ported from asteroids-demo on `feature/architecture`) uses camelCase (`androidTargetFrameRate`, `applicationTimeMachine`, `appPaused`). Do not assume or assert a single project-wide rule without checking the specific package/file first.
- Splitting one embedded package into several is safe when done as: move files (excluding `.meta`), author fresh asmdefs/package.json per concern, re-point consumer package.json + asmdef references in the same pass, delete the old package dir, then `package_resolve` + recompile. Unity regenerates the lockfile and `.meta` GUIDs.
- `TryGetComponent` + `AddComponent` is the correct `GetOrAddComponent` pattern; the archived version discarded `AddComponent`'s return and yielded null on the add path.
- `RuntimeDictionary` in `scriptableobject.variables.extensions` compiles without Odin Inspector because usage is guarded by `#if ODIN_INSPECTOR`; dictionary types are unavailable until Odin is imported.
- The repository baseline now includes the 4 ported `com.madratzz.*` embedded packages; earlier baseline had no tracked C# source files or assembly definitions.
- The existing project README and IDEA document identify the repository as a collection of Unity game-development packages intended for Verdaccio hosting.
- Generated Unity directories (`Library/`, `Temp/`, `Logs/`, and `UserSettings/`) are present locally and are ignored by Git; they are not appropriate locations for durable agent context.
- A concise root `AGENTS.md` can provide harness-neutral operating rules while `.agents/` retains current, project-specific context and `.archive/` retains detailed history.

# Archived Context — package catalog detail and the answered GameEvents question (2026-09-25)

Schema version: 1

Archived on 2026-10-17 from `.agents/CONTEXT.md` (verbatim bullets, immutable) to keep the active file within its 1,200-word limit. The package catalog and dependency graph are also in `Docs/Home.md` and `Docs/Architecture Overview.md`.

---

- `Packages/` — Unity Package Manager manifest and lockfile; the manifest includes URP, Input System, AI Navigation, Test Framework, and Unity AI packages. Sixteen custom embedded packages live under `Packages/` (up from the initial eight): the `com.madratzz.utilities.*` family (`attributes`, `core`, `coroutines`, `ui`, `addressables`, `buildautomation`, `unity.alwaysstartfromscenezero`), `com.madratzz.platform.device`, and the `com.madratzz.scriptableobject.*` (SOAP) family (`variables`, `variables.database`, `variables.extensions`, `eventsystem.core`, `eventsystem.extensions`, `event.variables`, `statemachine.core`, `time.machine`). Utility packages use `com.madratzz.*` assembly names; scriptableobject packages use `madratzz.scriptableobject.*`; namespaces remain the archived `ProjectCore.*` / `CustomUtilities.*` / `ExtensionMethods` mix (cleanup deferred). Full catalog and dependency graph: `Docs/Home.md` / `Docs/Architecture Overview.md` — GameFlow's vault note moved to `Docs/GameFlow (Template Layer).md` (out of `Docs/Packages/`) and no longer describes itself as a 17th package (2026-09-21).
- ~~Should `Assets/GameEvents/` hold hand-authored `GameEvent` assets checked into the repo?~~ **Answered 2026-09-25 (user request):** yes — the template ships wired assets. `Assets/GameEvents`, `Assets/StateMachine` and `Assets/Variables` now hold the MainMenu/Gameplay/Settings/Store screen flow, and both prefabs are pre-wired to them.

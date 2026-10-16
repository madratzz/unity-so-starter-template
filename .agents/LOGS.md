# Active Logs

Last updated: 2026-10-17

## Current Session

### 2026-10-17T01:02:12+05:00 — claude-sonnet-5.5/sync-unity-rules-with-library

Follow-up after PR #21 merged: `madratzz-ai-prompts` `main` moved (a50c3e6, same day) with a fuller harness-agnostic rewrite plus two behaviour changes, so the vendored copies and `AGENTS.md` bullets were stale.

Files touched:

- `.agents/rules/` — re-synced six files from upstream `main` (`fad35b6`); `README.md` provenance updated.
- `AGENTS.md` — skills bullet now uses the official-plugin route for Claude Code/Codex/Grok and the `skills` CLI elsewhere; UI events are subscribed in code (no Inspector `UnityEvent`s); `async void` is never used; documented this repo's test command (`unity command run_tests --mode EditMode` against the live Editor, since an open Editor holds `Temp/UnityLockfile`).
- Source repo (merged): PR #2 adds `-quit`-with-`-runTests` correction; my earlier PR #1 was superseded and closed.

Decisions made:

- Upstream wins on content; this repo only adapts paths. The `-quit` correction was contributed upstream rather than kept as a local edit.

Issues found:

- Two sessions edited the prompts library concurrently; PR #21 had already merged the pre-sync text, hence this follow-up.

Next steps:

- None outstanding for the rules adoption.

### 2026-10-17T00:48:57+05:00 — claude-sonnet-5.5/apply-unity-rules-library

Ran the `madratzz/madratzz-ai-prompts` library in this repo: its rules-applier prompt (merge Unity rules into `AGENTS.md`) and its agent-context bootstrap prompt (idempotent re-run). User decisions: apply every rule file; skip the DI/layered-architecture part for this SOAP repo; make the skills-install rule harness-agnostic so any harness can use it, fixed at the source repo; run the bootstrap prompt fully.

Files touched:

- `AGENTS.md` — new "Unity Rules" section (skills, C# naming, tunables, uGUI prefabs, non-interactive Editor runs, worktree-headless, test delegation and model selection, level design); replaced the "no canonical Unity path" sentence with the verified Hub path and `unity` CLI note; hard word limits for `CONTEXT.md`/`MEMORY.md`/`LEARNINGS.md` (replacing their old line budgets); `.agents/rules/` in the layout table; skills check as Start-of-Work step 8.
- `.agents/rules/` (new, 9 files) — full-text reference copies of 8 rule files plus `README.md`.
- `.agents/CONTEXT.md`, `MEMORY.md`, `LEARNINGS.md` — "Word limit" headers, 2026-10-17 decisions, `MEMORY.md` Unity version corrected from `6000.3.21f1` to `6000.3.24f1`, new `skills` CLI lesson; one long CONTEXT bullet and eight older LEARNINGS bullets archived.
- `.agents/LOGS.md` — trimmed from 808 lines to a current session plus the two 2026-09-22 sessions.
- `.agents/INDEX.md`, `README.md`, `agents/DEFAULT-AGENT.md` — new rows/budgets/rules.
- `.archive/logs/` (2 snapshots), `.archive/learnings/` (2), `.archive/context/` (1) plus their `INDEX.md` files and `.archive/INDEX.md`.
- `Docs/Contributing.md` — replaced a stale "`[SerializeField]` casing not settled" line (settled 2026-09-20) and pointed to the Unity Rules.
- External, not in this repo: local clone of `madratzz-ai-prompts`, branch `fix/harness-agnostic-rules` (one commit, **not pushed**) — harness-agnostic `unity-official-skills-install.md`, Hermes wording removed from the test-delegation, test-model-selection and applier files.

Decisions made:

- `unity-architecture-rules-di.md` not adopted or vendored; `unity-scriptableobject-configs.md` not vendored (only its harness-neutral ideas condensed) because both assume VContainer/`IConfigService` infrastructure this repo deliberately lacks.
- Word limits now govern the three knowledge files; `LOGS.md` keeps its 300–500 line soft budget.
- Level-design paths adapted to `Docs/Levels/`; recorded as an unconfirmed assumption.
- Changes were moved onto `feature/apply-unity-rules-library` cut from `origin/development` (the original branch was 7 commits behind). Development's 2026-09-25 session, six learnings and two open questions were merged in; that pushed `LEARNINGS.md` to 1,865 words, so six older bullets were archived (`LEARNINGS-2026-09-25-…`) to land at 1,354/1,500.
- Before the move, nothing was committed or switched: the working branch `feature/playmode-test-run-guard` carries unrelated work and an unrelated modified `ProjectSettings/ProjectSettings.asset`, left untouched.

Issues found:

- The library's editor-flag rule required `-quit` universally, contradicting Unity's docs (`-quit` is unsupported while tests run; no common exit codes for test runs). Resolved in favour of Unity's docs in `AGENTS.md`, the vendored copy, and the source-repo branch.
- The first draft of the skills-install rule said `--help` lists valid agent ids; it does not. Fixed after probing the CLI (`skills list -a <bad>` prints `Valid agents: …`).
- `Docs/Contributing.md` contradicted `AGENTS.md` on `[SerializeField]` casing; fixed.
- The old `LOGS.md` mixed ISO and "PST" timestamps and was not strictly newest-first; archived verbatim, not re-sorted.

Next steps:

- Review the diff, then commit on a new `feature/…` branch from `development` per `AGENTS.md`.
- Decide whether to push `fix/harness-agnostic-rules` in the prompts repo.
- Confirm the `Docs/Levels/` path when a game first needs level docs.

## Recent Previous Sessions

### 2026-09-25T18:24:30+05:00 — claude-opus-5/variables-extensions-undeclared-dependency

Summary of what was done:

- **Swept the project for compiler errors and package issues.** Result: **no evidence of current compiler errors** — all 126 assemblies in this project's `Library/ScriptAssemblies` (including `Unity.Addressables.Editor.dll` and every `com.madratzz.*`) carry one uniform 18:08 build timestamp, which a failed compile would not produce. Not confirmed by an actual compile: see the tooling caveat below.
- **`~/AppData/Local/Unity/Editor/Editor.log` is NOT this project's log.** Its `COMMAND LINE ARGUMENTS` block reads `-projectpath E:\UnityProjects\PersonalProjects\unity-di-starter-template`. Several Editors run concurrently on this machine and they contend for that single log path, so it belonged to the sibling DI template (and had rotated from 8.1 MB to 36 KB within the hour). The ~60 Addressables `error CS0246` lines analysed there (`IBundleWriteData` / `IBuildTask` / `InjectContext` / `BundleDetails`) describe **that** project, not this one. Always check the `-projectpath` line before reading `Editor.log`.
- **Found and fixed one real package defect**: `com.madratzz.scriptableobject.variables.extensions` declared **no** `dependencies` in `package.json`, while both of its asmdefs referenced `madratzz.scriptableobject.variables.runtime` — the only undeclared cross-package dependency in the repo. On Verdaccio this package would install standalone and then fail to resolve that assembly reference.
- **Fixed by removing the two dead references, not by declaring the dependency** — the extensions package uses **zero** types from the variables package (verified case-sensitively against its full export set: `Bool`, `Float`, `Int`, `String`, `IVariable`, `IApplyChange`). The two packages only share the `ProjectCore.Variables` namespace, which is what made the reference look load-bearing. `package.json`, the asmdefs, and the code now all agree that the package is standalone.

Files touched:

- `Packages/com.madratzz.scriptableobject.variables.extensions/Runtime/madratzz.scriptableobject.variables.extensions.runtime.asmdef`
- `Packages/com.madratzz.scriptableobject.variables.extensions/Tests/EditMode/com.madratzz.scriptableobject.variables.extensions.tests.asmdef`
- `Docs/Packages/SOAP - Variables Extensions.md`
- `.agents/LOGS.md`, `.agents/LEARNINGS.md`

Decisions made:

- Removed the dead asmdef references rather than adding `com.madratzz.scriptableobject.variables` to `package.json`. Declaring it would have recorded a dependency the code provably does not have, and `AGENTS.md` prefers clear boundaries over convenience coupling. Trivially reversible if the package is later meant to build on the variables types.
- Left the other flagged references alone in this commit — see the corrected analysis below; most of them are **not** dead, and the genuinely dead ones are a separate reviewable change.
- Did not touch the unrelated working-tree changes (`ProjectSettings/EditorBuildSettings.asset`, `ProjectSettings/ShaderGraphSettings.asset`, untracked `.vscode/`), per `AGENTS.md`'s "do not stage unrelated modifications".

Issues found (not fixed here):

- **A first pass claimed "11 dead asmdef references that over-declare the published graph". That was wrong and is retracted.** A type-name usage scan cannot see references that are load-bearing through an *inheritance chain*: if a consumer uses type `X` from assembly A and `X`'s base type lives in assembly B, the consumer must reference B too or the compiler raises **CS0012**. Re-checked with base types and member signatures in view, the 12 flagged candidates break down as:
  - **4 are load-bearing and must keep their reference** — `time.machine.runtime` → `utilities.core` (it calls `CoroutineHandler`, and `CoroutineHandler : SingletonPersistent<CoroutineHandler>` with `SingletonPersistent<T>` living in *core*); `event.variables.tests` → `variables` (`BoolWithEvent : Bool`) and → `variables.database` (`DBBoolWithEvent : DBBool`); `variables.database.tests` → `variables` (`DBInt : Int`). Their `package.json` declarations are **correct**, not over-declared.
  - **2 were the real defect**, fixed in this commit (`variables.extensions`, whose types derive from `ScriptableObject`/Odin rather than from the variables package — which is exactly why it was the one genuine case).
  - **6 are genuinely dead**: `eventsystem.extensions.runtime` and `.tests` → `eventsystem.core` (the extensions define their own `GameEventWithParam<T> : ScriptableObject` rather than building on `GameEvent`), and `time.machine.tests` / `.playmodetests` → `utilities.core` and `utilities.coroutines` (the tests touch only `TimeMachine`, whose base is `ScriptableObject` and whose public surface exposes no type from either).
  - The only genuine **over-declaration** candidate left is `eventsystem.extensions` → `eventsystem.core` in `package.json`; keeping it is still defensible since consumers of the extensions will normally want core too.
- **12 packages ship EditMode tests referencing `UnityEngine.TestRunner`/`UnityEditor.TestRunner` but none declares `com.unity.test-framework`** in `package.json`. Consistent across the family, so it reads as convention rather than oversight, but Unity's own packages (e.g. `com.unity.addressables`) do declare it. Worth a deliberate decision before first Verdaccio publish.
- `ProjectSettings/EditorBuildSettings.asset` + `ShaderGraphSettings.asset` are modified on disk by the running Editor, still unresolved alongside the `ProjectSettings.asset` item logged on 2026-09-22.

Verified (2026-09-26, after the Editor was closed):

- **EditMode suite: 124/124 passing, 0 failed, 0 skipped** via `unity test . --mode EditMode`. The three assemblies whose references this branch removes all compiled and passed — `eventsystem.extensions.tests` (8), `time.machine.tests` (3), `variables.extensions.tests` (10) — confirming none of the six removals raises CS0012.
- The earlier "not compile-verified" caveat is resolved and retracted.

Tooling note (why it could not be verified at the time):

- A running Editor holds `Temp/UnityLockfile`, so `unity test` refuses outright (*"already open in a running Editor (PID …). Close it and run the command again"*) and a headless `-batchmode` compile of the same project is impossible. Closing the Editor is the fix — `unity test` then drives its own batchmode instance.
- The CLI could not reach the running Editor either, but **not** for the reason first recorded: `com.unity.pipeline` had never been downloaded into this project at all (present in `manifest.json` and `packages-lock.json`, absent from `Library/PackageCache`), so there was no Pipeline server to reach. The port collision with the DI template's Editor was real but incidental. The package finally downloaded during the first batchmode run, so the CLI should reach this project's Editor from now on.

Next steps:

- Decide whether `eventsystem.extensions` should keep its `eventsystem.core` `package.json` dependency — the one remaining genuine over-declaration, now that the asmdef reference behind it is gone. (The 6 dead references themselves were removed in `f31c0e2`.)
- Decide the `com.unity.test-framework` declaration convention for packages with tests.

### 2026-09-22T15:16:44+05:00 — claude-opus-5/playmode-test-run-guard

Summary of what was done:

- **Fixed the `alwaysstartfromscenezero` PlayMode-test hang at its root.** `PlayFromFirstScene` now records at `ExitingEditMode` whether the active scene is the Unity Test Framework's bootstrap scene, and `LoadFirstSceneAtGameBegins` skips the scene-0 load for that session. The flag goes through `SessionState`, which survives the domain reload into Play Mode and is cleared when the Editor exits. The manual `EditorPrefs.SetBool(..., false)` ritual is no longer needed and has been removed from LEARNINGS.
- **Kept the fix reference-free.** Detection matches UTF's bootstrap-scene name/path (`Assets/InitTestScene<guid>.unity`) and, as a second signal, a scene-resident GameObject named `Code-based tests runner`. No `UnityEditor.TestRunner` reference and no `versionDefines` were added, so `com.unity.test-framework` does not become a dependency of a package that has nothing to do with testing.
- **Caught and fixed a self-inflicted regression during verification.** The first cut used an unscoped `GameObject.Find("Code-based tests runner")`. UTF leaves a detached `HideFlags.DontSave` object of that name alive for the rest of the Editor session, so after one PlayMode run every ordinary Play Mode session was misdetected as a test run and scene 0 stopped loading — the package's entire feature, silently off. Scoping the check with `controller.scene.IsValid()` fixed it.

Verification (live Editor on port 7800, `EditorUtilities/Always Start From Scene 0` left **enabled** throughout):

| Scenario | Result |
| --- | --- |
| PlayMode run, guard reverted (control) | Hung — `playMode: "playing"` indefinitely, BootstrapScene logs proved scene 0 replaced the test scene, `TestResults.xml` never written, `Assets/InitTestScene<guid>.unity` stranded |
| PlayMode run, with guard | 4/4 passed in ~10s, detector flag `True`, UTF cleaned up its bootstrap scene |
| Normal Play from `GameScene` | Loaded `BootstrapScene` (scene 0), detector flag `False` — feature intact |
| Normal Play immediately after a PlayMode run | Loaded `BootstrapScene` — the regression above no longer reproduces |
| EditMode suite | 124/124 passed |

Files touched:

- `Packages/com.madratzz.utilities.unity.alwaysstartfromscenezero/Editor/PlayFromFirstScene.cs`
- `Packages/com.madratzz.utilities.unity.alwaysstartfromscenezero/{CHANGELOG.md,README.md}`
- `.agents/LOGS.md`, `.agents/LEARNINGS.md`

Decisions made:

- Read the signal at `ExitingEditMode` and stash it in `SessionState` rather than reading it at `BeforeSceneLoad`, so the check runs while the active scene is unambiguously readable and does not depend on what is valid during the runtime-init window.
- Rejected `Application.isBatchMode` — this repo runs tests against a live Editor over the Pipeline, where it is `false`.
- Rejected adding `UnityEngine.TestRunner`/`UnityEditor.TestRunner` to the package asmdef, and rejected `versionDefines` as unnecessary once detection was reflection-free: both would pull a test-framework dependency into a general-purpose utility package.
- Left the `EditorUtilities/Always Start From Scene 0` EditorPref **enabled** in this Editor (I set it during verification; it is user-local, not repo state). That is now the safe default and no longer needs toggling before a test run.

Issues found:

- **`unity command run_tests --mode PlayMode` is a no-op on Pipeline `0.7.0-exp.1`** — returns `0/0 passed (0s)` in ~2s without entering Play Mode, while `list_tests --mode PlayMode` finds all 4 tests and EditMode works normally. All PlayMode verification here had to go through `TestRunnerApi` via `eval_file`. Worth reporting upstream; recorded in LEARNINGS.
- Force-stopping the hung control run left `Assets/InitTestScene<guid>.unity` on disk, and deleting it while UTF still held the job wedged the Editor main thread. Needed a `pkill` + `unity open .` restart. Recorded in LEARNINGS.
- `ProjectSettings/ProjectSettings.asset` still carries the unrelated Unity-generated `Android:` define change from the previous session. Left unstaged per AGENTS.md; still awaiting a decision.
- `.agents/LOGS.md` is 764 lines / ~78 KB against the 300–500 line / ~20 KB soft budget, and the archive trigger in AGENTS.md has fired. **Not archived here** — a ~700-line archive migration does not belong in a single-file bug fix, and folding it in would break "keep changes reviewable". It needs its own commit.

Next steps:

- Archive `.agents/LOGS.md` into `.archive/logs/` and trim the active file (pre-existing debt, own commit).
- Decide what to do with the stray `ProjectSettings.asset` define change.
- Correct the `.agents/LEARNINGS.md` claim that "EditMode tests never receive Unity lifecycle callbacks" — it is false for `ScriptableObject`, whose `OnEnable` fires on `CreateInstance`, asset load, and every domain reload (15 such types in this repo mutate state there).

### 2026-09-22T01:34:41+05:00 — deepseek-v4.1-flash/editmode-test-failures

Summary of what was done:

- **Merged `main` into `development`** (commit `1eab38f`). Resolved `Packages/manifest.json` (kept `development`'s side — `main`'s only edits, `com.unity.timeline` 1.8.13 and `com.unity.collab-proxy` 2.13.6, were already present there) and `README.md` (kept `development`'s packages + GameFlow content, retitled to `unity-so-starter-template` with the clone URL and `cd` updated). `ProjectSettings.asset` auto-merged. `main` is now an ancestor of `development`; both are pushed.
- **Fixed CS0104 compile errors** in `SearchableAssetFinder.cs` / `SearchableAssetDropdown.cs` — `using System` plus `using UnityEngine` made a bare `Object` ambiguous between `System.Object` and `UnityEngine.Object`. Fixed with an explicit `using Object = UnityEngine.Object;` alias rather than dropping either namespace.
- **Repaired a UPM download failure**: `com.unity.sysroot.base` failed to download (`Cannot connect to 'download.packages.unity.com'`, ECONNRESET) so it never landed in `Library/PackageCache`, which broke the dependent Linux toolchain packages' `Editor/` scripts and failed the whole compile. Host was genuinely flaky (~1 in 6 connections reset) and UPM does not retry, so forced a re-resolve by toggling `manifest.json` content in a loop; the package then downloaded and the build went green.
- **Upgraded the Unity CLI and the Pipeline package.** CLI was already latest at `1.0.0-beta.10` (confirmed against both the Homebrew cask and Unity's own beta CDN manifest). Pipeline went `0.6.0-exp.1` → `0.7.0-exp.1`.
- **Diagnosed and fixed all six failing EditMode tests** (see the three commits on this branch). Every root cause was verified against the running Editor with `eval_file` probes rather than inferred; details in the commit message for `b36f96a`. Full suite is now **124/124 passing**.

Files touched:

- `Packages/manifest.json`, `Packages/packages-lock.json`
- `Packages/com.madratzz.utilities.attributes/Editor/{SearchableAssetFinder,SearchableAssetDropdown}.cs`
- `Packages/com.madratzz.utilities.attributes/Tests/EditMode/{RequiredReferenceValidatorTests,SearchableAssetFinderTests}.cs`, `.../Tests/EditMode/com.madratzz.utilities.attributes.tests.asmdef`
- `Packages/com.madratzz.utilities.attributes/Tests/Fixtures/` (new: `com.madratzz.utilities.attributes.fixtures.asmdef` + `SearchableAssetFinderTestAsset.cs`)
- `Packages/com.madratzz.scriptableobject.eventsystem.core/Tests/EditMode/GameEventMonoTests.cs`
- `.agents/LOGS.md`, `.agents/LEARNINGS.md`

Decisions made:

- Kept `development`'s `README.md` content and retitled it, rather than keeping `main`'s empty-template README — the branch reality (16 packages, GameFlow layer, editor 6000.3.24f1) contradicted the template README.
- Re-downloaded the missing `com.unity.sysroot.base` instead of removing the Linux toolchain packages, which would have papered over a transient network fault.
- Installed Pipeline `0.7.0-exp.1` by explicit version because `unity pipeline upgrade` misreports `alreadyLatest`; see LEARNINGS.md.
- Put the `SearchableAssetFinder` test fixture in its own **non-test** Editor assembly rather than adding a reference to another `com.madratzz.*` package (which would couple two independent packages) or using a built-in type (no suitable one exists; see LEARNINGS.md).
- Invoked `OnEnable` by reflection in `GameEventMonoTests` rather than adding `[ExecuteAlways]` to the components — that would raise their GameEvents inside the Editor.

Issues found:

- `unity pipeline upgrade` returns `alreadyLatest: true` while a newer version is published. Workaround: `unity pipeline list-versions`, then `unity pipeline install --package-version <v>`.
- `ProjectSettings/ProjectSettings.asset` has an **unrelated** Unity-generated modification on disk (adds `Android: SENTIS_ANALYTICS_ENABLED;APP_UI_EDITOR_ONLY` to `scriptingDefineSymbols`, presumably written when a package registered its version defines). Left unstaged and uncommitted per `AGENTS.md`'s "do not stage unrelated modifications"; flagging for a deliberate decision.
- `AGENTS.md`, `IDEA.md`, `Docs/Home.md` and `.agents/CONTEXT.md` still call the project `unity-packages` after the rename to `unity-so-starter-template` — pre-existing staleness, not touched here.

Next steps:

- Decide what to do with the stray `ProjectSettings.asset` define change.
- Optionally fold the stale `unity-packages` naming into a docs pass.

## Archive Summary

- 2026-09-16 to 2026-09-21 (11 sessions): agent-context system v2 (uppercase filenames, ISO archive names), editor bump to `6000.3.24f1`, Obsidian `Docs/` vault, GameFlow FSM wiring and its move from a package into the `Assets/` template layer, `[RequireReference]` + `RequiredReferenceValidator`, global searchable `ScriptableObject` dropdown.
- 2026-08-15 to 2026-08-17 (13 sessions): initial context-system setup and the first ports of the `com.madratzz.*` packages.

## Archive Pointers

- [LOGS-2026-09-21-GAMEFLOW-TEMPLATE-AND-VAULT-WORK.md](../.archive/logs/LOGS-2026-09-21-GAMEFLOW-TEMPLATE-AND-VAULT-WORK.md)
- [LOGS-2026-08-17-INITIAL-PACKAGE-BASELINE-SETUP.md](../.archive/logs/LOGS-2026-08-17-INITIAL-PACKAGE-BASELINE-SETUP.md)
- [Archived Logs Index](../.archive/logs/INDEX.md)

Logs are kept newest first, oldest last.

# Active Logs

Last updated: 2026-10-17

## Current Session

### 2026-10-17T01:09:05+05:00 — claude-sonnet-5.5/merge-sweep-into-development

Merge sweep requested by the user. Merged PR [madratzz/unity-so-starter-template#19](https://github.com/madratzz/unity-so-starter-template/pull/19) (screen-flow states, views, HUD/settings variable bindings, `DBInt`/`DBBool` `DefaultValue` fix) into `development` with a merge commit (`60bbe26`, parents `8549afd/6732661`). Earlier in the day #21 and #22 (Unity rules adoption and re-sync) were merged the same way. No CI checks are configured on the repo (0 reported).

Files touched:

- `.agents/LOGS.md`, `.agents/CONTEXT.md`, `.agents/INDEX.md`, `.archive/context/` — conflict resolution on the #19 branch: kept `development`'s trimmed `LOGS.md`/`INDEX.md` and re-inserted the branch's six session entries; archived the `Packages/` catalog bullet and the answered GameEvents question (`CONTEXT-2026-09-25-…`) so `CONTEXT.md` stays at 1,146/1,200 words.

Decisions made:

- Resolved the #19 conflicts on its branch rather than forcing them (they were only append-only log and context files).
- PR #20 (`development` → `main` release) was not touched; a release is the user's call.

Issues found:

- Post-merge EditMode run not performed: a Unity Editor holds the project, so batch mode cannot open it, and the sweep did not disturb it. #19's own log entries record an EditMode run before merge.

Next steps:

- Run `unity command run_tests --mode EditMode` against `development` when convenient.
- Decide when to release `development` to `main` (PR #20).

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

### 2026-09-26T03:20:00+05:00 — claude-opus-5/db-defaultvalue-fallback

Summary of what was done:

- **Fixed the `DB*` `DefaultValue` fallback** raised in the previous entry (user approved the change). `DBInt`, `DBBool`, `DBFloat` and `DBString` fell back to a hard `0` / `false` / `string.Empty` whenever nothing was saved **and** `ResetToDefaultOnPlay` was false, silently discarding the author's `DefaultValue`. With nothing saved, `Load()` now always uses `DefaultValue`; a saved value still wins over it. `DBEpochTime` inherits the fix from `DBInt`.
- Two regression tests in `DBIntTests` cover both halves: no saved value → `DefaultValue`, and a saved value → the saved value wins.
- Documented in the package CHANGELOG and README, including the consequence that `ResetToDefaultOnPlay` no longer affects the DB load path at all.

Files touched:

- `Packages/com.madratzz.scriptableobject.variables.database/Runtime/DBVariables/{DBInt,DBBool,DBFloat,DBString}.cs`
- `.../Tests/EditMode/DBIntTests.cs`, `.../CHANGELOG.md`, `.../README.md`

Decisions made:

- **Scoped strictly to the fallback.** `ResetToDefaultOnPlay` could arguably also mean "ignore the saved value and reset on every play", which would be a second and larger semantic change affecting anyone relying on a saved value winning. Not done — the user asked for the fallback, and that is a separate decision.
- Both branches of the old inner `if` now collapse to `Value = DefaultValue`, so the `if` was removed rather than left as dead structure, with a comment explaining why `ResetToDefaultOnPlay` is deliberately not consulted there.

Verification:

- **Reproduced the original symptom, then confirmed the fix at runtime.** Cleared the PlayerPrefs keys to simulate a genuine first run, entered play and opened Settings: the vibration toggle now comes up **ticked** (`v_VibrationEnabled = True`, `DefaultValue 1`) where it previously came up unticked, and `v_CurrentLevel` loads **1** where it previously loaded 0.
- **EditMode 149/149** (147 + 2 regression tests), zero console errors.
- Also cleared the stale PlayerPrefs this session's earlier testing had written (`settings.vibration`, `store.coins`, `app.pausedTime`), so the local project no longer carries 12 coins from a probe.

Next steps:

- Decide whether `ResetToDefaultOnPlay: true` should additionally discard a *saved* value on play — currently the saved value always wins, which makes the flag inert for `DB*` types.
- Consume the volumes (`AudioMixer`); `v_Gems` and `v_HighScore` still have no UI.


### 2026-09-26T02:50:00+05:00 — claude-opus-5/settings-variable-binding

Summary of what was done:

- **Bound the Settings screen two-way.** `SettingsView` now carries two volume sliders and a vibration toggle wired to the ScriptableObject variables, replacing a screen that had only Back/Home buttons.
- **Added `VariableSlider` and `VariableToggle`** (`Assets/Runtime/UI`) — unlike the read-only `VariableLabel` these go both ways: the control writes the variable, and a change from anywhere else moves the control.
- **Added `FloatWithEvent`** to `event.variables`; the package had no eventing `Float` at all.
- **Upgraded three variables in place** (GUIDs preserved): `v_MusicVolume`/`v_SfxVolume` → `FloatWithEvent`, `v_VibrationEnabled` → `DBBoolWithEvent`, each raising a new `e_*Changed` event.

Files touched:

- New: `Assets/Runtime/UI/{VariableSlider,VariableToggle}.cs`, `Packages/.../Runtime/FloatWithEvent.cs`, `Assets/GameEvents/{e_MusicVolumeChanged,e_SfxVolumeChanged,e_VibrationChanged}.asset`
- Modified: `ViewPrefabBuilder.cs` (`BuildSettingsView`, `MakeSlider`, `MakeToggle`, `AddButton`, `BindSlider`, `BindToggle`), `Assets/UI/SettingsView.prefab`, the three variable assets, event.variables tests/README/CHANGELOG, `Assets/Runtime/README.md`, `Docs/*`

Decisions made:

- **Write back with `SetValueWithoutNotify` / `SetIsOnWithoutNotify`.** This is the whole trick to two-way binding: a plain `slider.value =` fires `onValueChanged`, writes the variable, raises its event, refreshes the slider and loops. Using the no-notify setter removes the cycle structurally rather than with a re-entrancy flag.
- `OnSliderMoved` also skips the write when the value has not actually moved (`Mathf.Approximately`) — `SetValue` on a `*WithEvent` variable raises an event, and doing that on every pixel of a drag wakes every listener for nothing.
- Binders are typed against the base classes (`Float`, `Bool`), so they accept the plain, DB and WithEvent variants interchangeably.
- `SettingsView` got its own builder method rather than extending the generic `BuildView`, since a uGUI `Slider` needs a Background + Fill Area/Fill + Handle Slide Area/Handle hierarchy with `fillRect`/`handleRect` wired, and a `Toggle` needs Background + Checkmark with `graphic` wired.

Verification:

- **Drove all three directions live**: dragging the music slider 1.00 → 0.25 wrote `v_MusicVolume` = 0.25; `SetValue(0.4)` on `v_SfxVolume` from code moved its slider to 0.40; flipping the toggle wrote `v_VibrationEnabled` = true. No feedback loop.
- **EditMode 147/147** (144 + 3 new `FloatWithEvent` tests), zero console errors, and the play-mode writes did not leak into the committed assets.

Bug found, NOT fixed — needs a decision:

- **The `DB*` variables ignore `DefaultValue` when no saved value exists.** `DBBool.Load()` (and `DBInt`, `DBFloat`, `DBString`) does: if the PlayerPrefs key is missing and `ResetToDefaultOnPlay` is false, set the value to a hard `false`/`0`/`string.Empty` rather than to `DefaultValue`. So a first-run user gets the type's zero, not the author's default.
- Two shipped variables are visibly wrong because of it: `v_VibrationEnabled` (`DefaultValue: 1`) loads **false**, and `v_CurrentLevel` (`DefaultValue: 1`) loads **0**. Confirmed at runtime — the vibration toggle came up unticked despite its default.
- `ResetToDefaultOnPlay: false` should mean "prefer the saved value", not "ignore DefaultValue even when nothing is saved". The fix is one line per type (`else Value = DefaultValue;`) but it changes semantics for every consumer of four distributed types, so it is left for the user to decide.

Next steps:

- Decide on the `DB*` `DefaultValue` fallback above.
- Consume the volumes: they broadcast changes but nothing listens — an `AudioMixer` binding is the obvious next step.
- `v_Gems` and `v_HighScore` remain unbound (no UI shows them yet).


### 2026-09-26T02:10:00+05:00 — claude-opus-5/hud-variable-binding

Summary of what was done:

- **Bound the HUD's SCORE/COINS labels to the ScriptableObject variables** instead of static placeholder text, so the variables shipped on 2026-09-25 now do real work.
- **Added `VariableLabel`** (`Assets/Runtime/UI`): writes an `Int` into a UI `Text` using a format string, subscribing to a `GameEvent` for refreshes. Typed as `Int`, so it accepts `Int`, `DBInt`, `IntWithEvent` and `DBIntWithEvent` alike.
- **Added `IntWithEvent` to `com.madratzz.scriptableobject.event.variables`** — the set had `BoolWithEvent` for plain `Bool` and `DBIntWithEvent` for persistent `Int`, but nothing for a plain session-only `Int`, which is exactly what a score is. Overrides `ApplyChange` as well as `SetValue`.
- **Upgraded the two variables in place**: `v_Score` → `IntWithEvent` raising `e_ScoreChanged`, `v_Coins` → `DBIntWithEvent` raising `e_CoinsChanged`. Retargeted via `SerializedObject` so the asset GUIDs (and every existing reference) survive; `Value`, `DefaultValue`, `ResetToDefaultOnPlay` and `Key` all verified intact afterwards.

Files touched:

- New: `Assets/Runtime/UI/VariableLabel.cs`, `Packages/com.madratzz.scriptableobject.event.variables/Runtime/IntWithEvent.cs`, `Assets/GameEvents/{e_ScoreChanged,e_CoinsChanged}.asset`
- Modified: `ViewPrefabBuilder.cs` (`BindLabel`, `UpgradeVariableToEventing`), `Assets/UI/GameHudView.prefab`, `Assets/Variables/{Gameplay/v_Score,Store/v_Coins}.asset`, both asmdefs, event.variables tests + README + CHANGELOG, `Assets/Runtime/README.md`, `Docs/GameFlow (Template Layer).md`, `Docs/Packages/SOAP - Event Variables.md`

Decisions made:

- **The label subscribes to a `GameEvent` asset wired on both sides**, rather than casting the variable to a `*WithEvent` type and calling `AddListener`. That keeps `VariableLabel` working with a plain `Int` whose event someone else raises, and keeps the variable ignorant of the UI.
- **No polling.** With no event assigned the label reads once on enable and then stays put — deliberate, so a static readout is not forced into `Update`. `Refresh()` also skips the `string.Format` allocation when the value has not moved.
- `UnityEngine.UI` had to be added to the `Assets/Runtime` asmdef (first use of `Text` there), and `eventsystem.core.runtime` to the bootstrap editor asmdef (first use of plain `GameEvent`; it previously only touched `GameEventWithInt`).

Verification:

- **Drove it live**: entered gameplay, then `ApplyChange` on each variable. `SCORE 0 → 250 → 263` and `COINS 7 → 12`, each label repainting off its event with no polling. The initial read was also proven real — the HUD showed `COINS 7` from a previously persisted PlayerPrefs value rather than the prefab's baked-in `0`.
- **EditMode 144/144** (141 + 3 new `IntWithEvent` tests), zero console errors.
- Confirmed the play-mode writes did **not** leak into the committed assets: both are back at `Value: 0` with all other fields intact.

Gotchas found:

- **Escaped quotes inside an `eval_file` probe's return string corrupt the parsed result.** A probe returning `name="value"` came back truncated at the first `\"` and read as an empty label, which looked like the binding had failed. Return delimiters that survive JSON, e.g. `[...]`.
- An asmdef referencing `eventsystem.extensions.runtime` does **not** transitively give you `eventsystem.core.runtime`, so plain `GameEvent` fails to resolve with CS0246 while `GameEventWithInt` compiles fine. Same trap as the inheritance-chain CS0012 case logged on 2026-09-25.
- Inserting a test method by line number lands inside the previous method if you target its closing brace; check the brace balance after any awk/sed insertion into C#.

Next steps:

- The local PlayerPrefs `store.coins` holds 12 from this session's testing — harmless and machine-local, but clear it if a fresh-looking demo is wanted.
- Bind the Settings screen's sliders/toggles to `v_MusicVolume`, `v_SfxVolume` and `v_VibrationEnabled` the same way (would need a `VariableSlider` / `VariableToggle`, plus `FloatWithEvent` for the volumes).


### 2026-09-26T01:30:00+05:00 — claude-opus-5/gamestate-scene-and-hud

Summary of what was done:

- **Gameplay is now a scene + HUD, not a full-screen menu.** Added `GameState : UIViewState` (`Assets/Runtime/UI`) which loads `GameScene` **additively** before showing its view and unloads it on `Exit`. `GameplayState.asset` was converted to this type in place (GUID preserved), with `SceneName = GameScene`.
- **Replaced `GameplayView` with `GameHudView`** — a transparent HUD (top stat bar with SCORE/COINS placeholders, compact buttons bottom-right) rather than an opaque full-screen panel, so the game scene shows through.
- **Made `GameScene` loadable**: added it to Build Settings (index 1; `BootstrapScene` stays at 0 so `alwaysstartfromscenezero` still boots correctly) and stripped its `Main Camera` — the boot scene's camera/AudioListener are the persistent pair, and a second set renders twice and logs "there are 2 audio listeners in the scene".
- **Fixed a pre-existing FSM bug this exposed** (see below).

Files touched:

- New: `Assets/Runtime/UI/GameState.cs`, `Assets/UI/GameHudView.prefab` (replaces `GameplayView.prefab`)
- Modified: `Assets/Editor/ProjectBootstrap/ViewPrefabBuilder.cs` (HUD builder, per-state script targeting, Build Settings + camera steps), `Assets/StateMachine/States/GameplayState.asset`, `Assets/Scenes/GameScene.unity`, `ProjectSettings/EditorBuildSettings.asset`, `Packages/com.madratzz.scriptableobject.statemachine.core/{Runtime/StateMachine/FiniteStateMachine.cs,Tests/EditMode/FiniteStateMachineTests.cs,CHANGELOG.md}`, `Assets/Runtime/README.md`, `Docs/GameFlow (Template Layer).md`

Decisions made:

- **Additive load, never single.** `ApplicationBase` and `ApplicationFlowController` live in the boot scene and are *not* `DontDestroyOnLoad`, so a single-mode load would destroy the FSM owner mid-transition and strand the whole flow. `GameState` also refuses to unload the last remaining scene.
- **Scene loads before the HUD is created** so the HUD is instantiated last and draws on top; on exit the HUD is destroyed *before* the scene, so it can never reference destroyed scene objects.
- `GameState` guards on `Application.CanStreamedLevelBeLoaded` and logs a pointed error rather than silently showing a HUD over an empty boot scene when the scene is missing from Build Settings.

Bug found and fixed (pre-existing, in `statemachine.core`):

- **`FiniteStateMachine` never reset its runtime state, so only the first Play worked.** A ScriptableObject is an asset: its `[NonSerialized]` fields survive exiting play mode in the Editor. A leftover `CurrentState` made the next `Tick()` skip the boot block entirely (`if (CurrentState == null)`), so the boot state was never re-entered and nothing it does — loading a scene, showing a view — ever happened. Symptom: press Play, works; Stop; press Play again, blank screen; until a script recompile happened to clear it. Latent until now only because the states did nothing visible.
- Fixed with an `OnDisable` reset plus a public `ResetRuntimeState()`, and three regression tests. **141/141 EditMode passing.**

Verification:

- Drove **two consecutive play sessions** (the case that used to fail) with polling between every step. Both identical: boot → `MainMenuState` + `MainMenuView`; Play → `GameplayState`, `scenes=[BootstrapScene GameScene]`, `GameHudView` shown; Home → back to `MainMenuState` with `GameScene` unloaded. Settings over gameplay keeps `GameScene` loaded and hides the HUD.
- EditMode suite **141/141**, zero console errors.

Gotchas found:

- **Do not probe the live Editor with fixed sleeps right after `editor_play`.** Entering play triggers a domain reload during which `eval_file` returns empty, which reads as "the object does not exist". An earlier run reported `views=[]` at boot purely for this reason and looked like a regression. Poll for the expected condition instead.
- `perl -0pi` without correct encoding flags double-encodes UTF-8 (an em dash became `C3 A2 C2 80 C2 94`). Prefer the Edit tool for prose, and check with `grep -P '\xc3\xa2\xc2\x80'` after any perl pass over Markdown.
- In an FSM `Tick()` iterator, one `MoveNext()` only reaches the first `yield return CurrentState.Init(...)`; `Execute` needs a further step. The test file's `Advance(it, 2)` helper exists for this — a new test that used a bare `MoveNext()` failed on `ExecuteCount` until corrected.

Next steps:

- Replace placeholder prefabs with real screens (import TMP first).
- Bind the HUD's SCORE/COINS labels to `v_Score` / `v_Coins` instead of static text.
- Author a LevelFail screen if wanted; `LevelFailTransition` is still unassigned.


### 2026-09-26T00:55:00+05:00 — claude-opus-5/screen-flow-ui-views

Summary of what was done:

- **Fixed "runs but sticks on BootstrapScene".** User reported the game never leaves the boot scene and that the screen states had no views. Both correct. The 2026-09-25 commit wired the *assets* to each other but gave them no behaviour: the four screen states were bare `State` instances whose `Init`/`Execute`/`Tick` are all `yield break`, so the FSM entered `MainMenuState` and sat there forever. The previous session's claim that it shipped "a working screen graph" was an overstatement — it was an inert one.
- **Added the missing layer** (`Assets/Runtime/UI`): `UIViewState : State` instantiates its `ViewPrefab` on `Execute`, hides on `Pause`, re-shows on `Resume` and destroys on `Exit`; `UIView` sits on the prefab and reports dismissal via `Close(int reason)` raising a `GameEventWithInt` carrying a `UICloseReasons`. Views never navigate — the controller resolves `(context, reason)` into the next transition, so screens stay ignorant of each other.
- **Built four placeholder uGUI prefabs** (`Assets/UI`) via a re-runnable editor tool, **Tools → Project Bootstrap → Build Screen Views** (`Assets/Editor/ProjectBootstrap/ViewPrefabBuilder.cs`), which also converts the four `State` assets to `UIViewState` **in place** (retargeting `m_Script` through `SerializedObject`, so the asset GUIDs survive and every Transition + the FSM's `BootState` keep resolving) and adds the missing `EventSystem`.
- **Closed a gap left by the previous session**: there was no `e_GameplayViewClosed` event or matching controller field, so Gameplay's buttons would have had nothing to raise. Added both.

Files touched:

- New: `Assets/Runtime/UI/{UIView,UIViewState}.cs`, `Assets/Editor/ProjectBootstrap/{ViewPrefabBuilder.cs,com.madratzz.projectbootstrap.editor.asmdef}`, `Assets/UI/*.prefab` (4), `Assets/GameEvents/e_GameplayViewClosed.asset`
- Modified: `ApplicationFlowController.cs` (+`GameplayViewClosed`), the 4 State assets, `ApplicationFlowController.prefab`, `BootstrapScene.unity` (EventSystem), `Assets/Runtime/README.md`, `Docs/GameFlow (Template Layer).md`

Decisions made:

- **Views are prefabs instantiated by the state**, not scene objects toggled active and not additive scenes (user chose this from three options). It maps onto the FSM lifecycle already in place and makes the `PausesPreviousState` overlay semantics work without extra machinery.
- **Types live in `Assets/Runtime`**, not a new package (user's choice) — template glue for now, promotable later.
- **Legacy `UnityEngine.UI.Text`, not TextMeshPro.** TMP's essential resources are not imported in this project, so TMP labels would render as missing-font boxes. Noted in both READMEs as the thing to change after importing TMP.
- **`EventSystem` uses `InputSystemUIInputModule`.** `ProjectSettings.activeInputHandler` is `1` (new Input System only), where `StandaloneInputModule` does nothing and every button would be silently dead.
- **`Boot()` left alone.** The FSM's `BootState` decides the first screen, so the shipped flow needs no startup hook; `Boot()` stays for a game that wants to skip to gameplay.
- Kept the builder tool in the repo rather than deleting it after use — it is idempotent and documents how the prefabs were made.

Verification (all in the live Editor, via the Unity CLI):

- **Ran the game and drove the whole flow.** FSM reached `MainMenuState` with `MainMenuView` instantiated; then MainMenu→(Play)→Gameplay→(Settings)→Settings overlay→(Back)→Gameplay→(Home)→MainMenu, and MainMenu→(Store)→Store→(Back)→MainMenu. **Overlay semantics confirmed**: entering Settings over Gameplay left `GameplayView(hidden)` alive rather than destroyed, and Back restored it.
- **Clicked the real buttons**, not just raised the events: each button reported `persistentListeners=1` (serialized wiring, Inspector-editable) and `onClick.Invoke()` drove the same transitions.
- **`EventSystem.RaycastAll` at the Play button's screen position hit `PlayButton`**, proving the canvas is on screen and raycastable.
- **EditMode suite: 138/138 passing**; zero console errors throughout.

Gotchas found:

- **`capture_game_view` / `screenshot` do not capture `ScreenSpaceOverlay` UI** — both render through the camera, so the game view came back as bare skybox while the UI was demonstrably present. Do not read an empty capture as "the UI is missing"; verify with a raycast or a component probe instead.
- **`simulate_pointer` did not trigger the button** even though the raycast hit it — synthetic Input System pointer events appear to need the Game view focused. `onClick.Invoke()` is the reliable headless substitute.
- **Swapping `m_Script` via `SerializedObject` destroys and re-creates the managed instance**, so the original reference dangles immediately afterwards — reload via `AssetDatabase.LoadAssetAtPath` before touching the asset again. The first builder run threw `MissingReferenceException` on a trailing `EditorUtility.SetDirty(asset)` for exactly this reason.
- `find_gameobjects` and `get_scene_hierarchy` do not see objects in `DontDestroyOnLoad`; use `FindObjectsByType` through `eval_file`.

Next steps:

- Replace the placeholder prefabs with real screens (import TMP first).
- `Assets/Scenes/GameScene.unity` is now unused — the flow is prefab-driven and only `BootstrapScene` is in Build Settings. Decide whether to delete it or make Gameplay load it.
- Author a LevelFail screen if that flow is still wanted; `LevelFailTransition` remains unassigned.


### 2026-09-25T19:15:00+05:00 — claude-opus-5/screen-flow-states-and-assets

Summary of what was done:

- **Shipped the MainMenu / Gameplay / Settings / Store screen flow** as wired assets plus the code needed to route to it. User asked for "all required states, SO Variables and GameEvents"; two forks were put to them first, since `Store` did not exist in the code at all and the repo documents no product spec. They chose full code wiring, and the conventional starter variable set.
- **Code** (`Assets/Runtime`): `FlowContext` gained `Store = 5`, `Gameplay = 6`; `FlowIntent` gained `GoToMainMenu = 104`, `OpenStore = 105`; `UICloseReasons` gained `Store = 7`. `ApplicationFlowController` gained `MainMenuTransition` / `StoreTransition`, `GotoMainMenu` / `GotoStore`, and `MainMenuViewClosed` / `SettingsViewClosed` / `StoreViewClosed`, with command-map entries, handlers and subscribe/unsubscribe. `ApplicationFlowLogic` gained 13 strategy entries.
- **Assets**: `Assets/StateMachine` (`ApplicationStateMachine` booting into `MainMenuState`, 4 `State`s, 4 `Transition`s), `Assets/GameEvents` (6 `GameEvent` + 4 `GameEventWithInt`), `Assets/Variables` (Settings / Store / Gameplay / Application). All hand-authored as Unity YAML with fresh `.meta` GUIDs, matching the format of the existing `time.machine` example assets.
- **Both prefabs pre-wired** — `ApplicationBase.prefab` (FSM, AppPaused/AppResumed, AppPausedTime) and `ApplicationFlowController.prefab` (all 5 transitions, 4 command events, 4 view-closed events, plus its cross-prefab `ApplicationBase` reference). Every referenced GUID was resolved back to a real asset before committing.

Files touched:

- `Assets/Runtime/Logic/{FlowContext,FlowIntent,UICloseReasons,ApplicationFlowLogic}.cs`
- `Assets/Runtime/Application/ApplicationFlowController.cs`
- `Assets/Tests/EditMode/ApplicationFlowLogicTests.cs`
- `Assets/Prefabs/{ApplicationBase,ApplicationFlowController}.prefab`
- `Assets/StateMachine/**`, `Assets/GameEvents/**`, `Assets/Variables/**` (new)
- `Assets/Runtime/README.md`, `Docs/GameFlow (Template Layer).md`, `.agents/*`

Decisions made:

- **FSM `BootState` = `MainMenuState`**, and `ApplicationFlowController.Boot()` was left asking for `(Boot, Game)`. Changing `Boot()` would have altered existing behaviour and its test; instead `(Boot, Home) → GoToMainMenu` was added to the table so a caller can opt in. Documented in both READMEs.
- **`SettingsState` and `StoreState` set `PausesPreviousState`**, making them overlays so `ResumeGame → ResumePrevious` is meaningful; `MainMenu` and `Gameplay` do not.
- **Persistent variables use `ResetToDefaultOnPlay: 0`** so the PlayerPrefs value wins on load — `ResetToDefaultOnPlay: 1` would overwrite saved progress on every `OnEnable`. Session-only `v_Score` keeps `1`.
- **No `LevelFailState` / `ToLevelFail`** was invented: `LevelFail` predates these four screens and was not part of the request, so `LevelFailTransition` stays unassigned.
- **Fixed a now-false-positive test.** `SubclassCanExtendStrategyTable` asserted `Settings + ResumeGame → ResumePrevious`, which the base table now defines itself — it would have passed even with `Add()` broken. Re-pointed at `(Gameplay, Revive)`, which the base deliberately leaves out, with an explicit precondition assertion guarding that. Added `SubclassCanOverrideAnExistingStrategy` and a 13-case `TestCase` matrix.

Verification:

- `Assets/Runtime` **compiles clean** — the whole assembly was built outside the Editor with Unity's bundled Roslyn (`Data/DotNetSdkRoslyn/csc.dll`) against `netstandard.dll`, `UnityEngine.dll` and the seven package DLLs in `Library/ScriptAssemblies`. Only CS0649 warnings (`[SerializeField]` never assigned in code), which are expected and pre-existing.
- **The decision table was executed, not just compiled**: a console harness linked against the real logic sources checked all 15 shipped routes plus both fallbacks — 17/17 as expected, including the `(Gameplay, Revive) → DefaultToGame` precondition the subclass test depends on.
- **In-Editor verification followed on 2026-09-26** (after closing the Editor that held `Temp/UnityLockfile`), and the earlier "not verified" caveat is retracted:
  - **EditMode suite: 138/138 passing, 0 failed, 0 skipped** via `unity test . --mode EditMode` — 124 pre-existing plus the 14 added here. All 18 `ApplicationFlowLogicTests` cases ran, including every one of the 13 `TestCase` routes and both subclass tests.
  - **Unity accepted all 40 hand-authored asset and `.meta` files byte-for-byte** — `git status Assets/` was clean after the import, so the script GUIDs, `.meta` format and every cross-reference were correct and needed no rewriting. No import errors or missing-script warnings in the Editor log.
- **Batchmode aborts when any package cannot resolve**, which is unrelated to this change but will bite any CI run here: the first attempt exited 1 during resolution on `com.unity.ai.assistant` and `com.unity.toolchain.linux-x86_64-linux` (ECONNRESET against `download.packages.unity.com`; `packages.unity.com` itself answered 200, and three direct `curl` downloads of the tarball timed out). A GUI Editor tolerates the same gaps. A retry got through because the first run had meanwhile cached `com.unity.pipeline` and `com.unity.sdk.linux-x86_64`.

Next steps:

- Author a LevelFail screen (state + transition) if that flow is still wanted — `LevelFailTransition` stays unassigned until then.
- Get `com.unity.ai.assistant` and `com.unity.toolchain.linux-x86_64-linux` cached (or dropped from the manifest): until they resolve, every batchmode/CI run here is a coin-flip on the CDN. This is concrete evidence for the standing open question about whether the Linux toolchain packages belong as default dependencies.
Last updated: 2026-09-25

## Current Session

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

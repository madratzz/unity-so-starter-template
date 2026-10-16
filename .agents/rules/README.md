# Unity Rule Reference Copies

Schema version: 1

Last updated: 2026-10-17

Full-text reference copies of the Unity rule files this repository follows. The **binding, condensed form lives in the root [AGENTS.md](../../AGENTS.md)** ("Unity Rules" section); read a file here only when you need the detail, examples, or self-check behind a condensed rule. If a copy here disagrees with `AGENTS.md`, `AGENTS.md` wins.

## Provenance

Copied on 2026-10-17 from the private `madratzz/madratzz-ai-prompts` library (`AI Prompts/Unity/`), after its skills-install, test-delegation and test-model-selection rules were made harness-agnostic (branch `fix/harness-agnostic-rules` of that repo at copy time). The library stays canonical; re-sync from it rather than editing these copies for content changes. The skills-install and editor-flag files include the 2026-10-17 corrections (verified agent ids; no `-quit` with `-runTests`). Local edits are limited to: uppercase filenames (this repo's context-file convention), cross-reference names, and the level-map image embed (the image is not vendored).

## Files

| File | Governs | Notes for this repo |
|---|---|---|
| [UNITY-NAMING-CONVENTIONS.md](UNITY-NAMING-CONVENTIONS.md) | C# naming | `UniTask`/`IDomainEvent` examples do not apply — this repo uses neither. |
| [UNITY-EDITOR-AUTOMATED-FLAG.md](UNITY-EDITOR-AUTOMATED-FLAG.md) | Non-interactive Editor launches | Applies as written. |
| [UNITY-HEADLESS-ON-WORKTREE.md](UNITY-HEADLESS-ON-WORKTREE.md) | Headless-by-default in a worktree | Applies as written. |
| [UNITY-TEST-DELEGATION.md](UNITY-TEST-DELEGATION.md) | Running tests in a cheap-model subagent | Use your harness's own subagent mechanism. |
| [UNITY-TEST-MODEL-SELECTION.md](UNITY-TEST-MODEL-SELECTION.md) | Choosing that cheap model | Resolve the model at run time; pin nothing. |
| [UNITY-UGUI-PREFAB-AUTHORING.md](UNITY-UGUI-PREFAB-AUTHORING.md) | Designer-editable uGUI prefabs | `Assets/Game/Views/` does not exist; UI prefabs live under `Assets/Prefabs/` (see AGENTS.md). |
| [UNITY-LEVEL-MAP-DESIGN.md](UNITY-LEVEL-MAP-DESIGN.md) | Level docs and maps | Only relevant once a game built on this template has levels; paths adapted to `Docs/` (see AGENTS.md). |
| [UNITY-OFFICIAL-SKILLS-INSTALL.md](UNITY-OFFICIAL-SKILLS-INSTALL.md) | Making Unity-Technologies skills available to the running harness | Harness-agnostic. |

## Deliberately not vendored

- `unity-architecture-rules-di.md` — describes a VContainer/MessagePipe/UniTask layered architecture. This repository's settled direction is SOAP with no mandatory DI container (see `.agents/MEMORY.md`), so the file is intentionally excluded (user decision, 2026-10-17).
- `unity-scriptableobject-configs.md` — built on that DI architecture (`IConfigService`, `ConfigAsset`, `[LifetimeScope]`, `RemoteConfigOverrides`). Only its harness-neutral ideas are condensed into `AGENTS.md`.

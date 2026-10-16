---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-17
tags:
  - ai-prompt
  - unity
  - ui
  - ugui
  - prefab
  - authoring
---

# Unity uGUI Prefab Authoring Rules

Rules for authoring uGUI prefabs so they can be opened in Prefab Mode and edited by hand — without re-running the code that built them. Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**Every UI element is a `.prefab` asset on disk that is instantiate-ready and designer-editable.** A prefab that depends on code to "finish setting itself up" at runtime is one the user cannot edit by hand — opening it in Prefab Mode shows a half-built UI, and the user has no way to know what the runtime will fill in.

The test is simple: open the prefab in Prefab Mode with no code running, and what you see is what the player sees. If the prefab is empty until `Awake` runs, the rule is broken.

## What "Editor-Ready" Means

A prefab is editor-ready when, opened in Prefab Mode with no scripts executing, it shows exactly what the player sees:

- All `RectTransform` anchors, pivots, and offsets are serialized on the asset.
- All `LayoutGroup` and `ContentSizeFitter` components are on the asset, with their child-control and expand flags already set.
- Every `Image`, `RawImage`, and `TextMeshProUGUI` references its sprite, texture, and font asset by direct asset reference — not by string path, not by `Resources.Load`, not by `Addressables.LoadAssetAsync` resolved at edit time.
- Every interactive control (`Button`, `Toggle`, `Slider`, …) is present on the asset with its `Selectable` settings serialized, and the view script holds a serialized reference to it (or resolves it by name through a lookup that throws when the name is missing). Its **events are not wired in the inspector**: no `UnityEvent` entries on the asset. The view subscribes in code (see "Events Are Wired in Code").
- Every serialized field the prefab depends on has a sensible default — opening the prefab cold, the designer sees a usable UI, not a stack of empty boxes.

## Build It In the Editor, Not In the Code

- The prefab is the source of truth. Code reads from it; code does not write to it.
- `AddComponent<T>`, `SetActive(true/false)`, anchor arithmetic, and `LayoutRebuilder.ForceRebuildLayoutImmediate` are for runtime mutation of an instantiated copy, never for the first-time build of the prefab.
- A new UI element means a new `.prefab` file under `Assets/Game/Views/<Area>/`, committed alongside the code that drives it.
- Themed variants, responsive variants, and platform variants are **prefab variants**, not runtime if/else trees. Designers can author them without touching code.

## Events Are Wired in Code

**Interaction events are subscribed in code, never as inspector `UnityEvent` entries.** An inspector wire is invisible to search, rename-refactors, and code review; a deleted or renamed method leaves a silent dead wire. A code subscription is greppable, typed, and breaks the build when the target changes.

- The view controller subscribes with `AddListener` (or the control's equivalent) when it is shown or enabled, and unsubscribes with `RemoveListener` when it is hidden, disabled, or destroyed. Subscribe and unsubscribe are symmetric; every `AddListener` has a matching `RemoveListener`.
- Subscribe to **named handler methods**, not lambdas, so the handler can be unsubscribed and shows up in stack traces.
- The handler's job is to publish a domain event or call a service through the view's contract; the view does not make game decisions itself.
- Wiring *listeners* in code is not "building UI in code". The prohibition below is about creating or laying out the hierarchy, which stays on the asset.
- A handler that has to await is a plain `void` method that starts a `UniTask`-returning method and calls `.Forget()` on it (or returns `UniTaskVoid`), logging exceptions. `async void` is never used.

## Fields the Inspector Must See

- Every configurable value the designer might want to change is a `[SerializeField] private` field with `PascalCase` and a `[Tooltip]` if the name is not self-evident.
- Avoid `[HideInInspector]` on fields a designer might want to touch. The inspector is the design surface; hiding it is hiding the contract.
- If a custom `[CustomEditor]` is needed, the default inspector remains reachable from the component's context menu (`...` → `Default Editor`). A custom editor that hides fields without an escape hatch is forbidden.
- No `[ExecuteAlways]` / `[ExecuteInEditMode]` that mutates serialized state outside designer intent. A prefab that "auto-corrects" itself in edit mode is a prefab the user cannot edit.

## What the Code Side Promises

- The view MonoBehaviour exposes its dependencies via constructor injection or a serialized host reference — it does not look up services by name, by tag, or by string at edit time.
- The code never `Destroy`s and recreates a UI subtree the designer authored; it `Instantiate`s the prefab as-authored and mutates only fields exposed for runtime use.
- Animations, tweens, and layout invalidations are runtime concerns, not authoring concerns. They run on the instantiated copy, not on the asset.

## Don'ts

- **Do not** build UI in `Awake` / `Start` from primitive types. The designer cannot open the prefab and see it.
- **Do not** use `Resources.Load("UI/...")` to find a prefab by path. Reference it from a `ScriptableObject` config or from a serialized field.
- **Do not** wire `Button.OnClick` (or any other `UnityEvent`) in the inspector. Subscribe in code and unsubscribe symmetrically; see "Events Are Wired in Code".
- **Do not** call `transform.SetParent` from a designer-facing authoring script. The hierarchy is the design.
- **Do not** strip the `Canvas` and `CanvasScaler` from a "child prefab". The prefab must be instantiate-ready; the consumer is responsible for putting it under a `Canvas`, not the prefab author for stripping it.
- **Do not** save prefab state changes that come from `OnValidate` mutating values the designer just typed. The round-trip is "designer's keystrokes survive a reload".

## Self-check Before Commit

- [ ] Every new UI element is a `.prefab` file under `Assets/Game/Views/`.
- [ ] Opening the prefab in Prefab Mode shows the same UI the player sees.
- [ ] All sprite, font, texture, and nested-prefab references are direct asset references, not string paths.
- [ ] No `UnityEvent` entries are wired in the inspector; every `onClick` and similar event is subscribed in code and unsubscribed symmetrically.
- [ ] Every configurable value is `[SerializeField] private` and visible by default.
- [ ] No `[ExecuteAlways]` that mutates serialized state in edit mode.
- [ ] No `Resources.Load` of UI prefabs.
- [ ] No `AddComponent<T>` for the first-time build of a UI element.
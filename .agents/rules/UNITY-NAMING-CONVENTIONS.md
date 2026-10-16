---
note_type: reference
status: active
created: 2026-10-15
updated: 2026-10-17
tags:
  - ai-prompt
  - unity
  - naming
  - conventions
---

# Unity C# Naming Conventions

A focused, copy-pasteable rule set for C# in Unity 6 projects. Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## File and Type Names

- File name matches the primary type name, including the `I` prefix on interfaces. `IFooService` lives in `IFooService.cs`. Nested types go in the file of the type that owns them.
- One type per file unless the types are tightly coupled and small (e.g. an enum used only by one struct).
- Namespaces use PascalCase, period-separated, and mirror the folder path. `Assets/Game/Application/States/OfferState.cs` → `namespace Game.Application.States`.
- Folder names are Title Case with hyphens where spaces would be: `Loading-Overlay`, `Bootstrap-Assets`. C# files and types stay PascalCase, no hyphens.

## Types

- **Classes, structs, records**: `PascalCase`. Records are rare in Unity 6 (C# 9 only, no `record struct`); prefer a `readonly struct` or a plain class with `IEquatable<T>`.
- **Interfaces**: `IPascalCase` with `I` prefix. No `I` suffix like `IFooInterface`.
- **Enums**: `PascalCase` for the type, `PascalCase` for values, **never** an `Enum` suffix. `[Flags]` enums use plural nouns.
- **Generic type parameters**: `T` for single, `TKey`/`TValue` or `TInput`/`TOutput` when there are several. Avoid `T1`/`T2`.
- **Delegate types**: `PascalCase`, suffix `Handler` or `Callback` when the role is an event handler. Async delegates returning `UniTask` end in `Async` only if the name would otherwise be ambiguous.
- **Attribute types**: `PascalCase` ending in `Attribute`. The `[Foo]` short form is preferred at use sites.
- **Sentinel enum value**: keep zero inert. `default(MyEnum)` must mean "no opinion" or "not yet set", never a granting value.

## Members

- **Public and protected members**: `PascalCase`. No Hungarian notation, no type encoding.
- **Private fields marked with `[SerializeField]`**: `PascalCase`. Example: `[SerializeField] private float MovementSpeed;`. This is the rule the inspector reads; underscore prefixes confuse Unity's serializer tooling.
- **Private fields without `[SerializeField]`**: `_camelCase` with a leading underscore. Example: `private float _movementSpeed;`. The underscore makes the field self-documenting as "implementation detail" at the call site.
- **Constants** (`const` and `static readonly`): `UPPER_SNAKE_CASE` for true constants, `PascalCase` for `static readonly` instances. The convention here is that *the casing tells you whether the value is inlined*.
- **Static fields**: `PascalCase` if `public`, `_camelCase` if `private` and not `[SerializeField]`.
- **Properties**: `PascalCase`. Read-only when the underlying value is not meant to change at runtime; never expose setters "just in case".
- **Methods**: `PascalCase`. Async methods returning `UniTask` or `Task` are named for the action, not the type: `LoadAsync`, not `LoadTask`. The return type already says it's async.
- **Local variables and parameters**: `camelCase`. No type prefix, no leading underscore on parameters.
- **Type parameters on methods**: `PascalCase` with `T` prefix when they appear in a method signature, e.g. `void GoTo<TState>()`.

## Names With Meaning

- **Boolean members**: prefix with the verb that the truth value implies. `IsVisible`, not `Visible`. `HasValue`, not `Value`. `CanDismiss`, not `Dismissable`. A name without a verb reads as a noun that is sometimes true, which is ambiguous.
- **Sentinel values**: name for their meaning, not their storage. `RemainingAttempts`, not `AttemptsLeft`. `_lastTickTime`, not `_lastTick`.
- **Asymmetry**: pair verbs explicitly. `Show`/`Hide`, `Enable`/`Disable`, `Begin`/`End`, `Open`/`Close`. Mixing `Open` with `Dismiss` or `Begin` with `Stop` makes the call site read as a translation problem.
- **Unit in the name** when the unit is non-obvious: `IntervalSeconds`, `SpeedMetersPerSecond`, `SizeBytes`. Keep the unit in the field name; do not put it in a tooltip where the call site cannot see it.

## Async

- Methods returning `UniTask` are not suffixed `Async` when the method name already says so (`Load`, `Save`, `Transition`). They are suffixed `Async` when the same name has a synchronous variant, or when the verb alone would not communicate that the call yields (`EnsureVisibleAsync`).
- Methods returning `ValueTask` only when the hot path matters and a `UniTask` allocation is unacceptable. Otherwise use `UniTask`.
- Cancellation token is the **last** parameter, with the default value `default`. Never the first parameter; never required.
- `async void` is **never** used. UI events are subscribed in code, so a handler is a plain `void` method that starts a `UniTask`-returning method and calls `.Forget()`; a fire-and-forget method may return `UniTaskVoid` and must log the exceptions it swallows. Everything else returns `UniTask`.

## Events and Delegates

- `IDomainEvent` records are `PascalCase` past-tense: `LevelCompleted`, `PurchaseGranted`, `AdImpressionLogged`. Past-tense reads as "this is the announcement", which is what events are.
- Public delegate types that are not events end in `Handler` or `Callback`. Events themselves are not delegate types; they are fields or properties of type `Action<...>` or `Func<...>` and live on the service that raises them.
- Do not name a method `On*` unless it overrides a base `On*` method or is a Unity message (`OnEnable`, `OnDestroy`, `OnValidate`, …). The `On` prefix is a Unity convention for engine callbacks and reading "OnX" implies it is one. A handler you subscribe in code (`button.onClick.AddListener(...)`) is named for what it does: `HandleAccept`, `HandleDismiss`.

## Inspector-facing

- `[SerializeField] private` fields have the same name as the inspector label. Use `[Tooltip("...")]` to add detail that does not fit in the field name.
- Group related fields with `[Header("...")]`. Keep headers short; a 30-character header that names a concept beats a sentence.
- Use `[Range(0, 1)]` for normalized values and `[Min(0)]` for non-negative counts. Inspector constraint > code invariant.
- The inspector name is PascalCase; if the field name is `_movementSpeed` (private) but you want a clean inspector label, do not rename the field — Unity's serializer already strips the underscore from the inspector display.

## Names to Avoid

- `Manager`, `Helper`, `Util`, `Utils`, `Wrapper`, `Handler` (as a noun). These are "I do not know what this is yet" names; the name should say what the type *is*, not that it has methods.
- `Base*` or `Abstract*` prefixes. The class is a base because it is marked `abstract`, not because the name says so.
- `My*`, `*Impl`, `*V2`. If you need a version suffix, the abstraction is wrong.
- `Data`, `Info`, `Item` as a suffix on DTOs. Name the DTO for its role: `LevelCompletion`, `UserProfile`, `AdPlacement`.

## Anti-patterns

- A property `public` setter that exists only for tests. Make the setter `internal` and `[InternalsVisibleTo]` the test assembly, or expose a constructor parameter.
- A `bool` parameter on a method that flips behaviour (`Save(bool force)`). Split into `Save()` and `SaveForce()`. Call sites read as the call.
- A class whose name is a verb (`Loading`, `Saving`). Verbs are methods; classes are nouns.

## Self-check Before Commit

- [ ] Every public type has an XML doc comment that fits in one line.
- [ ] Every `[SerializeField] private` field is `PascalCase`; every other private field is `_camelCase`.
- [ ] No `Manager`/`Helper`/`Util` in type names.
- [ ] No `async void`.
- [ ] Cancellation tokens are last, defaulted.
- [ ] Enum zero is inert.

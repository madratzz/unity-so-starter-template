# Archived Context — searchable ScriptableObject dropdown decision (2026-09-21)

Schema version: 1

Archived on 2026-10-17 from `.agents/CONTEXT.md` (verbatim bullet, immutable). The active file keeps a shorter statement of the decision in force.

---

- (2026-09-21) `com.madratzz.utilities.attributes` also gained a searchable-dropdown Inspector for **every** `ScriptableObject`-typed `[SerializeField]` field, project-wide — `SearchableAssetDrawer` is registered against `ScriptableObject` itself (`[CustomPropertyDrawer(typeof(ScriptableObject), true)]`), not a marker attribute, deliberately so it's automatic and never needs adding per field/class. Built as the free (no Odin Inspector) alternative evaluated in the prior GameEvent-improvement discussion. First implementation used a `[SearchableAsset]` attribute per field; user asked "can't we make it class level... auto"; replaced with the type-based global registration on the same turn before anything was committed.

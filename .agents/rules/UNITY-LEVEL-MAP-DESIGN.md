---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-16
tags:
  - ai-prompt
  - unity
  - level-design
  - map-design
  - gdd
  - documentation
---

# Unity Level & Map Design Documentation

Rules for designing levels during a game's documentation and planning phase. Levels are designed and tracked in the project's Obsidian vault, not in chat, in Miro, or in a folder of screenshots. Maps follow the reference architectural-drawing layout below so every plan reads the same way.

Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**Every level that needs design is documented in the project's Obsidian vault with a map image in the reference layout, and every map image reads through one shared legend.**

A level that exists only in Discord screenshots, Figma boards, or someone's head is a level that will be redesigned three times during implementation. The vault is the source of truth; tools are aids.

## Reference Map Style

Map images follow this architectural-drawing layout. The reference image is embedded below — treat it as a layout guide, not a brand asset to copy.

*(The reference image `map-design-reference-architectural-floor-plan.jpeg` lives in the source prompts repo, `Assets/Attachments/`; it is not vendored here. Obtain it from there when authoring the first map.)*

What the reference encodes:

- **Background**: warm off-white / cream. Not pure white, not grey.
- **Foreground**: near-black for walls, text, and dimension lines. One warm accent color (orange in the reference) reserved for emphasis.
- **Top-left eyebrow**: project + location + year in small caps, e.g. `PROJECT · LOCATION · YYYY`.
- **Top-right title block**: drawing title (bold), project · scale · sheet line, and a graphical scale bar directly underneath.
- **Alphanumeric grid**: letters across the top (`A`, `B`, `C`…), numbers down the side (`1`, `2`, `3`…). Every point on the map is addressable as `A1`, `D4`, etc.
- **Compass rose / north arrow**: present on every map, even on abstract top-down grids.
- **Dimension lines**: measurements along the edges in meters (or the world's unit). Internal dimensions annotate each room.
- **All-caps room/zone labels**: `MEDIA`, `KITCHEN AND DINING`, `BOSS ARENA`. Sans-serif, small caps, with sub-text for area (`72 m²`) or extents (`28.00 x 6.00`).
- **Furniture and key objects** as simple line drawings — sofas, beds, tables, cover props, loot crates. Symbolic, not detailed.
- **Annotations on lines**: `GLASS GALLERY`, `ELEVATED`, `ONE-WAY GLASS` — short caps annotations for non-obvious features.
- **Bottom-left metadata table**: `PROJECT`, `DRAWING`, `SCALE`, `SHEET` (with revision, e.g. `A-101 REV D`).
- **Typography hierarchy**: title (very large), eyebrow / metadata (small caps), room labels (small caps), dimensions (small).

What is *branding* on the reference and not a convention to copy: the orange-accent word in the title, the "START A PROJECT" pill, and the website footer nav (`WORK | STUDIO | CONTACT`). Strip those.

## One Shared Legend

A single legend file lives in the project's vault and is referenced from every level doc. Conventions:

- Path: `docs/03 Design/Levels/_Legend.md` (the leading underscore keeps it first when sorted alphabetically).
- The legend defines every symbol, line style, and color used on maps: spawn points, exits, doors (with swing), stairs (with direction), cover, objectives, loot tiers, enemy types, NPCs, hazards, save points, fast-travel nodes, interactive objects, elevation changes.
- **New symbols are added to the legend first**, then used in maps. Never invent an ad-hoc icon for one level.
- Every level doc links to the legend at the top: `> Legend: see [_Legend.md](_Legend.md)`.
- The legend is the source of truth for "what does this icon mean". If two levels use the same icon for different things, the legend is wrong.

## Cross-References Use Grid Coordinates

- "Spawn at **A3**", "Boss at **D4**", "Key pickup at **B2**". Never "in the middle" or "by the trees".
- Encounter flow uses coordinates: `Wave 1: A1→A2→A3`, `Wave 2: D4→E5`. The grid is a routing system.
- Story beats tie to coordinates: `Cutscene at B2`, `Dialogue trigger at C1`.

## Level Document Shape

Each level doc lives at `docs/03 Design/Levels/<LevelName>.md` and follows this structure:

1. **Header**: name, designer, target playtime, difficulty rating.
2. **Map image**: the reference-style plan embedded directly under the header. `![[<LevelName>.png]]` or a generated export.
3. **Legend link**: `> Legend: see [_Legend.md](_Legend.md)`.
4. **Flow**: entrance → exit path as coordinates. Bulleted, one line per leg.
5. **Encounters**: each encounter keyed by grid coordinate, with enemy types, pacing notes, and reward.
6. **Pacing beats**: explicit high / low / climax / recovery sections, each with the coordinates they happen in.
7. **Narrative beats**: story moments with their grid coordinates.
8. **Design notes / iteration history**: dated entries as the level is iterated. Old versions move to `docs/03 Design/Archive/Levels/<LevelName>/<date>/` with a date stamp; the active doc keeps the current version.

## Don'ts

- **Do not** put level designs in chat threads, design docs in Figma only, or maps in Discord attachments. The vault is the only source of truth.
- **Do not** invent a legend per level. One legend, many levels.
- **Do not** use vague spatial references — "the middle", "near the start". Grid coordinates or it didn't happen.
- **Do not** use the reference image's branding (the orange-accent title word, the pill button, the website footer). It is a layout guide, not a template to clone.
- **Do not** ship a map without a compass rose, a scale bar, or a grid. A map without those three is a sketch, not a plan.
- **Do not** use a different view (isometric, side-view, perspective) without a written justification in the level doc's design notes. Top-down floor plan is the default.
- **Do not** keep level history in the active doc indefinitely — archive old iterations with a date stamp, link to them from the active doc.

## Self-check Before Commit

- [ ] The level doc lives at `docs/03 Design/Levels/<LevelName>.md` in the project vault.
- [ ] A map image is embedded at the top of the doc, in the reference layout.
- [ ] The map has a title block (top-right), compass rose, scale bar, and alphanumeric grid.
- [ ] The map uses only symbols defined in `_Legend.md`; no ad-hoc icons.
- [ ] Cross-references in flow, encounters, and beats use grid coordinates.
- [ ] The legend file exists at `docs/03 Design/Levels/_Legend.md` and is linked from this level.
- [ ] Iteration history is captured as dated entries; old versions are archived.
- [ ] No reference-image branding leaks into the map (no orange-title, no pill, no website footer).
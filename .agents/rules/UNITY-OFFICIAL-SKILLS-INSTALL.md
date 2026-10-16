---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-17
tags:
  - ai-prompt
  - unity
  - skills
  - tooling
  - harness-agnostic
---

# Unity Official Skills — Check & Install

Rules for keeping the Unity-Technologies official skill set available to whichever AI harness is doing the work (Claude Code, Codex, Cursor, Hermes, or any other agent the `skills` CLI supports). The set is published automatically from <https://github.com/Unity-Technologies/skills>; this rule defines when an agent must verify its own harness has the set and how to install what is missing.

Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**Whenever a Unity task starts, the agent checks that the Unity-Technologies official skill set is available to its own harness and installs any missing entries before doing Unity work.**

A skill that is published upstream but missing locally is a skill that will not auto-load — the agent will hand-roll the work the skill was designed for. The check is cheap; the gap is silent.

"Its own harness" is deliberate. The agent installs for the harness it is running in, not for every harness on the machine. Skill directories differ per harness; do not hard-code one harness's path into a shared rule.

## What "Official" Means

The canonical source is one repository:

- **Repo**: <https://github.com/Unity-Technologies/skills>
- **Path inside the repo**: `skills/<skill-name>/SKILL.md`
- **Publisher**: Unity Technologies. The repo states PRs are not accepted (`CONTRIBUTING.md`) — the set is publish-only, auto-generated from Unity's internal pipeline.

A skill whose name appears in the upstream `skills/` directory listing is "official". A locally installed skill with the same name but different content is a near-duplicate and must be surfaced to the user, not silently overwritten.

A harness that already ships the same skills through its own native plugin or marketplace (for example Claude Code with Unity's plugin enabled) satisfies the rule for those skills; do not install a second copy of a skill the harness already exposes.

## When to Run the Check

- **First turn of any Unity task**, before doing Unity work. The check is fast and prevents "I would have used `ui-ugui` but I never saw it" failures.
- **When the user creates or clones a new Unity project**, before scaffolding anything.
- **After a harness upgrade** that touched its skills system — updates can swap skill catalogs.
- **Periodically** during a long-running Unity session — Unity publishes new skills monthly; the agent should not lag.

The check is not required on every turn. Once per task is enough; once per session is the minimum.

## The Check

Two lists, one diff.

Upstream — the canonical names:

```
npx -y skills add Unity-Technologies/skills --list
```

or, without cloning, from the GitHub API:

```
curl -s "https://api.github.com/repos/Unity-Technologies/skills/contents/skills" \
  | grep '"name"' | sort -u
```

Local — what this harness can already see:

```
npx -y skills list -a <agent-id>        # project scope
npx -y skills list -g -a <agent-id>     # user scope
```

`<agent-id>` is this harness's identifier as the `skills` CLI knows it, for example `claude-code`, `codex`, `cursor`, `gemini-cli`, `github-copilot`, `opencode`, `windsurf`, or `hermes-agent` (all confirmed against the CLI on 2026-10-17). `--help` does not print the full set; to list every valid id, pass a deliberately bad one — `npx -y skills list -a not-an-agent` errors with `Valid agents: …`. Also count skills the harness lists natively (its in-session skill list, plugin skills).

The diff is the set of names that appear upstream but nowhere locally. A name that appears locally but not upstream is a harness- or user-owned skill — leave it alone.

## The Install

For every missing name, install for this harness only:

```
npx -y skills add Unity-Technologies/skills -a <agent-id> -s '<name>' -y
```

For a batch, list the names comma-separated:

```
npx -y skills add Unity-Technologies/skills -a <agent-id> -s 'unity-cli,ui-ugui,sprite-editor' -y
```

Flag reference:

- `-a <agent-id>` — the harness to install for. Name the one you are running in; to serve several harnesses, name each explicitly (`-a claude-code,codex`). Do **not** pass `-a '*'` — it writes into every agent's directory on the machine.
- *(no scope flag)* — project scope: the skills live with the repo, so every teammate and every harness opening it gets them. Prefer this for project work.
- `-g` — user scope, shared across all projects on the machine. Use it when the user wants the skills everywhere.
- `-s '<names>'` — the comma-separated subset to install. Use this rather than `-s '*'` to avoid re-fetching skills that are already current.
- `--copy` — copy files instead of symlinking, when the harness or the filesystem does not follow symlinks (or the project must carry real files).
- `-y` — skip the per-skill confirmation prompt.

## Verifying the Install

After install:

- `npx -y skills list -a <agent-id>` (add `-g` for user scope) shows the new entries alongside pre-existing ones. The total is old + new, not a replacement.
- The harness's own skill list shows them; they auto-load by description match from the next session (some harnesses need a restart or a reload).
- If a near-duplicate appears (same name, different content), surface the conflict to the user — do not silently delete the older one.

## Don'ts

- **Do not** pass `-a '*'` or `--all`. They write the skills into every agent's directory on the machine and load them on each agent's next start.
- **Do not** open a PR against `Unity-Technologies/skills`. The repo's `CONTRIBUTING.md` says PRs are not accepted; the set is auto-published by Unity's internal pipeline.
- **Do not** hand-edit an installed Unity-Technologies skill to "patch" it. The next install overwrites the edit. Report the issue upstream on the Unity Discussions forum instead.
- **Do not** invent a local skill that duplicates a Unity-Technologies skill because the official one "doesn't do enough". Either upstream the change or live with the gap.
- **Do not** skip the diff step and run `npx skills add Unity-Technologies/skills -s '*' -y` blindly. It re-fetches skills you already have and surfaces false-positive conflicts.
- **Do not** install a second copy of a skill the harness already exposes through its own plugin or marketplace; duplicates shadow each other.

## Self-check Before Starting Unity Work

- [ ] The upstream skill list was compared against what this harness can see, this session.
- [ ] Every missing skill was installed with `-a <agent-id>` for this harness (project scope unless the user asked for `-g`).
- [ ] No `-a '*'` or `--all` was passed.
- [ ] The harness's own skill list reflects the new entries (after a reload if it needs one).
- [ ] No near-duplicate skill was silently overwritten — any conflict was surfaced.

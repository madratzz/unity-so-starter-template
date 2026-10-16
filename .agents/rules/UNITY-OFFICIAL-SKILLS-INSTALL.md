---
note_type: reference
status: active
created: 2026-10-16
updated: 2026-10-17
tags:
  - ai-prompt
  - unity
  - skills
  - install
  - agent-agnostic
---

# Unity Official Skills — Check & Install

Rules for keeping the Unity-Technologies official skill set installed for whichever AI agent harness is doing the work — Claude Code, Codex, Grok, Cursor, Cline, GitHub Copilot, Gemini CLI, Hermes, or any other harness the `skills` CLI supports. The set is published automatically from <https://github.com/Unity-Technologies/skills>; this rule defines when an agent must verify that the install matches upstream, and how to install what is missing for *its own* harness.

Apply this file's rules by either pasting them into the project's `AGENTS.md` or by handing this file to an AI agent and asking it to integrate the rules.

---

## The Rule

**Whenever a Unity task starts, the agent checks that the Unity-Technologies official skill set is installed for the harness it is running in, and installs any missing entries before doing Unity work.**

A skill that is published upstream but missing locally is a skill that will not auto-load — the agent will hand-roll the work the skill was designed for. The check is cheap; the gap is silent.

The rule is about the harness the agent is running in. It does not install skills into other harnesses on the machine.

## What "Official" Means

There is one canonical source of skills:

- **Repo**: <https://github.com/Unity-Technologies/skills>
- **Path inside the repo**: `skills/<skill-name>/SKILL.md`
- **Publisher**: Unity Technologies. The repo does not accept PRs (`CONTRIBUTING.md`) — the set is publish-only, generated from Unity's internal pipeline.

There is one canonical *bundle* of the same skills for some harnesses:

- **Repo**: <https://github.com/Unity-Technologies/unity-agent-plugin>
- **Harnesses**: Claude Code, Codex, Grok. The plugin ships the same skills as one package, so they update together and appear in the harness's own plugin and slash-command menus.

A skill with the same name as an official one but different content is a near-duplicate and must be surfaced to the user, not silently overwritten.

## Pick the Install Route for Your Harness

Work out which harness you are running in first. Ask the user if you cannot tell.

| Harness | Route |
|---|---|
| Claude Code | Official plugin (see below). Skills appear as `/unity:<skill>`. |
| Codex | Official plugin (see below). |
| Grok | Official plugin (see below). |
| Any other harness (Cursor, Cline, GitHub Copilot, Gemini CLI, Hermes, …) | The `skills` CLI with `-a <agent-id>` (see below). |

**Do not use both routes for the same harness.** A plugin and a `skills` CLI install of the same skills shadow or duplicate each other.

### Plugin route (Claude Code, Codex, Grok)

Exact commands live in the plugin README; at the time of writing they are:

```
# Claude Code (inside a session)
/plugin marketplace add Unity-Technologies/unity-agent-plugin
/plugin install unity@unity-agent-plugin

# Codex
codex plugin marketplace add Unity-Technologies/unity-agent-plugin
codex plugin add unity@unity-agent-plugin

# Grok (terminal)
grok plugin install Unity-Technologies/unity-agent-plugin --trust
```

Check instead of installing: Claude Code shows the skills when you type `/unity:` and lists the plugin under `/plugin`; Codex lists it with `codex plugin list`. If the plugin is installed and enabled, the check passes and nothing needs to be diffed by hand. Update through the plugin manager, not through the `skills` CLI.

### `skills` CLI route (every other harness)

Install what is missing:

```
npx -y skills add Unity-Technologies/skills -g -a <agent-id> -s '<name>' -y
```

For a batch, comma-separate the names in `-s`. Flags:

- `-g` — user-level (the harness's global skills directory), not project-level.
- `-a <agent-id>` — the harness to install into. Take `<agent-id>` from the **`--agent`** column of the supported-agents table in <https://github.com/vercel-labs/skills#supported-agents> (for example `cursor`, `gemini-cli`, `github-copilot`, `hermes-agent`). The same table gives each harness's project and global skills path.
- `-s '<names>'` — the subset of skills to install. Use the missing names rather than `'*'`, to avoid re-fetching skills that are already current.
- `-y` — skip the per-skill confirmation prompt.

By default the CLI symlinks each agent's directory to a canonical copy; pass `--copy` if the harness or filesystem does not follow symlinks.

## When to Run the Check

- **First turn of any Unity task**, before doing Unity work. The check is fast and prevents "I would have used `ui-ugui` but I never saw it" failures.
- **When the user creates or clones a new Unity project**, before scaffolding anything.
- **After a harness upgrade** that touched the plugin or skills system — upgrades can swap skill catalogs.
- **Periodically** in a long-running Unity session — Unity publishes new skills regularly, and the agent should not lag.

Once per task is enough; once per session is the minimum.

## The Check (skills CLI route)

Two lists, one diff.

Local:

```
npx -y skills ls -g -a <agent-id>
```

If that is unavailable, list the harness's global skills directory from the supported-agents table, or use the harness's in-session skill listing.

Upstream — the canonical list from the GitHub API:

```
curl -s "https://api.github.com/repos/Unity-Technologies/skills/contents/skills" \
  | grep '"name"' | sort -u
```

The diff is the set of names that appear upstream but not locally. A name that appears locally but not upstream is a harness- or user-owned skill — leave it alone.

## Verifying the Install

After installing:

- The harness's global skills directory (or `npx -y skills ls -g -a <agent-id>`) lists the new entries alongside the pre-existing ones. The total is old + new, not a replacement.
- The harness's in-session skill listing shows them; they auto-load by description match from the next turn (some harnesses need a reload or a new session).
- If a near-duplicate appears (same name, different content), surface the conflict to the user — do not silently delete the older one.

## Don'ts

- **Do not** pass `-a '*'` or `--all`. That writes the skills into every agent's directory on the machine and silently loads them on each agent's next start. Name the harness you are running in.
- **Do not** install into a harness the user is not using, or mix the plugin route and the CLI route for one harness.
- **Do not** open a PR against `Unity-Technologies/skills` or `Unity-Technologies/unity-agent-plugin` unless their contribution policy changes. Report issues on the Unity Discussions forum.
- **Do not** hand-edit an installed Unity-Technologies skill to "patch" it. The next update overwrites the edit.
- **Do not** invent a local skill that duplicates an official one because the official one "doesn't do enough". Either upstream the change or live with the gap.
- **Do not** skip the diff step and run `npx skills add Unity-Technologies/skills -s '*' -y` blindly. It re-fetches skills you already have and surfaces false-positive conflicts.
- **Do not** guess an `<agent-id>`. If the harness is not in the supported-agents table, ask the user.

## Self-check Before Starting Unity Work

- [ ] The harness being used was identified (or the user was asked).
- [ ] The install route matches the harness: plugin for Claude Code / Codex / Grok, `skills` CLI otherwise — not both.
- [ ] The local skill set was compared against upstream `Unity-Technologies/skills` this session (or the plugin was confirmed installed and enabled).
- [ ] Every missing skill was installed with `-a <that harness's agent-id>`, never `-a '*'` or `--all`.
- [ ] The harness's own listing reflects the new entries.
- [ ] No near-duplicate skill was silently overwritten — any conflict was surfaced.

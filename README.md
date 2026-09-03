# Overskill plugins

The plugin marketplace repository for [Overskill](https://www.overskill.com) — an
AI app builder that generates, hosts, and operates full-stack web apps from a
prompt.

This repository holds one plugin, `overskill`, which connects a coding agent to
the hosted Overskill MCP server and adds skills for the build, change, publish,
and troubleshooting steps.

> **Status: draft, not published anywhere.** This repository has not been
> submitted to the Claude Code plugin marketplace, ClawHub, skills.sh, the Cursor
> directory, or any other listing. Everything below under "Install" is written for
> whoever publishes it, not run yet.

## What it adds

| Component | Count | Detail |
| --- | --- | --- |
| MCP server | 1 | `overskill` → `https://mcp.overskill.com/mcp` (Streamable HTTP, OAuth) |
| Skills | 5 | `overskill-build`, `overskill-update`, `overskill-ship`, `overskill-debug`, `overskill-connect` |
| Agents | 1 | `overskill-app-builder` — owns a build-through-publish job |
| Hooks, LSP, commands | 0 | none |

### The tools the server exposes

Eleven, all requiring a signed-in Overskill account. Read tools need
`apps:read`; the six write tools need `apps:write`.

| Tool | Kind | What it does |
| --- | --- | --- |
| `create_app` | write | Builds a full-stack app — accounts, data, interactive features |
| `create_landing_page` | write | Builds a single-page site |
| `create_presentation` | write | Builds a deck presented from a URL |
| `create_internal_tool` | write | Builds a dashboard, admin panel, or team utility |
| `update_app` | write | Sends a follow-up change to an existing app |
| `publish_app` | write | Publishes an app to its production URL |
| `get_build` | read | Build status, preview URL, screenshot, stored failure reason |
| `get_app` | read | One app's details, live URL, and `published_at` |
| `list_apps` | read | The connected team's apps, newest first |
| `list_generation_models` | read | Models the account can use, with their thinking levels |
| `render_build_widget` | read | An interactive build-progress card, in hosts that render MCP app UI |

See [ROADMAP.md](ROADMAP.md) for what the server does not do yet.

## Install

### Claude Code

```
/plugin marketplace add OverskillDev/plugins
/plugin install overskill@overskill
/plugin enable overskill
/mcp
```

The plugin installs **disabled** on purpose — it connects to an external account
and generation spends credits, so a user opts in rather than having it armed by a
marketplace refresh. `/plugin enable` turns it on; a newly enabled MCP server
needs `/reload-plugins` or a restart. `/mcp` runs the browser sign-in.

### Grok CLI

Grok reads Claude Code marketplaces, so the same repository installs unchanged:

```
grok plugin marketplace add OverskillDev/plugins
```

### Any other MCP host

Point it at `https://mcp.overskill.com/mcp` with OAuth. Per-host setup —
Cursor, Claude, ChatGPT, Grok Bot, OpenClaw, Hermes Agent — is maintained at
<https://www.overskill.com/connect>.

## Authentication

OAuth only. The host opens Overskill sign-in in a browser and keeps the token;
there is no API key to paste for this server, and no anonymous mode. Scopes are
`apps:read` and `apps:write`.

- `401` means not signed in or an expired token — sign in and retry.
- `403 Missing permission: apps:write` means the token is read-only — re-authorize
  with both scopes. Retrying the same call cannot succeed.

## Two deliberate choices

**`defaultEnabled: false`**, in both `plugin.json` and the marketplace entry. A
plugin that spends money on the user's account should not enable itself.

**No `allowed-tools` grants in any skill.** The skills describe the tools but do
not pre-authorize them, so every build, update, and publish goes through the
host's normal permission prompt. A skill that granted the write tools would let
an agent spend credits without the user seeing a prompt.

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json          # the marketplace catalog (repo root)
├── plugins/
│   └── overskill/
│       ├── .claude-plugin/
│       │   └── plugin.json       # the plugin manifest
│       ├── .mcp.json             # the hosted MCP server
│       ├── skills/               # five job-scoped skills
│       │   ├── overskill-build/SKILL.md
│       │   ├── overskill-update/SKILL.md
│       │   ├── overskill-ship/SKILL.md
│       │   ├── overskill-debug/SKILL.md
│       │   └── overskill-connect/SKILL.md
│       └── agents/
│           └── overskill-app-builder.md
├── ROADMAP.md
└── README.md
```

Every `SKILL.md` uses only the six fields in the [Agent Skills
specification](https://agentskills.io) — `name`, `description`, `license`,
`compatibility`, `metadata`, `allowed-tools` — so the same files load in Claude
Code, Grok, Cursor, and the Skills API without edits. Host-specific keys belong
in `metadata`.

## Validate before publishing

```
claude plugin validate ./ --strict
claude plugin validate ./plugins/overskill --strict
```

Both are clean as of Claude Code 2.1.259.

## Before publishing

Three things this repository cannot verify for itself:

1. **The install.** Run the Claude Code block above against this repository once
   and confirm the eleven tools appear after sign-in.
2. **Grok CLI.** The claim that the same repository installs unchanged rests on
   xAI's documented behaviour, not on a run. Verify it before saying so publicly.
3. **The skill set is five, and each one maps to tools that exist today.** Skills
   for monetization, templates and remix, and third-party integrations are
   deliberately absent: their tools are not on the hosted server, and a skill
   that describes a tool an agent cannot call is a bug report waiting to happen.
   Add them with the tools, not before. [ROADMAP.md](ROADMAP.md) tracks which
   tool each one waits on.

## Releasing a change

`version` appears in two places — `plugins/overskill/.claude-plugin/plugin.json`
and the plugin's entry in `.claude-plugin/marketplace.json`. Bump both, or
`claude plugin tag` will refuse the release tag for disagreeing.

Users pick up a change with `/plugin marketplace update overskill`.

## License

MIT. See [LICENSE](LICENSE).

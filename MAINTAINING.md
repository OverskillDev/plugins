# Maintaining this repository

Notes for whoever changes the plugin. Users read [README.md](README.md) and
[plugins/overskill/README.md](plugins/overskill/README.md).

## Two deliberate choices

**`defaultEnabled: false`**, in both `plugin.json` and the marketplace entry. A
plugin that spends money on the user's account should not enable itself.

**No `allowed-tools` grants in any skill.** The skills describe the tools but do
not pre-authorize them, so every build, update, and publish goes through the
host's normal permission prompt. A skill that granted the write tools would let
an agent spend credits without the user seeing a prompt.

## The skill set tracks the tools

**The skill set is five, and each one maps to tools that exist today.** Skills
for monetization, templates and remix, and third-party integrations are
deliberately absent: their tools are not on the hosted server, and a skill
that describes a tool an agent cannot call is a bug report waiting to happen.
Add them with the tools, not before. [ROADMAP.md](ROADMAP.md) tracks which
tool each one waits on.

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json          # the marketplace catalog (repo root)
├── plugins/
│   └── overskill/
│       ├── .claude-plugin/
│       │   └── plugin.json       # the plugin manifest
│       ├── README.md             # what users see in the directory
│       ├── .mcp.json             # the hosted MCP server
│       ├── skills/               # five job-scoped skills
│       │   ├── overskill-build/SKILL.md
│       │   ├── overskill-update/SKILL.md
│       │   ├── overskill-ship/SKILL.md
│       │   ├── overskill-debug/SKILL.md
│       │   └── overskill-connect/SKILL.md
│       └── agents/
│           └── overskill-app-builder.md
├── MAINTAINING.md
├── ROADMAP.md
└── README.md
```

Every `SKILL.md` uses only the six fields in the [Agent Skills
specification](https://agentskills.io) — `name`, `description`, `license`,
`compatibility`, `metadata`, `allowed-tools` — so the same files are portable across
hosts that follow the spec. Host-specific keys belong
in `metadata`.

## Validate

```
claude plugin validate ./ --strict
claude plugin validate ./plugins/overskill --strict
```

Both are clean as of Claude Code 2.1.259.

## Releasing a change

`version` appears in two places — `plugins/overskill/.claude-plugin/plugin.json`
and the plugin's entry in `.claude-plugin/marketplace.json`. Bump both, or
`claude plugin tag` will refuse the release tag for disagreeing.

Users pick up a change with `/plugin marketplace update overskill`.

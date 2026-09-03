---
name: overskill-connect
description: >-
  Get the Overskill tools connected and working, and interpret an authorization
  failure. Use when the Overskill tools are missing from the toolset, when a
  call returns 401 or 403, when the user asks how to connect Overskill to this
  or another agent, or when a build tool reports it cannot reach the account.
  Covers the one server URL, the OAuth sign-in, what each failure code means and
  which ones are worth retrying, and where the setup steps for other hosts live.
license: MIT
compatibility: >-
  Describes the hosted Overskill MCP server at https://mcp.overskill.com/mcp.
  Network access required. No local runtime and no API key needed.
metadata:
  author: Overskill
  short-description: Connect Overskill and read auth failures
  homepage: https://www.overskill.com/connect
  version: "1.0.0"
---

# Connect Overskill

## The one server

`https://mcp.overskill.com/mcp` — Streamable HTTP, OAuth sign-in, scopes
`apps:read` and `apps:write`. That single URL is the whole configuration.

Sign-in happens in the user's browser and the token stays with the host. There
is no API key to paste for this server and no anonymous mode: an Overskill
account is signed in before anything is built. Never ask the user to paste a
credential into the chat, and never put one in a file you write for them.

## In this agent

This plugin already ships the server definition, so there is nothing to
configure by hand. If the Overskill tools are not in your toolset:

1. The plugin installs disabled on purpose, because it connects to an external
   account. Enable it — `/plugin` in Claude Code, or `claude plugin enable
   overskill`. A newly enabled plugin's MCP server needs `/reload-plugins` or a
   restart.
2. Run `/mcp`, pick `overskill`, and complete the sign-in in the browser.
3. Ask the user to confirm they finished the browser step before you retry a
   call. The sign-in window can outlive a tool timeout.

## Reading the failures

| Signal | What it means | What to do |
| --- | --- | --- |
| Tools absent entirely | The plugin is disabled, or the server was never connected | Enable, reload, then sign in |
| `401` | Not signed in, or the token expired | Re-run the OAuth sign-in, then retry once |
| `403 Missing permission: apps:write` | The token is read-only | The account must re-authorize with both `apps:read` and `apps:write`. Stop; retrying the same call cannot succeed |
| `Unknown tool` | The tool does not exist on this server | Do not fall back to a guessed name or a REST call. List the tools you do have |

401 is worth one retry after a real sign-in. 403 never is.

## Other agents

The same URL works in any host that speaks MCP over HTTPS with OAuth. Per-host
steps — where the field lives, which admin has to enable it first — are
maintained at <https://www.overskill.com/connect>. Send the user there rather
than reciting steps for a host you cannot see.

The Overskill MCP server is remote, so a host that can only run local
subprocesses needs outbound internet access to use it.

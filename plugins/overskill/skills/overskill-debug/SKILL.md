---
name: overskill-debug
description: >-
  Diagnose an Overskill build that failed, stalled, or refused to start, and
  report what actually went wrong. Use when a build's status comes back failed,
  a build seems stuck generating, a tool result says the work was blocked, or the
  user asks why their Overskill app did not build or publish. Covers reading the
  stored failure reason instead of guessing, the blocked and refusal shapes and
  what each one means, and when to hand a human the editor link. Requires a
  connected Overskill account.
license: MIT
compatibility: >-
  Requires the Overskill MCP server at https://mcp.overskill.com/mcp with an
  authorized account. Network access required. Read-only diagnosis; fixing an
  app needs the apps:write scope.
metadata:
  author: Overskill
  short-description: Diagnose a failed or stalled Overskill build
  homepage: https://www.overskill.com/connect
  version: "1.0.0"
---

# Diagnose an Overskill build

The first rule: report the reason the platform gave you. Do not construct a
plausible-sounding cause. If no reason was stored, say that no reason was
recorded and offer the editor link.

## 1. Read the build

`get_build` with the `build_id` returns `status` — `generating`, `ready`, or
`failed` — plus `preview_url`, the latest `screenshot_url` when one exists, and
`editor_url`. On `failed` it includes a stored failure reason when one exists.

`get_app` is the equivalent for an app that is not mid-build: status,
description, preview and live URLs, `published_at`, editor link.

## 2. Match the signal

**`status: "generating"` for a long time.** Large apps take minutes. Keep
polling every few seconds. Do not start a second build to "try again" — that is
a second app and a second charge. If the user wants to walk away, give them
`editor_url`; the build continues without the chat.

**`status: "failed"` with a stored reason.** Quote the reason. If it points at
something the user can restate — an unclear requirement, an unsupported
integration, a file that did not arrive — the fix is a new `update_app` prompt
or a fresh build, not a retry of the identical prompt.

**`blocked_reason: "insufficient_credits"`.** Nothing was started. The team's
balance was too low. Tell the user, and do not retry until they say the balance
has changed. Retrying is the failure mode that wastes their day.

**"Builder access required".** The connected team cannot start builds yet. This
is an account state, not a bug in the prompt. Stop and tell the user.

**A rejected `model` or `thinking_level`.** The value is unknown or not
available on this account's plan. Re-read `list_generation_models` and either
use a listed value or omit both to take the account default.

**A publish refusal** — still building, still a draft, no source files, already
publishing. See `overskill-ship`; these are states, not errors to retry.

**A tool that is missing, a 401, or a 403.** That is a connection or permission
problem, not a build problem. See `overskill-connect`.

## 3. Know what you cannot see

The MCP surface exposes build status, app details, and the editor link. It does
not expose the app's server logs, its database rows, or its deploy history. If
the question needs one of those, say so and point the user at `editor_url`
rather than speculating about the cause.

## 4. Close honestly

When you have made a change, verify it with `get_build` or `get_app` before
telling the user it is fixed. An unverified fix reported as done is worse than
an open problem.

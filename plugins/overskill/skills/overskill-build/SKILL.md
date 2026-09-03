---
name: overskill-build
description: >-
  Build a new hosted web app on Overskill from a plain-language description —
  React and TypeScript, a database, accounts, and hosting included. Use when the
  user asks to build, generate, scaffold, or prototype an app, a landing page, a
  slide deck, or an internal tool and wants it running at a URL rather than in a
  local repo. Covers picking the right create tool, choosing a generation model,
  polling the build to completion, and reporting the preview link. Requires a
  connected Overskill account.
license: MIT
compatibility: >-
  Requires the Overskill MCP server at https://mcp.overskill.com/mcp with an
  authorized account and the apps:write scope. Network access required. No local
  runtime, repository, or build step needed.
metadata:
  author: Overskill
  short-description: Build a new app on Overskill
  homepage: https://www.overskill.com/connect
  version: "1.0.0"
---

# Build an app on Overskill

The build runs on Overskill's servers against the signed-in account. You are
starting and reporting on work, not writing the app's code yourself.

## 1. Pick the create tool

| The user wants | Tool | Notes |
| --- | --- | --- |
| An application — accounts, saved data, forms, interactive features | `create_app` | The default when in doubt |
| A single-page site — marketing, product, event, personal | `create_landing_page` | Takes optional `brand_colors` and an ordered `sections` list |
| A deck presented from a URL | `create_presentation` | Takes an optional `outline` (one entry per slide) and `slide_count` |
| A dashboard, admin panel, CRUD screen, or team utility | `create_internal_tool` | Takes an optional `data_description` |

Pick one. Do not call two create tools for one request.

If the user attached a file in this chat — a screenshot, logo, mock, or PDF —
pass it as `attachment`. Never invent a file reference.

## 2. Choose a model only when the user cares

Omit `model` and `thinking_level` to use the account's default. Call
`list_generation_models` first only when the user asks for a specific model or a
different thinking level, and pass values from that list verbatim — an unknown
or unavailable id is rejected. A model can be unavailable on the account's plan;
if one is rejected, say so and use the default rather than retrying it.

## 3. Report progress honestly

A create tool returns `build_id`, `status`, `preview_url`, and `editor_url`.

- In a host that renders MCP app UI, call `render_build_widget` once with the
  `build_id` right after the create call.
- Otherwise poll `get_build` every few seconds while `status` is `generating`.
  A large app takes minutes; that is normal.
- `preview_url` is the working preview. It is not the app's production URL and
  publishing it is a separate, human-confirmed step — see `overskill-ship`.

Never describe an app as finished, live, or published on the strength of a
create call alone. Say what `get_build` actually returned.

## 4. Stop conditions

- **Low balance.** A result carrying `blocked_reason: "insufficient_credits"`
  means nothing was started. Tell the user the balance was too low and stop. Do
  not retry until they say the balance has changed.
- **Build failed.** `get_build` returns a stored failure reason when one exists.
  Quote it; never guess one. See `overskill-debug`.
- **A create call that appears to time out.** Poll `get_build` with the
  `build_id`, or call `list_apps`, before creating anything again. Repeated
  create calls with identical arguments are collapsed for a short window, but a
  reworded prompt is a second build and a second charge.

## 5. Handing off

`editor_url` is where a human continues in Overskill's own editor. Offer it when
the user wants to take over, tweak visually, or invite a teammate.

---
name: overskill-ship
description: >-
  Publish an Overskill app to its production URL, so the app's real users see
  the current version. Use when the user says to publish, ship, deploy, go live,
  or push an app they already have on Overskill, and to check whether an app is
  currently published. Publishing changes what real users see, so it always
  needs the user's explicit confirmation first. Covers the confirmation, the
  publish call, the refusals it can return, and verifying the result. Requires a
  connected Overskill account.
license: MIT
compatibility: >-
  Requires the Overskill MCP server at https://mcp.overskill.com/mcp with an
  authorized account and the apps:write scope. Network access required.
metadata:
  author: Overskill
  short-description: Publish an Overskill app to production
  homepage: https://www.overskill.com/connect
  version: "1.0.0"
---

# Publish an Overskill app

## 1. Confirm with the user, every time

`publish_app` changes what the app's real users see. Before calling it, tell the
user which app you are about to publish and what changed, then wait for an
explicit yes in that turn. The server cannot tell a confirmed call from an
unconfirmed one — the confirmation is yours to get.

A yes covers the publish you just described. A later change needs a new one.

## 2. Check what you are publishing

`get_app` gives the app's status, its preview and live URLs, and
`published_at`. Use it to answer "is this already live?" and to show the user
what they are about to replace. `list_apps` finds the `build_id` if you do not
have it.

## 3. Publish

Call `publish_app` with the `build_id`. It returns the app version being
published. Then poll `get_app` until `published_at` updates, and report the
production URL from that response.

Do not announce that an app is live before `published_at` moves. A publish takes
a little time.

## 4. Refusals to read, not retry

`publish_app` refuses an app that is:

- still building — wait for `get_build` to report `ready`, then publish
- still a draft
- without source files yet
- already being published — a publish is in flight; poll `get_app` instead

Each of these is a reason to report and stop, not to call again in a loop. If
the user disagrees with the refusal, `editor_url` is where a human sorts it out.

## 5. After publishing

Give the user the production URL and say plainly that this is the version their
users now see. If they then ask for more changes, the loop is
`overskill-update`, a fresh confirmation, then publish again.

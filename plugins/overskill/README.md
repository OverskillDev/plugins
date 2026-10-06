# Overskill for Claude Code

Build, change, and publish full-stack web apps on [Overskill](https://www.overskill.com)
by describing them in plain language. Ask Claude Code for an app, a landing page,
a presentation, or an internal tool, and Overskill builds it, hosts it, and gives
you a live link.

## What you can ask for

- "Build me a booking app for my yoga studio with class schedules and member sign-up, then publish it."
- "Add a waitlist to my Overskill app and show me the preview before we ship."
- "My last Overskill build failed — find out why and fix it."
- "List my Overskill apps and tell me which ones are live."

## What it adds

- **The Overskill server** at `https://mcp.overskill.com/mcp`, which builds, updates,
  checks, and publishes apps on your Overskill account.
- **Five skills** that guide Claude through building, changing, publishing,
  troubleshooting, and connecting.
- **An app-builder agent** that takes a job from first prompt to live URL.

## Before you start

- You need an [Overskill account](https://www.overskill.com). Building and changing
  apps uses your Overskill credits.
- The plugin installs **turned off**, so nothing touches your account until you
  choose to. Turn it on, then run `/mcp` to sign in to Overskill in your browser.
  There is no API key to paste.
- Every build, change, and publish asks for your permission first.

## Install

From the Claude plugin directory, or in Claude Code:

```
/plugin marketplace add OverskillDev/plugins
/plugin install overskill@overskill
/plugin enable overskill
/mcp
```

After enabling, run `/reload-plugins` (or restart) so the server loads, then `/mcp`
to sign in.

## If something goes wrong

- **"Not signed in" or a 401:** run `/mcp` and sign in again.
- **"Missing permission: apps:write":** sign in again and approve both read and
  write access.
- Setup for other tools (Cursor, ChatGPT, and more) is at
  [overskill.com/connect](https://www.overskill.com/connect).
- Help: [overskill.com/help](https://www.overskill.com/help)

## What it can't do yet

See [ROADMAP.md](https://github.com/OverskillDev/plugins/blob/main/ROADMAP.md).
Anything not listed there happens in Overskill's own editor.

## Privacy and terms

[Privacy policy](https://www.overskill.com/privacy) ·
[Terms](https://www.overskill.com/terms)

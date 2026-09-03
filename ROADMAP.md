# Roadmap

What the Overskill MCP server does **not** expose today. Nothing here is
installed by this plugin, and no skill in it claims any of these — an agent that
tries one will get `Unknown tool`.

No dates. Each line ships when its tool ships.

| Not available yet | What it would cover | Waiting on |
| --- | --- | --- |
| Account balance and usage | Reading the connected team's credit balance, tier, and usage history | A read-only balance tool. The builder deliberately does **not** quote what a turn will cost — an estimate that disagrees with the real bill is worse than no estimate — so this reports state, never a forecast |
| Templates and remix | Listing starting points and remixing one into a new app | A templates list and a remix tool |
| App versions and restore | Listing an app's versions and rolling one back | A public versions route, then a confirmed restore tool |
| Environment variables | Reading and setting an app's configuration | A config tool pair. Values are never echoed back; a read returns masked previews |
| Custom domains | Attaching a domain and reporting its DNS state | A domain tool. Buying a domain stays a human step on the web |
| Deploy status | Reporting a publish in flight, beyond `get_app` and `published_at` | A deploy-status tool that reports what the editor reports |
| App logs | An app's runtime logs | A logs tool |
| Third-party integrations | Connecting an app to an external service | An integration tool that returns a sign-in URL for a human to complete |
| Selling from a generated app | Setting up paid products inside an app the user built | Work in flight elsewhere. It will return a draft plus a link a human activates — an agent never completes a purchase or handles a card |

Two rules apply to everything above when it does land:

- **Anything that spends money returns a URL, not a completion.** A human clicks
  it. An agent is never the buyer.
- **No anonymous use.** An account is signed in before anything is generated.

Until a tool exists, the honest answer is that the tools cannot do it and the
work happens in Overskill's own editor. Do not substitute a REST call or a
guessed tool name.

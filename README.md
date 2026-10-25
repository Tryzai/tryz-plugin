# Tryz plugin

Tryz is your personal Playbook for every AI agent: the rules, preferences and info you want every agent to follow when it acts for you (sizes, budget, kosher, Shabbat, tone, a spending ceiling). You set it once at [tryz.ai](https://tryz.ai), and every connected agent reads it and checks its actions against it. Tryz never performs or approves actions itself: it returns an instruction to proceed or stop, and you decide in Tryz.

## What's inside

- `.mcp.json`: the Tryz connector, a remote MCP server at `https://tryz.ai/mcp` (OAuth sign-in to your Tryz account).
- `skills/tryz-playbook/SKILL.md`: tells Claude when to read your Playbook (shopping, trips, recommendations, plans, research) and when to check an action before it acts, including when the topic comes up midway through a conversation.

## Tools

| Tool | What it does | Type |
| --- | --- | --- |
| `read_playbook` | Reads the rules and info you shared with this agent | Read-only |
| `check_action` | Checks a proposed action (purchase, booking, message…) against your rules and returns allow, deny or needs approval | Records the request in your Tryz history |
| `get_action_status` | Gets your decision on a request waiting for approval | Read-only |
| `suggest` | Suggests adding or updating an item in your Playbook; nothing is saved until you approve it in Tryz | Creates a suggestion for you to review |

## Setup

1. Create a free account at [tryz.ai](https://tryz.ai) and build your Playbook.
2. In Claude, add the plugin from the directory (or in Claude Code: `claude plugin marketplace add davidforsher/tryz-plugin`, then `claude plugin install tryz@tryz`).
3. Connect Tryz and sign in. On the consent screen, choose which topics this agent may see.
4. Start a new chat and ask for something, for example to compare running shoes or order dinner.

## Examples

- "Find me running shoes": Claude reads your sizes, brands and budget first.
- "Order the first pair": Claude checks the purchase with Tryz; if it's above your ceiling, it stops and gives you a link to approve or deny in Tryz.
- "What should I watch tonight?": recommendations follow your content rules (for example no violence, only services you have).

## Privacy Policy

Tryz stores only what you put in your Playbook and the agent requests it checks (your history). An agent sees only the topics you shared with it on the consent screen, and you can change or revoke access at any time in Tryz. We don't sell your data and don't share it with third parties, except the AI provider that interprets rules and checks requests. You can export or delete all your data in Settings. Full policy: [tryz.ai/privacy](https://tryz.ai/privacy). Terms: [tryz.ai/terms](https://tryz.ai/terms).

## Support

tryzai.support@gmail.com · [tryz.ai/help](https://tryz.ai/help)

## License

MIT

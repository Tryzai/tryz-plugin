# Tryz plugin

Connects Claude to your Tryz Playbook (https://tryz.ai).

Contents:
- `.mcp.json`: the Tryz connector (`https://tryz.ai/mcp`).
- `skills/tryz-playbook/SKILL.md`: tells Claude when to read your Playbook and when to check an action before acting, including in the middle of a conversation.

Install in Claude Code:

    claude plugin marketplace add davidforsher/tryz-plugin
    claude plugin install tryz@tryz

Version 0.1.0 is an early version. The skill file is a copy of the user's saved `tryz-playbook` skill; update both when one changes.

# Eventstripe — Claude plugin marketplace

Run your entire event ticketing from a chat with Claude: create events, sell tickets,
pull sales reports and manage payouts on [Eventstripe](https://eventstripe.com)
(white-label ticketing for Indonesia — QRIS, GoPay & cards, global payouts).

The `eventstripe` plugin bundles:

- **MCP connector** — `https://connect.eventstripe.com/mcp` (14 ticketing tools, OAuth sign-in)
- **`eventstripe-events` skill** — a guided create-event flow: one batch of questions,
  auto-fill offers for every content block (venue + map pin, description, gallery,
  YouTube video, artist line-up), human-in-the-loop publishing

## Install (Claude Code)

```
/plugin marketplace add TicketServicePlayground/claude-marketplace
/plugin install eventstripe@eventstripe
```

Then just ask: *"Create an event on Eventstripe"* — sign in with your organizer
account (or create one right there) and approve the draft.

## Other assistants

Claude.ai, Cursor, VS Code, ChatGPT and any MCP-enabled assistant can use the bare
connector — see [eventstripe.com](https://eventstripe.com) → "Add to your AI".

## Support

- Website: https://eventstripe.com
- Email: ceo@ltvcac.agency

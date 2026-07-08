---
name: eventstripe-events
description: Create and manage events on Eventstripe through the Eventstripe MCP connector — from a one-line brief to a published event page with tickets, plus sales reports, transactions and payouts. Trigger when the user asks to create an event / sell tickets / check sales on Eventstripe ("create an event", "put tickets on sale", "how are my sales", "заведи ивент", "создай мероприятие").
---

# Eventstripe — run ticketing from chat

You operate Eventstripe (white-label ticketing, Bali/Indonesia, IDR) on the user's behalf through
the **Eventstripe connector** tools. Everything is human-in-the-loop: you propose, the user
approves; never publish or refund without explicit approval.

## 0. Prerequisites

- The Eventstripe connector must be connected. If its tools are unavailable, point the user to
  Settings → Connectors → `https://connect.eventstripe.com/mcp` (or https://eventstripe.com → "Add to your AI").
- Always call `account_status` first: role (organizer / buyer), account state, payment onboarding.
  - Buyer role → only `my_orders`, `order_details`, `resend_tickets` apply.
  - Organizer not ACTIVE → drafts are fine, publishing unlocks after verification.

## 0.5 Organizer page onboarding (new accounts)

Call `get_organizer_page`. If `emptyBlocks` is non-empty — typical for a fresh account — the
public organizer page (the storefront every event links back to) is incomplete. Fold its
questions into the SAME batch as the event questions: brand/company name, page headline,
1-2 sentence intro, about text (offer to draft it), logo + page banner image urls, social
links, contact email. Apply with `update_organizer_page` (read-merge: only provided fields
change; external images are re-hosted on the CDN; bank details are dashboard-only). A new
account should end up with BOTH a complete organizer page and a complete event page.

## 1. Creating an event

### 1a. Essentials — ONE batch of questions (offer defaults; never invent dates or prices)

1. Event name.
2. Date & time (start; end defaults to +6h) + timezone (default `Asia/Makassar`, UTC+8).
3. Venue: name + address — or "Online event" + meeting link (required before publish).
4. Ticket tiers: name, price (whole IDR; paid tickets have a minimum, typically 100,000 IDR),
   quantity. Warn: buyers pay face value + ~20% fees on top; the organizer receives full face value.

### 1b. Content blocks — go through EVERY one, never skip silently

These are the blocks that otherwise ship empty. For each, ask AND bring a concrete auto-fill
proposal the user can accept, tweak, or consciously skip — not an abstract "want to add X?":

- **Venue address + map pin** (`venue_name`, `venue_address`, `venue_lat`, `venue_lng`).
  Web-search the venue's real name and street address; get coordinates from a free geocoder —
  `https://nominatim.openstreetmap.org/search?q=<venue>&format=json&limit=1` (send a User-Agent,
  ≤1 req/s; fallback: `https://photon.komoot.io/api/?q=<venue>`). Verify the result matches the
  intended venue before using it.
- **Full description** (`description`, HTML ok) + `short_description` (1-2 sentences for cards).
  Always draft both from the event's theme (what it is, the vibe, who it's for, what to expect)
  and get them approved. Never leave empty.
- **Line-up / headliners**: ask who performs. `list_artists` to reuse the roster;
  `set_event_lineup` after creation — it creates missing artists automatically (name +
  `photo_url` + music links; find real photos/links via web search).
- **Gallery** (`gallery_urls`): propose 3-6 thematic images — found on the web (prefer
  royalty-free) or generated if an image tool is available. Any public https url works;
  each is re-hosted on the CDN automatically.
- **Atmospheric YouTube video** (`youtube_video_link`): find a real, existing video matching
  the event's vibe via web search; verify the link before using it.
- **Banners**: `preview_banner_url` (card) + `main_banner_url` (header) — user's artwork URL,
  or offer to find/generate one; `upload_image_from_url` re-hosts to the CDN.
- **Category / sub_category / age restriction** — confirm defaults.

**Autofill rule (critical):** only use real, verified data. NEVER invent addresses, coordinates,
YouTube video IDs, artist links or image urls — an invented link ships a broken block to the
live event page. If web search is unavailable, ask the user instead of guessing.

### Then

- `create_event` → always a DRAFT; include everything gathered in 1a + 1b.
- `create_or_update_ticket_type` per tier; `set_event_lineup` for the artists.
- Show a compact summary (name, when + timezone, where, tiers/prices/quantities, plus which
  content blocks are filled vs. skipped) → ask for approval.
- On explicit approval → `publish_event` → share `publicUrl` (promo page) and `adminUrl` (dashboard).
- `update_event` for later edits — only provided fields change.

## 2. Managing & reporting

- `list_events` / `get_event` — inventory and details.
- `sales_report` — tickets sold, revenue; `list_transactions` — individual orders.
- `payout_status` — balance and payout state.
- Fees model: buyer pays face + ~8% service + ~12% government on top. All amounts whole IDR.

## 3. Rules

- One batch of questions, not an interrogation. Offer sensible defaults.
- Never publish, refund or change prices without explicit user approval in this conversation.
- If a tool returns an error message, follow its instructions (they are actionable) and retry once.
- Quote money as `IDR 250,000` style; dates with timezone.

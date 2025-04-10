# Stream Protocol

**Stream Protocol** is a decentralized, lightweight standard for incentivizing work in game ecosystems using [Nostr](https://nostr.com) and [Lightning](https://lightning.network). It lets participants—people, bots, or infrastructure—signal their contributions and earn micro-BTC payments (sats) in a simple, interoperable way. Think of it as a nervous system for games, where value flows based on merit, not middlemen.

---

## Intent

Stream Protocol is built to:
- **Decentralize incentives**: Anyone can signal work and earn sats—no central gatekeepers needed.
- **Simplify value flow**: Lean on Nostr for communication and Lightning for payments, keeping it open and efficient.
- **Standardize flexibly**: Offer a consistent structure for requests, offers, and reputation signals, while letting games set their own rules.

The goal? A plug-and-play system that’s easy to adopt and adapt, powering fair, scalable economies in games.

---

## Problems Solved

Game ecosystems often wrestle with:
- **Centralized control**: Traditional setups trap incentives in silos, shutting out open participation.
- **Complexity**: Custom payment or verification systems are a pain to build, scale, or reuse.
- **Interoperability**: Contributions in one game don’t translate to another—there’s no common language.

Stream Protocol fixes this by:
- **Opening the doors**: Anyone can join, signal work, and get paid in sats.
- **Streamlining the stack**: Nostr and Lightning handle the heavy lifting—no need for bespoke infrastructure.
- **Unifying signals**: A shared schema lets games and systems talk to each other effortlessly.

---

## Schema

Stream Protocol uses a single Nostr `kind` value (`42069`) for all events, with a `["event_type", "<subtype>"]` tag to define what’s happening. The `content` field is optional—keep it empty or toss in a note if you’d like. Tags carry the real payload.

### Event Structure
- **Kind**: `42069`
- **Content**: Optional (`""` or a human-readable string)
- **Tags**: Key-value pairs, including:
  - `["event_type", "<subtype>"]`: Specifies the sub-event (e.g., `"request"`, `"offer"`, `"vibe_vote"`)
  - Sub-event-specific tags (see below)

### Sub-Event Types
1. **Request (`"event_type", "request"`)**  
   Signals a need for work.  
   - **Tags**: 
     - `["work_type", "<type>"]` (e.g., `"matchmaking"`)
     - `["proof_type", "<proof>"]` (e.g., `"player_ids, match_id"`)
     - `["rate", "<rate>"]` (e.g., `"5 sats per match"`)
     - Optional: `["vibe_min", "<score>"]`, `["matches_min", "<count>"]`, etc.

2. **Offer (`"event_type", "offer"`)**  
   Signals completed work for payment.  
   - **Tags**: 
     - `["work_type", "<type>"]`
     - `["proof_type", "<proof>"]`
     - `["proof_value", "<value>"]` (e.g., `"Match1: [p1, p2]"`)
     - `["quantity", "<amount>"]`
     - `["invoice", "<lightning_invoice>"]`
     - Optional: `["vibe_ranking", "<score>"]`, `["total_matches", "<count>"]`

3. **Vibe Vote (`"event_type", "vibe_vote"`)**  
   Signals satisfaction to boost or ding reputation.  
   - **Tags**: 
     - `["bot_id", "<bot_pubkey>"]`
     - `["match_id", "<match_id>"]`
     - `["vote", "+1" or "-1"]`

---

## Example: Matchmaking Bot

Let’s walk through a classic scenario: a game needs a bot to match players, the bot delivers, and players weigh in. Here’s how it plays out with Stream Protocol.

### 1. Game Requests a Matchmaking Bot
The game signals it’ll pay for matchmaking services.  
- **Nostr Event**:
  ```json
  {
    "kind": 42069,
    "content": "",
    "tags": [
      ["event_type", "request"],
      ["work_type", "matchmaking"],
      ["proof_type", "player_ids, match_id"],
      ["rate", "5 sats per match"],
      ["vibe_min", "70"],
      ["matches_min", "1000"]
    ],
    "pubkey": "game_pubkey",
    "created_at": 1712690200
  }
  ```

### 2. Bot Offers Completed Work
A bot steps up, matches players, and submits proof with a payment invoice.  
- **Nostr Event**:
  ```json
  {
    "kind": 42069,
    "content": "",
    "tags": [
      ["event_type", "offer"],
      ["work_type", "matchmaking"],
      ["proof_type", "player_ids, match_id"],
      ["proof_value", "Match1: [p1, p2], Match2: [p3, p4]"],
      ["quantity", "2"],
      ["invoice", "lnbc100n1p..."],
      ["vibe_ranking", "85"],
      ["total_matches", "1500"]
    ],
    "pubkey": "bot_pubkey",
    "created_at": 1712690300
  }
  ```

### 3. Players Signal Vibe Votes
Player 1 likes the match and upvotes the bot.  
- **Nostr Event**:
  ```json
  {
    "kind": 42069,
    "content": "",
    "tags": [
      ["event_type", "vibe_vote"],
      ["bot_id", "bot_pubkey"],
      ["match_id", "Match1"],
      ["vote", "+1"]
    ],
    "pubkey": "player1_pubkey",
    "created_at": 1712690350
  }
  ```

### 4. Game Verifies and Pays
- **Verification**: The game confirms:
  - Vibe ranking: 85 (> 70)
  - Total matches: 1500 (> 1000)
  - Proof: Valid player IDs and match IDs
- **Payment**: Sends **10 sats** (2 matches × 5 sats) to the bot’s Lightning invoice.

This loop—request, offer, vote, pay—keeps the ecosystem humming, with clear signals and instant payouts.

---

## Contributing

Stream Protocol is a living standard. Want to add new sub-events, tweak work types, or improve it? Fork it, hack it, or drop issues/PRs on GitHub. Keep it simple, DRY, and decentralized—that’s the vibe.

# WOCS: Work Order Coordination Settlement

**A Minimal Protocol for Decentralized Coordination in Games — Conceptual**

🏠 **[Overview](https://github.com/decentralized-game-standard)** · 📦 **[AEMS](https://github.com/decentralized-game-standard/aems-standard)** · 🔧 **[RUNS](https://github.com/decentralized-game-standard/runs-standard)** · 🎭 **[MAPS](https://github.com/decentralized-game-standard/ludic-notation-standard)** · ❓ **[FAQ](https://github.com/decentralized-game-standard/.github/blob/main/profile/FAQ.md)**

For as long as ships have entered harbors, pilots have guided them through the channel. The system is ancient and simple. A ship signals its approach. An independent pilot rows out, boards, navigates the passage, and is paid upon safe arrival. No employment contract. No staffing agency. No platform takes a percentage. The harbor provides the meeting point. The pilot provides the skill. The payment settles the exchange. If one pilot retires, another takes the signal. The harbor does not depend on any single pilot, and no pilot depends on any single harbor.

This system has operated continuously in port cities for over two thousand years. It survives because it solved a coordination problem at its minimal surface: broadcast a need, match it to a capable stranger, and settle on delivery.

Digital games have coordination problems that follow the same shape. Servers need hosting. Anti-cheat needs running. Tournaments need brackets. Mods need building. Assets need verifying. Today, these functions are bundled into platforms that own the coordination layer. The platform matches supply to demand, takes a cut, and becomes a dependency. When the platform decides the game is no longer profitable, the coordination dies with it. The game's community fragments because the meeting point was owned, and the owner left.

What if the coordination layer were as ownerless as a harbor entrance?

WOCS is three Nostr events and a Lightning payment. That is the entire protocol.

## Three Events

**Offer** (kind 32001). Broadcast a need with a committed payment amount. A community needs a 50-slot game server hosted for a month. They publish an Offer to Nostr relays: 50,000 sats, monthly, with specific requirements in the content field.

```json
{
  "kind": 32001,
  "tags": [
    ["d", "<unique-id>"],
    ["sats", "50000"],
    ["t", "server-hosting"],
    ["t", "minecraft-rpg-realm"],
    ["duration", "month"]
  ],
  "content": "50k sats/month to host a 50-slot Minecraft server with these mods..."
}
```

Anyone on Nostr can see the Offer. No account on a specific platform required. No discovery algorithm deciding who sees it.

**Fulfill** (kind 32002). Claim delivery with proof and a Lightning invoice. A hosting provider sees the Offer, spins up the server, and publishes a Fulfill referencing the original Offer. The proof is the server address. The invoice is how they get paid.

```json
{
  "kind": 32002,
  "tags": [
    ["d", "<fulfill-id>"],
    ["e", "<offer-event-id>", "", "offer"],
    ["proof", "<server-ip-or-dashboard-link>"],
    ["invoice", "lnbc50000..."]
  ],
  "content": "Server live at example.com:25565, uptime guaranteed..."
}
```

**Ack** (kind 32003). Acknowledge settlement. The community verifies the server is live, pays the Lightning invoice, and publishes an Ack with the payment preimage as proof of settlement.

```json
{
  "kind": 32003,
  "tags": [
    ["d", "<ack-id>"],
    ["e", "<fulfill-event-id>", "", "fulfill"],
    ["status", "paid"],
    ["preimage", "<payment-proof>"]
  ]
}
```

The entire exchange is public, signed, and settled in seconds. A reputation history emerges naturally from the graph of Offers, Fulfills, and Acks without the protocol having to define what reputation means.

## Why This Shape

The protocol enforces event structure and referencing. It does nothing else. Lightning eliminates credit risk because payment is final on delivery. Nostr provides global broadcast because relays replicate events to anyone who asks. Together they produce the minimum viable coordination layer: a harbor entrance that no one owns, where any pilot can show up.

Escrow, reputation scoring, dispute resolution, discovery matching, and compliance belong to services built above WOCS and funded through WOCS. The protocol is the harbor. The services are the port authority, the pilot's guild, the chandlers. Each can be replaced without dismantling the harbor itself.

## What Emerges

Server hosting becomes resilient because hosts compete for recurring Offers. If one host disappears, another fulfills. The infrastructure is antifragile in the precise sense: stress (a host leaving) improves it (the next host is chosen on merit, not on legacy).

Anti-cheat operates as a service. Communities publish Offers for cheat detection. Independent developers build and maintain detection Processors funded by those Offers. Deployment is voluntary. No single anti-cheat provider holds a monopoly.

Tournaments coordinate without organizers owning the infrastructure. A community publishes Offers for bracket management, casting, streaming, and prize pools. Independent operators fulfill each role. The tournament is a composition of WOCS transactions, not a product of a single company.

Mod and asset creation becomes a funded marketplace. A community wants a new character Manifestation. They publish an Offer. An artist fulfills it. Payment settles on delivery. No 30% store cut. No gatekeeping approval process.

Provenance audits keep AEMS entity chains honest. Curators coordinate verification of asset histories through WOCS Offers, funded by communities that value trust in their shared artifacts.

## Why This Works Now

Two technologies matured in parallel to make trust-minimized coordination practical for the first time. Nostr provides permissionless global broadcast: any relay replicates events to anyone. Lightning provides instant, low-cost final settlement: payment clears in seconds, at any amount, with no counterparty risk. Together they replace the platform. The platform's three functions were discovery (solved by Nostr broadcast), trust (solved by cryptographic signatures and public history), and settlement (solved by Lightning). WOCS is what remains when you subtract the platform from the coordination.

## Status

WOCS is a conceptual specification. No production deployments exist. The protocol defines the minimal coordination primitive for digital game ecosystems. Everything above — discovery indexes, reputation aggregators, dispute mediation, specialized clients — remains open territory for anyone to build.

**MIT License** — Implement, extend, experiment.
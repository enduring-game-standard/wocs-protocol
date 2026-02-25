# WOCS: Work Order Coordination Settlement

**A Minimal Protocol for Decentralized Coordination in Games — Conceptual**

🏠 **[Overview](https://github.com/enduring-game-standard)** · 📦 **[AEMS](https://github.com/enduring-game-standard/aems-standard)** · 🔧 **[RUNS](https://github.com/enduring-game-standard/runs-standard)** · 🎼 **[MAPS](https://github.com/enduring-game-standard/ludic-notation-standard)** · ❓ **[FAQ](https://github.com/enduring-game-standard/.github/blob/main/profile/FAQ.md)**

---

## Why Coordination Needs a Protocol

The Enduring Game Standard makes digital games as durable as the rulesets humanity has played for millennia: open rules anyone can implement, persistent artifacts no single entity controls, and execution environments that remain neutral. But durability demands coordination. Servers must be hosted. Anti-cheat systems must be maintained. Provenance chains must be audited. Tournaments must be organized. Unlike a chessboard, which coordinates through physical proximity, a digital game requires active infrastructure sustained by ongoing effort.

Today, that coordination is centralized. A studio hosts the servers, runs the matchmaking, enforces the rules, and funds the upkeep. When the studio closes, changes direction, or decides the game no longer justifies its costs, the coordination dies with it. Counter-Strike: Global Offensive was replaced by Counter-Strike 2 through a direct overwrite in 2023. Demon's Souls lost its World Tendency system permanently when its servers shut down in 2018. In both cases, the games were not technically broken. The coordination infrastructure was removed by the single entity that controlled it.

This fragility is not accidental. It is the predictable outcome of centralized coordination under monetary conditions that reward enclosure over openness. WOCS provides the alternative: a minimal coordination primitive on a commons that no entity can capture, where needs are broadcast openly, delivery is verifiable, and settlement is instant and final.

## The Coordination Challenge

Digital games require active upkeep that physical games largely avoid:

- **Server Architecture** — Persistent, low-latency hosting for synchronized state across players.
- **Anti-Cheat Systems** — Real-time detection and enforcement to preserve fair play.
- **Community Provenance Audits** — Verifiable chains of attribution for mods, assets, and rule changes.
- **Matchmaking & Tournaments** — Scalable organization of competitive play, prize distribution, and event support.
- **Mod & Asset Development** — Ongoing creation and refinement of new entities, processors, and manifestations.
- **Notation & Design Analysis** — Craft contributions such as balance audits, pattern documentation, and design review.

Without neutral mechanisms for strangers to coordinate these functions, they either remain centralized, carrying the risks of capture and abandonment, or depend on fragile volunteer effort that cannot sustain itself economically.

## Mental Model

WOCS is a classified ads board on a commons.

Anyone posts a need. Anyone fills it. Settlement is instant and final. The board is public: every offer, every fulfillment, every acknowledgment is a Nostr event on an open graph. This public history is not a side effect. It is the substrate. Because WOCS events persist on the Nostr commons, they are discoverable by anyone, inheritable by any client, and composable with any tool that reads the graph. A coordination need outlives the person who posted it. A fulfillment record outlives the provider who delivered it. The commons accumulates a public record of what the ecosystem needs and who provides it, creating the conditions for reputation, specialization, and trust to emerge without any protocol-level mechanism enforcing them.

Three events. One cycle.

1. **OFFER** — Broadcast a need with a committed settlement amount.
2. **FULFILL** — Claim delivery with proof and a Lightning invoice.
3. **ACK** — Acknowledge completion with instant payment.

## Why Lightning

WOCS settles exclusively through the Lightning Network. This is not an arbitrary payment integration. Lightning settles in bitcoin, so every WOCS payment is denominated in a non-depreciating asset. Coordination funded through sound money inherits the same patient capital properties the rest of the standard depends on. Lightning also provides instant finality: a payment either completes or it does not, with no intermediate state. Instant finality eliminates credit risk. Without credit risk, there is no need for escrow. Without escrow, there is no need for a trusted intermediary to hold funds. The causal chain is precise: instant finality → no credit risk → no escrow → no intermediary.

This is what distinguishes WOCS from a job board with a payment plugin. A conventional marketplace requires either trust between parties or a platform that custodies funds and mediates disputes. WOCS requires neither. The Lightning invoice attached to a FULFILL event is a self-contained payment instruction. The ACK event with a payment preimage is cryptographic proof that settlement occurred. No platform touches the funds. No intermediary extracts a fee.

For recurring coordination, such as monthly server hosting, the pattern repeats: the offer persists on the Nostr graph, fulfillment is verified each period, and settlement occurs instantly. Resilient infrastructure emerges from this repetition. If one provider fails, the offer remains visible and another provider can respond, because the coordination signal lives on the commons, not on any single provider's platform.

## WOCS Design

WOCS provides the minimal layer for emergent coordination:

- Broadcast offers for recurring or one-time needs: server hosting, anti-cheat services, provenance audits, tournament pools, notation contributions.
- Submit fulfillment with verifiable proof and a Lightning invoice.
- Publicly acknowledge settlement or rejection.

The protocol enforces only event structure and referencing. Everything else, discovery indexes, reputation aggregators, dispute mediation, specialized clients, remains at higher layers, themselves coordinatable via WOCS.

### Relationship to Existing Nostr Protocols

The Nostr ecosystem includes marketplace protocols (NIP-15) for product listings and data vending machines (NIP-90) for job-based data processing. WOCS operates in adjacent territory but serves a distinct purpose: it coordinates ongoing game infrastructure, not one-off product purchases or stateless data jobs. WOCS offers may recur monthly. They reference EGS-specific entity types (AEMS Manifestations, RUNS Processors). They compose with the EGS protocol stack in ways that general-purpose marketplace events do not. WOCS is domain-specific coordination for the game commons, built on the same Nostr substrate that NIP-15 and NIP-90 use for their respective domains.

## What WOCS Deliberately Excludes

Like other EGS primitives, WOCS embraces deliberate minimalism.

| Excluded              | Why                                      | Where It Belongs                  |
|-----------------------|------------------------------------------|-----------------------------------|
| Escrow/custody        | Protocol signals intent, not holds funds | Third-party services              |
| Reputation systems    | Protocol records history, not scores     | Aggregators, community curation   |
| Dispute resolution    | Protocol enables exit, not arbitration   | Voluntary mediation               |
| Discovery/matching    | Protocol broadcasts, not recommends      | Index services, clients           |
| Compliance/KYC        | Protocol is neutral, not jurisdictional  | Service providers as needed       |

Every exclusion preserves optionality and prevents capture. Escrow creates a custodian. Reputation scoring creates a gatekeeper. Dispute resolution creates an arbiter. Each is a function that belongs at a higher layer, where competition and choice remain possible.

## Technical Specification: Three Primitives

WOCS uses standard Nostr kinds.

A community wants low-latency server hosting for their game. They broadcast an OFFER:

### 1. OFFER (kind 32001)

Broadcast a need with committed settlement amount.

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

Mandatory: `d` (addressable), `sats`. Optional tags for filtering and context. The `duration` tag signals recurring coordination: this offer persists on the Nostr graph and expects periodic fulfillment.

A server operator in Osaka sees the offer and responds:

### 2. FULFILL (kind 32002)

Claim delivery with proof and a Lightning invoice.

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

Mandatory: reference to offer, invoice. The proof tag is intentionally unstructured. What constitutes proof varies by domain: a server IP for hosting, a signed attestation for an audit, a link to a deployed Processor for development work.

The community verifies the server is running and pays:

### 3. ACK (kind 32003)

Acknowledge settlement.

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

The `preimage` is cryptographic proof that the Lightning payment completed. Public history emerges naturally on the Nostr graph: anyone can trace which offers were posted, who fulfilled them, and whether settlement occurred. No platform curates this history. No intermediary controls access to it.

## Emergent Capabilities

With WOCS layered atop AEMS (persistent artifacts) and RUNS (neutral execution):

**Resilient Server Networks** — Competing hosts respond to recurring offers, creating redundancy. If one host fails, the offer remains on the Nostr graph and another host responds. The community does not depend on any single provider.

**Distributed Anti-Cheat** — Offers fund open RUNS Processors that detect and enforce fair play. Deployments subscribe to these Processors as needed, paying per period through WOCS.

**Provenance Audits** — Curators coordinate verification of AEMS entity chains, ensuring that Manifestations, Entities, and Assets maintain accurate attribution across forks and extensions.

**Permissionless Tournaments** — Organizers broadcast prize pools. Casters, bracket runners, and streamers fulfill roles. Settlement is instant: no platform holds the prize money.

**Asset & Mod Creation** — Direct coordination for new AEMS Manifestations, RUNS Processors, or community fixes. A designer posts an offer for a new weapon variant. An artist fulfills it. Payment completes before the next session.

**Notation & Craft Contributions** — WOCS coordinates work on the craft itself: funding MAPS pattern documentation, balance analysis, design audits, and Score notation. The economic substrate for cumulative craft is the same three-event cycle that funds server hosting.

**Micro-Contributions** — Small-scale funding for bug reports, balance tweaks, translation, or cosmetic work. Lightning's sub-cent transaction costs make coordination viable at any scale.

All settled instantly, composed openly, recorded on the commons.

## Why This Works Now

Two technologies matured simultaneously: Nostr and Lightning.

Nostr provides permissionless, global broadcast with no API keys, no platform accounts, and no gatekeepers. Any relay can carry WOCS events. Any client can read them. The coordination signals live on a commons that no entity controls.

Lightning provides instant, low-cost final settlement at any scale. A 50,000-sat server hosting payment and a 500-sat bug report bounty use the same settlement mechanism. No minimum transaction size. No platform fee.

Neither alone is sufficient. Nostr without Lightning is signaling without settlement: needs are broadcast but fulfillment cannot be compensated. Lightning without Nostr is payment without coordination: funds can move but needs cannot be discovered. Together, they provide the minimum viable coordination stack for trust-minimized exchange among strangers.

Post an offer. Fulfill one. Extend the ecosystem.

**MIT License** — Implement, extend, experiment.
# WOCS: Work Order Coordination Settlement

**A Minimal Protocol for Decentralized Coordination in Games — Conceptual**

🏠 **Overview** · 📦 **AEMS** · 🔧 **RUNS** · ❓ **FAQ**

---

The Decentralized Game Standard aims to make digital games as enduring, open, and composable as the games humanity has played for centuries: rulesets that anyone can implement, artifacts that persist independently, and execution environments that remain neutral.

Digital games introduce requirements that static physical games do not: real-time networking, state synchronization, anti-cheat enforcement, provenance tracking, and persistent server architecture. These functions demand ongoing, coordinated effort to sustain at scale.

Today, these functions are typically provided by centralized entities. This approach solves immediate coordination challenges but introduces fragility (single points of failure), lock-in (proprietary dependencies), and opportunities for rent extraction on the value created by communities and creators.

WOCS is a minimal coordination primitive built on Nostr that enables independent actors to collectively provide and maintain these essential functions. It consists of three structured events: broadcast a need with committed settlement (OFFER), prove delivery (FULFILL), and acknowledge completion with instant Lightning payment (ACK).

No central platform. No mandatory reputation. No built-in escrow. Just open signals that allow strangers to align incentives and sustain shared infrastructure through voluntary, trust-minimized exchange.

## The Coordination Challenge

Digital games require active upkeep that physical games largely avoid:

- **Server Architecture** — Persistent, low-latency hosting for synchronized state across players.
- **Anti-Cheat Systems** — Real-time detection and enforcement to preserve fair play.
- **Community Provenance Audits** — Verifiable chains of attribution for mods, assets, and rules changes.
- **Matchmaking & Tournaments** — Scalable organization of competitive play, prize distribution, and event support.
- **Mod & Asset Development** — Ongoing creation and refinement of new entities, processors, and manifestations.

Without neutral mechanisms for strangers to coordinate on these needs, the functions either remain centralized (with the attendant risks and costs) or depend on fragile volunteer effort.

WOCS supplies the lightest possible substrate: global broadcast of needs, verifiable claims of delivery, and instant final settlement. This removes counterparty risk and enables market-driven, resilient maintenance by distributed participants.

## WOCS Design

WOCS provides the minimal layer for emergent coordination:

- Broadcast offers for recurring or one-time needs (server hosting, anti-cheat services, provenance audits, tournament pools).
- Submit fulfillment with proof and a Lightning invoice.
- Publicly acknowledge settlement (or rejection).

The protocol enforces only event structure and referencing. Instant settlement via Lightning eliminates credit risk. Global reach via Nostr ensures any capable contributor can participate.

Higher-layer services—discovery indexes, reputation aggregators, dispute mediation, specialized clients—remain open opportunities, themselves coordinatable via WOCS.

## What WOCS Deliberately Excludes

Like other DGS primitives, WOCS embraces radical minimalism.

| Excluded              | Why                                      | Where It Belongs                  |
|-----------------------|------------------------------------------|-----------------------------------|
| Escrow/custody        | Protocol signals intent, not holds funds | Third-party services              |
| Reputation systems    | Protocol records history, not scores     | Aggregators, community curation   |
| Dispute resolution    | Protocol enables exit, not arbitration   | Voluntary mediation               |
| Discovery/matching    | Protocol broadcasts, not recommends      | Index services, clients           |
| Compliance/KYC        | Protocol is neutral, not jurisdictional  | Service providers as needed       |

Every exclusion preserves optionality and prevents capture.

## Technical Specification: Three Primitives

WOCS uses standard Nostr kinds.

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

Mandatory: `d` (addressable), `sats`. Optional tags for filtering and context.

### 2. FULFILL (kind 32002)

Claim delivery.

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

Mandatory: reference to offer, invoice.

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

Public history emerges naturally on the Nostr graph.

## Emergent Capabilities

With WOCS layered atop AEMS (persistent artifacts) and RUNS (neutral execution):

**Resilient Server Networks** — Competing hosts respond to recurring offers, creating redundancy.  
**Distributed Anti-Cheat** — Offers fund open processors; deployments subscribe as needed.  
**Provenance Audits** — Curators coordinate verification of AEMS entity chains.  
**Permissionless Tournaments** — Organizers broadcast pools; casters, bracket runners, and streamers fulfill roles.  
**Asset & Mod Creation** — Direct coordination for new manifestations, processors, or fixes.  
**Micro-Contributions** — Small-scale funding for bug reports, balance tweaks, or cosmetic work.

All settled instantly, composed openly.

## Why This Works Now

Nostr provides permissionless, global broadcast. Lightning provides instant, low-cost final settlement at any scale. Together, they make trust-minimized coordination among strangers practical for the first time—unlocking resilient, community-sustained digital game foundations.

Post an offer. Fulfill one. Extend the ecosystem.

**MIT License** — Implement, extend, experiment.
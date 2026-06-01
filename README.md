# AgentCheckout

> OSS Trusted Surface implementation bridging [A2UI](https://github.com/google/A2UI) rendering and [AP2](https://ap2-protocol.org) mandate signing. Privacy-preserving agent commerce.

[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-pre--alpha-orange.svg)](#status)
[![Phase](https://img.shields.io/badge/phase-0--validation-yellow.svg)](#roadmap)

## The one-sentence pitch

> AgentCheckout is the missing rendering primitive between A2UI and AP2 — an OSS Trusted Surface implementation with privacy-preserving budget mandates that other agent stacks can drop in.

## What it does

When a shopping agent needs the user to sign an [AP2 Intent / Cart / Payment Mandate](https://ap2-protocol.org/ap2/specification/), AgentCheckout:

1. **Renders** the mandate content as an A2UI-extended component (`<af:intent-card>`, `<af:cart-card>`, `<af:payment-confirm>`, `<af:budget-proof>`)
2. **Across substrates** — browser (html-in-canvas), terminal (Ratatui), MCP bridge (legacy)
3. **Captures** user approval via WebAuthn — making the surface a true AP2 Trusted Surface
4. **Optionally attaches** a [Noir](https://noir-lang.org) zero-knowledge proof: "agent is within budget without revealing the budget"
5. **Emits** a W3C Verifiable Credential conforming to AP2 v0.2

## Why it exists

[AP2 v0.2 §Implementation Considerations](https://ap2-protocol.org/ap2/implementation_considerations/) defines a Trusted Surface as the UI that displays mandate content and captures user consent — but the spec leaves the UI to implementers. No OSS implementation exists that is substrate-neutral, clickjacking-resistant (canvas-space hit-region enforcement), and privacy-preserving (ZK budget proofs).

## Status

**Pre-alpha. Pre-build.** Currently in Phase 0 (validation). Built on [`@agentframe/core`](https://github.com/ninan-versioning/agentframe).

PMF bar: **≥10 GitHub stars in the 30 days post-launch**, or this project is publicly marked `POC FAILED` and shelved.

## Roadmap

See [`SPEC.md`](SPEC.md) for the phased specification.

| Phase | Status | Target |
|---|---|---|
| 0 — Pre-build validation | 🟡 active | Day 1 |
| 1 — Foundation (af:* primitives + AP2 schema) | ⚪ queued | Week 1 |
| 2 — Browser substrate (Trusted Surface) | ⚪ queued | Week 2 |
| 3 — Additional substrates (terminal + MCP bridge) | ⚪ queued | Week 3 |
| 4 — ZK budget proof + cross-agent integration | ⚪ queued | Week 4 |
| 5 — Landing page & launch | ⚪ queued | Week 5 |
| Day-31 kill-gate review | ⚪ queued | Week 9 |

## Stage A vs Stage B

This repo is **Stage A** — uses stock [Noir](https://noir-lang.org) for ZK budget proofs. Stage B (planned Q3 2026) swaps the prover for Vega-killer — a separate research project optimizing ZK prover speed against [Microsoft Research's Vega/Spartan2](https://www.microsoft.com/en-us/research/) benchmarks via Karpathy's autoresearch framework. Stage B will be tracked in a separate ticket — Stage A intentionally does not depend on Vega-killer to keep the 30-day kill-gate honest.

## Architecture (the A2UI ↔ AP2 bridge)

```
┌───────────────────────────────────────────────────────────┐
│           SHOPPING AGENT (any LLM, any framework)         │
│  Emits A2A message containing AP2 mandate request +       │
│  A2UI manifest hint                                       │
└────────────────────────────┬──────────────────────────────┘
                             │ A2A (JSON-RPC)
                             ▼
┌───────────────────────────────────────────────────────────┐
│             agentcheckout (this repo)                     │
│  - Parses AP2 mandate skeleton                            │
│  - Selects af:* primitive (af:cart-card etc.)             │
│  - Routes to substrate adapter                            │
└────────────┬────────────────┬────────────────┬────────────┘
             ▼                ▼                ▼
   ┌──────────────────┐ ┌─────────────┐ ┌──────────────┐
   │  Browser         │ │  Terminal   │ │  MCP Bridge  │
   │  setHitTest +    │ │  Ratatui +  │ │  → mcp-ui    │
   │  WebAuthn        │ │  WebAuthn   │ │  (fallback)  │
   └────────┬─────────┘ └──────┬──────┘ └──────┬───────┘
            └──────────────────┼───────────────┘
                               ▼
            ┌──────────────────────────────────┐
            │  TRUSTED SURFACE (AP2)           │
            │  - Display mandate content       │
            │  - Capture user consent          │
            │  - Optional ZK budget proof      │
            │  - Emit signed W3C VC            │
            └──────────────────┬───────────────┘
                               │ Signed VC
                               ▼
            Back to Shopping Agent via A2A
```

## License

Apache-2.0. See [`LICENSE`](LICENSE).

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md).

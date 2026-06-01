# AgentCheckout — Phased Specification (Stage A)

**Owner:** @ninan-versioning
**Builds on:** [`@agentframe/core`](https://github.com/ninan-versioning/agentframe)
**Kill-gate:** ≥10 GitHub stars in 30 days post-launch, or mark `POC FAILED`
**License:** Apache-2.0
**Successor:** AGENTCHECKOUT-002 (Stage B — Vega-killer prover swap, Q3 2026)

---

## Phase 0 — Pre-build Validation (Day 1, ≤4 hours)

**Goal:** Prove AP2 v0.2 + Noir + WebAuthn + AgentFrame core all compose before writing product code.

### Checks

| Check | Acceptance |
|---|---|
| AP2 v0.2 spec is stable enough to generate signed VCs against [google/ap2](https://github.com/google/ap2) reference verifier | reference verifier accepts our generated `IntentMandate` |
| Noir + browser-WASM proves a 20-line budget circuit in <5s on M-series Mac | scratch proves + verifies |
| WebAuthn `navigator.credentials.get()` with mandate-hash challenge works in Chrome Canary | scratch returns signature |
| `@agentframe/core` Phase-1 scaffold accepts custom primitives via plugin API | `<af:test>` registers + renders stub |
| A2A (JSON-RPC) message containing AP2 mandate request can be parsed by our shim | round-trip works |

**Exit:** All 5 green or ticket `BLOCKED`.

---

## Phase 1 — Foundation (Week 1)

**Goal:** Ship the 4 `af:*` primitives as registered AgentFrame extensions + AP2 schema generators.

### Deliverables

- `packages/primitives/` with:
  - `<af:intent-card>` extending A2UI `form`
  - `<af:cart-card>` extending A2UI `comparison-card`
  - `<af:payment-confirm>` extending A2UI `dialog`
  - `<af:budget-proof>` (new primitive, ZK-bound)
- `packages/ap2/` — AP2 v0.2 schema generators for `IntentMandate`, `CartMandate`, `PaymentMandate` as W3C VCs (JSON-LD)
- Verifier sanity test against AP2 reference impl
- Issue / PR templates

### Out of scope

- Rendering (Phase 2)
- ZK proofs (Phase 4)

---

## Phase 2 — Browser Trusted Surface (Week 2)

**Goal:** Browser substrate that meets AP2 Trusted Surface guarantees.

### Deliverables

- `BrowserTrustedSurface` class extending AgentFrame `BrowserSubstrate`
- `<af:cart-card>` rendering via `<canvas layoutsubtree>` + `drawHTMLElement`
- `setHitTestRegions` enforcing Approve/Reject in canvas-space (clickjacking-resistant)
- WebAuthn approval flow → signed Cart Mandate VC
- Demo page (`examples/browser-cart/`) — agent emits manifest → user signs → console shows VC

### Acceptance

A signed `CartMandate` VC produced by AgentCheckout passes verification by the AP2 reference verifier without modification.

---

## Phase 3 — Additional Substrates (Week 3)

**Goal:** Same mandate, three places.

### Deliverables

- Terminal (Ratatui) rendering for all 4 primitives + keyboard handlers
- Platform-authenticator WebAuthn on terminal (Touch ID / libfido2)
- MCP bridge (legacy renderer fallback via mcp-ui)
- `npx agentcheckout-cli demo` works on macOS + Linux

---

## Phase 4 — ZK Budget Proof + Cross-Agent Integration (Week 4)

**Goal:** Privacy-preserving mandate + cross-agent A2A handoff.

### Deliverables

- Noir budget circuit (`packages/zk/circuits/budget.nr`):
  ```
  Public: merchant_id, cart_total, mandate_hash
  Private: weekly_budget, ytd_spend_in_category, category_cap
  Prove: cart_total + ytd_spend ≤ category_cap AND cart_total ≤ weekly_budget
  ```
- Browser-WASM prover, proof <2KB, prove <3s on M-series
- `<af:budget-proof>` attaches proof to Cart Mandate
- A2A end-to-end: Shopping Agent sends mandate request → AgentCheckout renders + signs + ZK-proves → returns signed VC + proof
- Recorded demos (3 substrates × 4 primitives = 12 short clips, or 3 long-form walkthroughs)

### Out of scope

- Vega-killer integration (Stage B / AGENTCHECKOUT-002)
- Push payments, recurring mandates, crypto rails (Stage B)

---

## Phase 5 — Landing Page & Launch (Week 5)

**Goal:** Public OSS launch.

### Deliverables

- **Landing page** (`apps/landing/`) — Next.js + Three.js
  - Hero: animated WebGL of the A2UI ↔ AP2 bridge — mandate JSON flowing through the af:* primitive, signed VC popping out
  - "The seam": diagram showing where AP2's Trusted Surface meets A2UI's primitives, with AgentCheckout in the gap
  - "Why ZK": 60-second animated explainer of budget-proof-without-revealing-budget
  - Live demo embed (browser substrate, can be approved in-page with Passkey)
  - "Drop-in for your AP2 stack" code snippet
  - GitHub stars counter
  - Mailing list signup
- Domain target: `agentcheckout.dev` (or whichever is free)
- HN + Twitter draft at `docs/launch/`
- Tuesday 9:01am ET launch
- Outreach (sent week 6, day before launch):
  - Commercetools dev rel
  - Adyen Developer Network
  - AP2 spec author team via AP2 GitHub Discussions
  - Aztec Network / PSE (Privacy & Scaling Explorations)

---

## Day-31 Kill-Gate Review (Week 9)

**Pass criteria (need 4 of 6):**

| Signal | Pass | Fail |
|---|---|---|
| GitHub stars | ≥10 | <10 |
| Forks | ≥3 | <3 |
| AP2 ecosystem inbound (Adyen / Stripe / Commercetools / Mastercard partner) | ≥1 reply | 0 |
| `--enable-blink-features=CanvasDrawElement` works in CI on GitHub Actions Ubuntu | green | red |
| AP2 v0.2 VCs still verify against [google/ap2](https://github.com/google/ap2) reference | green | red |
| Noir proof time stays <5s on commodity hardware | green | red |

**Failure protocol:** README header changes to `⚰️ POC FAILED`, retrospective posted, repo archived. Vega-killer integration (Stage B) is also cancelled if Stage A fails.

---

## Cross-cutting principles

1. Same as `agentframe`: one issue = one task, PR descriptions are handoffs, brutal-honesty kill-gates.
2. **Compliance posture:** Stage A is a POC-grade Trusted Surface. Production deployment by design partners requires payment-network review. Document explicitly.
3. **No scope creep:** any v1.0 idea goes to `docs/v1-backlog.md`.

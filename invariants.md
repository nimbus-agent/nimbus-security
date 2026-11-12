# Nimbus Security Invariants & Non-Negotiables

> This is a **summary** for external readers. The authoritative, always-current version (with full rationale, anti-patterns, and the wiring/test sites) is [`docs/SECURITY-INVARIANTS.md`](https://github.com/nimbus-agent/Nimbus/blob/main/docs/SECURITY-INVARIANTS.md) in the main repo. This page states the principles and links to that list; it does not copy it.

## The seven non-negotiables

These are architectural constraints, not preferences:

1. **Local-first** — the machine is the source of truth; the cloud is a connector.
2. **HITL is structural** — the consent gate lives in the executor, not the prompt; it cannot be bypassed or configured away.
3. **No plaintext credentials** — Vault only (DPAPI / Keychain / libsecret); never in logs, IPC, or config.
4. **MCP as the connector standard** — the engine never calls cloud APIs directly.
5. **Platform equality** — Windows / macOS / Linux are equally supported.
6. **AGPL-3.0 core / MIT SDK** — the dual license is intentional.
7. **No `any`** — `unknown` for external data; TypeScript strict mode throughout.

## The triple rule

Every invariant ships as a triple in one commit: a **production wiring site**, a **docs entry**, and an **enforcement test** (`packages/gateway/src/security-invariants.test.ts`). Retiring a defense means deleting the row — never leaving drift. A static structural audit (`scripts/structure-audit/check-nimbus-invariants.ts`) enforces a subset before the test suite even runs.

## Where the catalogue lives

The numbered invariants are listed in one place only: the gateway's
[`docs/SECURITY-INVARIANTS.md`](https://github.com/nimbus-agent/Nimbus/blob/main/docs/SECURITY-INVARIANTS.md), with the rationale,
the anti-patterns, the production wiring site and the enforcement test for each.

This page used to carry a copy of that table. The copy went stale while the gateway kept adding
defenses, which is the failure the triple rule exists to prevent, so it was removed on 2026-11-12.
This page does not state how many invariants there are; the canonical document does.

What the catalogue covers, by area:

- **The consent gate** — which actions need the owner's approval, that the gate cannot be
  configured away, and what the owner is shown before approving.
- **Credentials** — where secrets live, and that they never reach logs, IPC, the index or config.
- **Sandboxing** — how connectors, extensions and owner-registered MCP servers are confined.
- **Client exposure** — what the desktop renderer, the local HTTP API and the LAN surface can reach.
- **Federation and identity** — what a peer or a signed-in operator can ask for and trigger.
- **Egress and proof** — the ledger row written before every dispatched action.
- **Extensions and the registry** — signatures, revocation, takedowns and evaluation.

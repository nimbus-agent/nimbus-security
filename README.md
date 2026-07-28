# Nimbus Security

The public security model and coordinated-disclosure process for [**Nimbus**](https://github.com/nimbus-agent/Nimbus) — the local-first AI agent for DevOps and platform teams.

> **Canonical sources.** The authoritative, always-current security documentation lives in the main repo: [`docs/SECURITY-INVARIANTS.md`](https://github.com/nimbus-agent/Nimbus/blob/main/docs/SECURITY-INVARIANTS.md) and the Security Model section of [`docs/architecture.md`](https://github.com/nimbus-agent/Nimbus/blob/main/docs/architecture.md). This repo summarizes that posture for external readers and hosts the disclosure policy. As of this writing the invariant catalogue runs through **I30** and the local schema is **V44**.

## Reporting a vulnerability

Use **GitHub private vulnerability reporting** on the affected repository — its **Security** tab → **Report a vulnerability**. For the Gateway, CLI and connectors that is [Nimbus → Report a vulnerability](https://github.com/nimbus-agent/Nimbus/security/advisories/new).

There is deliberately no security email address and no PGP key; see [SECURITY.md](./SECURITY.md) for why, the full repository list, scope, what to expect, and safe harbor. Machine-readable contact metadata: [`.well-known/security.txt`](./.well-known/security.txt) (served at [nimbus-agent.dev/.well-known/security.txt](https://nimbus-agent.dev/.well-known/security.txt)).

Please report privately — do not open a public issue for a suspected vulnerability.

## Security posture in brief

Nimbus is built on seven architectural **non-negotiables** and a catalogue of structural **security invariants** (I1–I30, with I28 reserved). Each invariant has a production wiring site, a docs entry, and an enforcement test that ship in the same commit (the "triple rule"); several also have a static structural audit that fails the build before the test suite runs.

- **Local-first.** Your machine is the source of truth; the cloud is a connector. Data is indexed into a local SQLite database, not a remote service.
- **No plaintext credentials.** Secrets live only in the OS-native keystore (Windows DPAPI / macOS Keychain / Linux libsecret). They never appear in logs, IPC messages, the index, or config — and are injected into connector child processes via environment variables at spawn time only.
- **Structural HITL.** The human-in-the-loop consent gate for destructive/outbound actions lives in the executor, keyed on a frozen, source-declared action-type set. It cannot be bypassed by a prompt, by configuration, or by an extension.
- **MCP-only boundary.** The engine never calls cloud APIs directly; every integration is an MCP server, making each call auditable and each connector replaceable.
- **Sandboxed, signed extensions.** Third-party connector extensions run sandboxed; `publisher` extensions are Ed25519 signature-verified at install and on every startup.
- **Least-exposure clients.** The desktop renderer reaches the gateway only through an explicit method allowlist (no RCE-class methods) under a restrictive CSP; the local HTTP API is read-only except sanctioned, bearer-authenticated write routes.
- **Provable egress.** Every authorized outbound action appends a row to an append-only, BLAKE3-chained local egress ledger *before* it is dispatched — a denied action records a `blocked` row, and a ledger-append failure aborts the action rather than letting it through. `nimbus prove` verifies the chain.

## Contents

- [SECURITY.md](./SECURITY.md) — coordinated vulnerability disclosure policy.
- [invariants.md](./invariants.md) — the non-negotiables + the I1–I30 catalogue (summary).
- [`.well-known/security.txt`](./.well-known/security.txt) — RFC 9116 contact metadata (published copy: [nimbus-agent.dev](https://nimbus-agent.dev/.well-known/security.txt)).

## License

[MIT](./LICENSE) — documentation. Nimbus core is licensed AGPL-3.0.

# Nimbus Security Invariants & Non-Negotiables

> This is a **summary** for external readers. The authoritative, always-current version (with full rationale, anti-patterns, and the wiring/test sites) is [`docs/SECURITY-INVARIANTS.md`](https://github.com/nimbus-agent/Nimbus/blob/main/docs/SECURITY-INVARIANTS.md) in the main repo. Current through **I30**; schema **V44**.

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

## The invariant catalogue (I1–I30)

`I28` is intentionally absent: it is reserved for a defense on an unmerged branch, and the `I27`→`I29` gap is documented intent rather than drift.

| ID | Guarantee (summary) | Production site |
|----|---------------------|-----------------|
| I1 | Child-process env scoping via `extensionProcessEnv()` | `connectors/lazy-mesh/*` |
| I2 | HITL frozen-set membership (module-private, source-declared) | `engine/executor.ts` `gate()` |
| I3 | HITL gate consults `action.type` only, never `payload.mcpToolId` | `engine/executor.ts` |
| I4 | `hitlStatus` set only by the consent gate | `engine/executor.ts` |
| I5 | `checkLanMethodAllowed` intrinsic to `LanServer` | `ipc/lan-server.ts` |
| I6 | LAN bind defaults to `127.0.0.1` | `config/nimbus-toml.ts` |
| I7 | Tauri `ALLOWED_METHODS` excludes RCE-class methods | `ui/src-tauri/src/gateway_bridge.rs` |
| I8 | Restrictive renderer CSP (no `unsafe-inline`/`unsafe-eval`) | `ui/src-tauri/tauri.conf.json` |
| I9 | Bound-param SQL; identifiers via `escapeIdentifier` | `db/write.ts`, `db/repair.ts`, `people/person-store.ts` |
| I10 | Constant-time compare for hashes/MACs/pairing-codes/tokens | `util/timing-safe-compare.ts` |
| I11 | LLM-facing results wrapped via `wrapToolOutput` | `engine/agent.ts`, `engine/tool-output-envelope.ts` |
| I12 | DPAPI passes `pOptionalEntropy` from a vault entropy file | `vault/win32.ts` |
| I13 | HTTP writes via `WRITE_ROUTE_ALLOWLIST` + bearer auth | `ipc/http-server.ts`, `ipc/http-write-routes.ts` |
| I14 | SQLite writes via `dbRun`/`dbExec`/`dbStmtRun` | `db/write.ts` |
| I15 | Every lazy-mesh `ServerSpec` via `wrapServerSpec()` → sandbox | `connectors/lazy-mesh/*` |
| I16 | Ed25519 signature verify at install + every startup for `publisher` extensions | `extensions/{install-from-local,verify-extensions}.ts` |
| I17 | Federated answering only in the consent-scoped, leak-proof query gate | `federation/query-gate.ts` |
| I18 | IdP token validation only in `identity/verifier.ts`; raw tokens Vault-only | `identity/verifier.ts` |
| I19 | Team-vault secrets consumed only via the principal-polymorphic invoke gate | `federation/invoke-gate.ts`, `teamvault/team-tool-invoke.ts` |
| I20 | A delegated HITL approval is honored only from a live, in-scope, identity-valid delegate | `engine/delegated-approval.ts` |
| I21 | Quorum counts only DISTINCT authenticated peers (deny aborts, fail-closed) | `engine/quorum/quorum-coordinator.ts` |
| I22 | Signature-verified org policy + monotonic-stricter resolution (tighten-only, fail-closed) | `policy/policy-gate.ts` |
| I23 | ChatOps operational posts go only to a server-derived `ReplyTarget` (never caller-supplied) | `chatops/reply-dispatcher.ts` |
| I24 | A federated preflight action runs only behind the LOCAL owner's HITL gate, sandboxed, config-resolved | `federation/preflight-gate.ts` |
| I25 | Tribal-knowledge KB capture writes only to the config-pinned destination, behind local-owner HITL | `tribal/tribal-write-gate.ts` |
| I26 | Connector writes (warehouse/BI ∪ GitOps/ML) run only behind local HITL; a peer can never trigger a connector write (fail-closed) | `federation/invoke-gate.ts`, `connectors/connector-write-registry.ts` |
| I27 | An outbound share leaves only through the share-gate (origin) or forward-gate: redaction + owner-approved preview + Vault-signed + audited, fail-closed; a received share is inert | `share/share-gate.ts`, `share/share-forward.ts`, `share/share-keypair.ts` |
| I29 | Egress-ledger completeness: every gated action appends one BLAKE3-chained `egress_ledger` row before dispatch (blocked row on deny; append failure aborts) | `engine/executor.ts`, `egress/*` |
| I30 | Web-clipper token minting is fail-closed behind a live, single-use, owner-opened pairing window; minted tokens are Vault-stored and revocable | `clips/pairing-window.ts`, `clips/clip-token-store.ts` |

For the rationale, anti-patterns, and the exact enforcement test behind each row, read the canonical [`docs/SECURITY-INVARIANTS.md`](https://github.com/nimbus-agent/Nimbus/blob/main/docs/SECURITY-INVARIANTS.md).

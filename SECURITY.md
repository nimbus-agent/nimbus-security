# Coordinated Vulnerability Disclosure Policy

We take the security of Nimbus seriously — it is a local-first agent that holds credentials and acts on production systems on a user's behalf. Thank you for helping keep it safe.

## How to report

> ⚠️ **PLACEHOLDER — confirm before publishing:** set a real security contact below (a monitored inbox, ideally with a published PGP key).

- **Email:** `security@nimbus.dev` *(placeholder)*
- Encrypt sensitive reports with our PGP key *(placeholder — publish a key fingerprint here)*.
- Do **not** open a public GitHub issue, discussion, or PR for a suspected vulnerability.

Please include: affected component and version, a description of the issue, reproduction steps or a proof of concept, and the impact you believe it has.

## Scope

In scope (the AGPL-3.0 core and MIT SDK):

- `gateway` — engine, HITL gate, Vault, local index, IPC, federation/identity.
- `cli` and the IPC surface it uses.
- `mcp-connectors/*` — first-party connectors.
- `sdk` / `client` — the public extension and IPC surfaces.

Examples of in-scope issues: credential exposure (plaintext leakage via logs/IPC/index/config), HITL gate bypass (executing a gated action without consent), sandbox escape from an extension, signature-verification bypass for `publisher` extensions, renderer reaching an RCE-class method, SQL injection, or prompt-injection that escalates to a real action.

Out of scope: issues requiring a pre-compromised machine or OS account, social-engineering, denial-of-service from unrealistic resource exhaustion, and findings solely in third-party cloud APIs.

## Our commitment

- We aim to **acknowledge** a report within **3 business days** *(placeholder SLA — adjust to your capacity)*.
- We will keep you informed of remediation progress and coordinate a disclosure timeline.
- We support **coordinated disclosure** and will credit reporters who wish to be named.

## Safe harbor

We will not pursue or support legal action against researchers who, in good faith: report promptly, avoid privacy violations and data destruction, only interact with accounts/systems they own or have permission to test, and give us reasonable time to remediate before public disclosure.

# Coordinated Vulnerability Disclosure Policy

We take the security of Nimbus seriously — it is a local-first agent that holds credentials and acts on production systems on a user's behalf. Thank you for helping keep it safe.

## How to report

**Use GitHub private vulnerability reporting (PVR) on the affected repository.** Open the repository's **Security** tab → **Report a vulnerability**, or use the direct link below. The report is private to the maintainer, gives you a private fork to collaborate on a fix, and issues a CVE on publication.

| Repository | Report a vulnerability |
|---|---|
| Gateway, CLI, MCP connectors, docs (`Nimbus`) | [Report](https://github.com/nimbus-agent/Nimbus/security/advisories/new) |
| `nimbus-sdk` — extension-authoring contract | [Report](https://github.com/nimbus-agent/nimbus-sdk/security/advisories/new) |
| `nimbus-client` — typed IPC wrapper | [Report](https://github.com/nimbus-agent/nimbus-client/security/advisories/new) |
| `nimbus-vscode` — VS Code / Open VSX extension | [Report](https://github.com/nimbus-agent/nimbus-vscode/security/advisories/new) |
| `nimbus-web-clipper` — Chrome / Firefox MV3 extension | [Report](https://github.com/nimbus-agent/nimbus-web-clipper/security/advisories/new) |
| Packaging: `homebrew-tap`, `scoop-bucket`, `linux-repo` | [tap](https://github.com/nimbus-agent/homebrew-tap/security/advisories/new) · [scoop](https://github.com/nimbus-agent/scoop-bucket/security/advisories/new) · [linux](https://github.com/nimbus-agent/linux-repo/security/advisories/new) |

Filing requires a (free) GitHub account. If you are unsure which repository is affected, report against [`Nimbus`](https://github.com/nimbus-agent/Nimbus/security/advisories/new) and we will move it.

Please do **not** open a public issue, discussion, or pull request for a suspected vulnerability.

Please include: affected component and version, a description of the issue, reproduction steps or a proof of concept, and the impact you believe it has.

### No email or PGP contact

We deliberately do **not** publish a security email address or a PGP key. Nimbus is maintained by a single developer, and an unmonitored inbox or an unrotated key is worse than no channel at all — it silently drops reports. GitHub PVR gives you a private, authenticated, end-to-end channel that is actually watched, so it is the only reporting channel we offer. If PVR is genuinely unusable for you, open a public issue that says only "requesting a private security contact" with no technical detail, and we will arrange one.

## Scope

In scope (the AGPL-3.0 core and MIT SDK):

- `gateway` — engine, HITL gate, Vault, local index, IPC, federation/identity, egress ledger.
- `cli` and the IPC surface it uses.
- `mcp-connectors/*` — first-party connectors.
- `sdk` / `client` — the public extension and IPC surfaces.
- The VS Code extension and the browser web clipper.

Examples of in-scope issues: credential exposure (plaintext leakage via logs/IPC/index/config), HITL gate bypass (executing a gated action without consent), egress-ledger bypass (dispatching an action that writes no ledger row), sandbox escape from an extension, signature-verification bypass for `publisher` extensions, renderer reaching an RCE-class method, web-clipper token minting without an owner-opened pairing window, SQL injection, or prompt-injection that escalates to a real action.

Out of scope: issues requiring a pre-compromised machine or OS account, social-engineering, denial-of-service from unrealistic resource exhaustion, and findings solely in third-party cloud APIs.

## What to expect

Nimbus is maintained by one person as a side project. We are not going to promise a response time we cannot keep, so instead, honestly:

- There is **no guaranteed response time and no SLA.** Reports are typically read within a week, but a stretch of travel or day-job load can make that longer.
- We will confirm receipt when we first read the report, tell you whether we consider it in scope, and keep you updated as we work on a fix.
- Fixes are prioritised by severity. A credential-exposure or HITL/egress-bypass report jumps the queue.
- We support **coordinated disclosure**: we will agree a disclosure timeline with you rather than sitting on a report indefinitely, and if you have not heard from us in **30 days**, you should consider yourself free to disclose publicly.
- We will credit reporters who wish to be named in the published advisory.
- There is **no bug-bounty programme** and no monetary reward — this is an unfunded open-source project.

## Safe harbor

We will not pursue or support legal action against researchers who, in good faith: report promptly, avoid privacy violations and data destruction, only interact with accounts/systems they own or have permission to test, and give us reasonable time to remediate before public disclosure.

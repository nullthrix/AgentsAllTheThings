# VRT Severity Matrix — P1–P5 digest
# Severity/coverage map for planning hunts, writing findings, and the
# pre-close checklist. Based on the Bugcrowd Vulneruntary Rating
# Taxonomy. THE PROGRAM'S OWN SEVERITY TABLE ALWAYS WINS — read it first
# in the scope file; this matrix is the default when the program is
# silent.

## Tiers

| Tier | Label | Submit? |
|---|---|---|
| P1 | Critical | yes |
| P2 | High | yes |
| P3 | Medium | yes |
| P4 | Low | recorded, not submitted (unless the scope widens the floor) |
| P5 | Info | noted in ledger only |

## Class → typical tier (default floor = P1–P3)

### P1 — Critical
- server-side code execution (RCE, command injection, SSTI→RCE)
- SQL injection reaching sensitive data
- authentication bypass (no user interaction) / full account takeover
- stored XSS on sensitive surfaces chaining to ATO (admin session)
- access to all data of other users (broken isolation at scale)
- SSRF into internal cloud metadata yielding credentials

### P2 — High
- stored XSS (user-facing, impact demonstrated)
- IDOR / BOLA on sensitive objects (PII, payments, documents)
- privilege escalation (user → admin, tenant cross-over)
- business-logic abuse: price manipulation, free goods, fund moves
- 2FA / OTP bypass
- password reset poisoning / token leakage → ATO
- race condition with monetary/data impact
- SSRF reaching internal services (non-metadata)
- authentication bypass requiring user interaction

### P3 — Medium
- reflected XSS with realistic delivery
- IDOR on non-sensitive objects
- CSRF forcing state changes (email change, settings)
- open redirect on OAuth/login chains
- information disclosure (sensitive keys, internal structure)
- JWT flaws with demonstrated abuse
- WebSocket auth gaps
- cache poisoning/deception with impact
- subdomain takeover (depends on the subdomain's sensitivity)

### P4 — Low
- session expiry / logout gaps
- verbose errors, stack traces
- mixed content, missing security headers
- username enumeration
- CSRF on non-sensitive actions
- open redirect (bare, no chain)
- self-XSS-only contexts (NOT reportable at all — keep as evidence)

### P5 — Info
- best-practice observations, theoretical issues with no impact

## Web3 exception
On-chain programs grade on their own scale (Critical / High / Medium /
Low + Insights). There, Low and Insights ARE hunted and reported —
gas griefing, missing events on critical state, rounding favouring the
protocol, tx.origin auth, weak randomness, front-runnable transitions,
centralisation risk WITH a concrete path.

## Pre-close checklist
- [ ] every P1–P3 class above either tested, N-A, or SKIP-with-reason
- [ ] anomaly register empty or fully triaged
- [ ] ledger rows complete for every lane fired (incl. negatives)
- [ ] all in-scope assets covered
- [ ] findings' severities cross-checked against the program's own table

# Scenario Batteries — per-class test scenarios
# The source list the battery-triage skill enumerates from. Before probing
# ANY class: copy the relevant block into a triage table, mark each row
# TEST / N-A / SKIP-with-reason, declare falsifiers per TEST row, THEN fire.
# Every row — including negatives and skips — lands in the engagement
# ledger. These are ignition material, not a script: escalate and combine.

--------------------------------------------------------------------------------

## Authentication (login)
- username enumeration via login error divergence / timing / reset flow
- response manipulation: flip success:false→true, status flags, JSON roles
- SQLi / NoSQLi in login fields (error, boolean, time, OOB shapes)
- default / weak creds on admin surfaces (bounded, where RoE allows)
- login via every alternate channel (mobile API, SSO, legacy, GraphQL)
- rate-limit / lockout bypass (spoofed headers, IP rotation = prompt-first)
- case/unicode tricks on username (admin vs ADMIN vs аdmin)

## Registration & verification
- register with disposable mailbox; verify the token is: single-use?
  time-bound? bound to the account? predictable?
- unverified-account capabilities: what works before clicking the link?
- verification link host-header poisoning (link built from Host header)
- re-send verification to a different address (token/cookie confusion)
- account pre-hijack: register a victim address before victim does

## Password reset (ATO class)
- reset token in URL? → predictability, reuse, expiry, account binding
- reset link leaks token in response body / referrer
- host-header poisoning of the reset email link
- reset request for A while logged in as B (account confusion)
- email change without current-password / without re-verification
- reset token reuse across users; token accepted for arbitrary account id
- response-based bypass: submit wrong code, intercept, tamper status

## 2FA / MFA
- OTP brute space + rate limit (bounded counts only)
- response manipulation on OTP verify (flip status)
- skip-step: navigate past the OTP screen by direct URL / API call
- remember-device / trusted-device token forgery or theft
- backup codes: reuse, predictable, generated before 2FA completes
- OTP replay after successful use; negative-verification logic bugs
- 2FA enforced on login but NOT on password change / email change / SSO

## Sessions & tokens
- session invalidation on logout / password change / 2FA change
- session fixation: pre-auth cookie survives login
- token scope: one session valid across all in-scope subdomains/roles
- cookie flags (HttpOnly/Secure/SameSite) and their actual enforcement
- concurrent-session policy; old token after "log out all devices"
- JWT: alg=none, HS/RS confusion, weak HMAC secret, kid injection,
  exp/nbf ignored, audience/issuer unvalidated, claim tampering
- token in URL / referrer / logs

## OAuth / OIDC / SSO
- redirect_uri validation: open, suffix-match, path tricks, //, encoded
- state/nonce missing or not validated (CSRF on the callback)
- authorization code reuse; code redemption by different client
- IDP mixup: token from IdP-A accepted by client-B
- account linking without re-verification (pre-ATO bind)
- email-trust: register via IdP with an email that matches a local user
- token leakage to referrer/logs/redirect chains

## IDOR / BOLA (pairs, both directions)
- object refs (id/uuid/slug) in path, body, params, headers — A↔B + anon
- same object via every channel: REST vs GraphQL vs mobile vs webhook
- ID encoding swaps that should not matter (int↔uuid↔base64 of same id)
- list endpoints leaking other tenants' objects
- write ops: update/delete on B's object from A
- metadata endpoints (count, export, preview, async-job) on B's objects

## Tenant / workspace isolation
- cross-tenant join via invite token reuse or direct API
- tenant id in JWT vs body vs path — which one wins?
- per-tenant feature/data gates reachable from another tenant
- user enumeration across tenants

## Access control / BAC
- forced browsing to role-gated pages/APIs directly
- hidden/disabled UI actions reachable via API (hidden ≠ protected)
- mass assignment: role, isAdmin, tenantId, plan, verified in updates
- stale tab after role revocation; revoked state respected later?
- vertical escalation: user→admin params, admin endpoints on user token
- 403/401 bypass battery: verb swap, path normalisation (//, /./, ..;/,
  %-encode, trailing dot/slash, case), X-Forwarded-For / X-Real-IP /
  X-Original-URL / Forwarded header set, alternate common ports

## Invites & teams
- invite token: reuse, cross-tenant, expiry, role at accept
- join without invite via ID guess; accept on behalf of another user
- role escalation at accept time; admin-gated actions post-join

## Business logic / payments (write invariants FIRST)
- client-supplied price / quantity / currency / discount tampering
- step-skip and reorder in checkout / wizard flows
- replay & duplicate submission (race + retry)
- negative / zero / fractional quantities; integer overflow
- rounding direction; coupon stacking; entitlement abuse
- refund / cancellation abuse; trial re-entry
- wallet/credit: concurrent spends (race), negative balance

## Race conditions
- single-shot race windows (parallel HTTP) on: balance, limit, one-time
  code, draw, vote, coupon, withdrawal
- multi-endpoint races (add-to-cart vs checkout)
- verify persisted state owner-side, never from response alone

## State machines / workflows
- transition table first: permitted initiators, preconditions, re-check
  at target state, partial-state on failure, revocation respect
- unexpected transitions; revived revoked/deleted state
- completion without prerequisites; stale IDs; replay of earlier steps

## SSRF
- URL params: url, fetch, src, dest, redirect, uri, path, callback, next,
  data, reference, file, document, folder, page, feed, host, port
- webhooks / importers / renderers / PDF generators / avatar-from-URL
- internal reach proof via OAST (interactsh) — reachability only
- redirect-to-internal chains; DNS rebinding (notes only)
- metadata endpoints where policy allows (169.254.169.254)

## XSS
- context analysis FIRST (HTML / attribute / JS string / URL / CSS)
- reflected / stored / DOM (sink tracing: innerHTML, document.write,
  eval, location sinks, jQuery href)
- source matters: URL params, fragments, postMessage, window.name
- SVG upload → stored XSS; email-field payloads; filename payloads
- CSP present? note it, try allowed-scheme/import chains
- stored payload seen by staff = blind XSS lane (one payload/surface)

## SQLi / NoSQLi
- error / boolean / time / OOB shapes; headers (X-Forwarded-For, UA,
  Referer, cookies), sort/order/column params, JSON keys
- NoSQL: $ne/$gt operators, $where, Mongo JSON injection
- second-order: stored payload executed by a later query

## Command injection / SSTI / file upload
- command: OAST confirmation first; separators, backticks, $( ), newline
- SSTI: detection probes ({{7*7}}, ${7*7}, <%= 7*7 %>), then per engine
- upload: extension/MIME/content gaps, double extensions, null bytes,
  path traversal in filename, content-type confusion, SVG/HTML polyglots

## CSRF / CORS
- state-changing endpoint without token / with removable token
- token validation parity across channels (form vs JSON vs API)
- SameSite bypass shapes (subdomain origin, POST→GET)
- CORS: origin reflection, null origin, subdomain/suffix trust,
  credentialised reflection — impact required, header alone ≠ finding

## GraphQL
- introspection on/off; field-suggestion brute
- batching / aliasing to defeat rate limits
- depth/complexity abuse (DoS — only where RoE allows)
- IDOR through nodes/edges/relay ids; mutations lacking REST parity
- token in query string; errors leaking schema

## WebSockets
- origin validation on connect; auth per-message vs connect-only
- message replay / cross-channel command confusion
- subscription to another user's channel (IDOR)

## Host header / cache / redirect
- host header in password-reset links / absolute URLs / cache keys
- cache poisoning/deception: unkeyed inputs, path-extension tricks,
  fat GET (non-destructive shapes only)
- open redirect: param sweep, //evil, validated-redirect bypass,
  redirect chains into OAuth callbacks

## Information disclosure
- verbose errors, stack traces, debug params (?debug, ?test)
- backup/config files, .git exposure, exposed sourcemaps (in-memory
  analysis only), api docs (swagger) reachable unauthenticated
- source maps revealing internal routes; response headers leaking
  versions/frameworks → CVE lane

# Bug Bounty Skill Catalog — names + one-line purposes
# The bootstrap GENERATES the real skill files from this catalog — it does
# not copy anything verbatim. One skill per name below, written into the
# opencode skills folder (~/.config/opencode/skills/), authored by the
# agent in its own words, expanded from the one-line purpose here and the
# canon in globalRules.md.
#
# GENERATION CONTRACT — every generated skill must define:
#   TRIGGER  — the exact moment the skill fires
#   FLOW     — what it does, step by step
#   ARTIFACT — what it leaves on disk (no artifact = did not happen)
# Rule of thumb: a skill fires BEFORE its class is probed, and every
# skill's FLOW obeys the gates in globalRules.md (pacing, scope, pauses).

--------------------------------------------------------------------------------

## PHASE 0 — ENGAGEMENT SETUP

- scope-parser — turn a handed-over program/policy brief into the scope
  file: in-scope assets, exclusions, RoE, severity table, Program URL,
  wildcard/enum determination.
- engagement-bootstrap — on first task for a new target: engagement dir
  tree, STATE file, empty anomaly/lead/GAP registers.
- battery-triage — before the FIRST probe of ANY vulnerability class:
  enumerate all scenario sources into a triage table with falsifiers
  declared; every row test / N-A / SKIP-with-reason.
- policy-checker — re-fetch the live policy from the scope file's Program
  URL; check findings against eligible assets, tiers, disqualifiers.

## PHASE 1 — RECON (BROWSERLESS PASS)

- recon-pipeline — the standard subdomain-enum + httpx + curated-picklist
  + restricted nmap top-100 pass for wildcard scopes, honouring the
  no-wildcard gate and pacing.
- dns-hygiene — per-engagement DNS set: NS discovery, one-shot AXFR,
  DNSSEC/CAA, wildcard behaviour, dangling-CNAME takeover sweep,
  SPF/DMARC/DKIM posture.
- fingerprinter — tech/server/title fingerprint of every live host; diff
  against the previous pass — a changed app is a fresh lead.
- port-sweeper — restricted nmap top-100 (no NSE, no -p-, once per
  engagement) plus single-shot probing of every open port found.
- origin-hunter — passive-first origin discovery (TLS-SAN, CT records,
  Wayback archaeology, SPF/MX includes, conventional origin names);
  ownership-prove candidates before any probe; evaluate edge-gate bypass.
- takeover-hunter — match dangling CNAMEs against the takeover-able
  service list; HTTP-verify only the suspicious few.
- js-analyst — in-memory JS battery over every live host (encoded values,
  IPs, sinks, secrets, URLs, logic analysis); never write target
  JS/JSON/YAML to disk — derived hit lists only.
- endpoint-cartographer — the deduplicated endpoint inventory built from
  BOTH the browserless and browserly passes, tagged with source.
- embedded-url-sweeper — extract and test URLs inside any discovered
  json/js/yml/config artefact, in memory; save only tagged derived lists.
- secret-tracker — classify and exploit-test secrets-adjacent hits;
  low-signal ones NOTED, never submitted.
- intel-platform-consult — default-consult the intel platforms (Netlas,
  ZoomEye, FOFA, Shodan, Censys) whenever their data can answer a
  question; record usage including negatives.
- cve-matcher — map fingerprinted outdated tech to public CVEs; bounded
  non-destructive PoC where warranted and in scope.

## PHASE 2 — IDENTITY & ACCESS

- auth-hunting — on FIRST touch of any authentication surface: auth
  state-machine modelling, unverified-account capabilities, verification-
  token binding, login channels, OAuth trust shapes, pair tests.
- session-manager — token meaning/predictability/disclosure, fixation,
  invalidation on logout/password/2FA change, concurrent-session and
  scope-confusion checks.
- mfa-bypass — OTP rate limits, response manipulation, skip-step and
  reorder, backup codes, remember-device binding, OTP reuse.
- oauth-flow-mapper — model each OAuth/OIDC flow: redirect_uri
  validation, state/nonce, code binding and reuse, open-redirect chains
  into the callback, token leakage, IDP mixup.
- account-linking — pre-ATO bind tests: email change to an address
  verified elsewhere, second-IdP link without re-verification, token
  replay across accounts.
- invite-abuse — invite token reuse, cross-tenant join, role escalation
  at accept, uninvited join, expired-invite revival.
- password-reset — reset-token predictability/binding, host-header
  poisoning of the link, token leak in responses, reset-after-email-
  change, both-identity pair tests.

## PHASE 3 — AUTHORIZATION & OBJECT ACCESS

- bola-pair — both-direction object-access pairs (A→B, B→A, anonymous
  where safe) via every channel, plus metamorphic should-not-change
  swaps; single-identity coverage is PARTIAL, never clean.
- tenant-isolation — cross-tenant reads/writes, tenant-id confusion
  across JWT/body/path, tenant-scoped enumeration leaks.
- mass-assignment — role/plan/tenantId/verified fields in update
  payloads; hidden UI actions reached directly; stale-tab re-checks.
- access-control-bypass — the full bypass battery before any BLOCKED
  verdict: verb swaps, path normalisation, proxy headers, Host/SNI
  confusion, alternate ports, egress rotation; positive control included.

## PHASE 4 — BUSINESS LOGIC & FLOWS

- state-machine-modeller — write the lifecycle transition table BEFORE
  probing transitions; unexpected transitions go to the anomaly register.
- payment-logic — invariants written first; then client-supplied price/
  quantity/currency, step-skip, reorder, replay, duplicates, rounding,
  coupon and refund abuse.
- entitlement-abuse — trial re-entry, tier-gate server-side enforcement,
  quota resets, downgrade feature retention, feature-flag tampering.
- race-condition — single-shot race windows on valuable state changes;
  persisted state verified owner-side, never from the response alone.
- workflow-bypass — wizard step-skip, out-of-order submission, stale IDs,
  replay of earlier steps, completion without prerequisites.

## PHASE 5 — INJECTION & TECHNICAL CLASSES

- sqli-hunter — error/time/boolean/out-of-band shapes manually first,
  automated triage only where RoE allows; minimal report-sized proof.
- xss-hunter — context analysis first, minimal in-page canaries,
  stored-vs-reflected distinction, DOM sink tracing; execution proof, not
  alert parades.
- blind-xss — one payload per distinct surface, fired after a valid
  value; collector hit = observed execution; self-XSS never reportable.
- ssti-hunter — engine detection, then engine-specific benign canaries;
  per-engine payload reference.
- command-injection — OAST confirmation first, time-based only where OAST
  is impossible; nothing destructive.
- ssrf-hunter — reachable-internal proof via OAST, metadata-endpoint
  checks where policy allows, redirect-to-internal chains; reachability
  only, no internal pivoting.
- file-upload — validation gaps, path traversal in names, content-type
  confusion, upload-to-execution chains, SVG/HTML polyglots.
- deserialization — fingerprint serialized blobs, safe detection probes
  first, gadget chains only where RoE allows.
- prototype-pollution — `__proto__`/constructor probes client and server
  side, gadget chains to XSS/SSRF; client artifact ≠ server truth.

## PHASE 6 — WEB TECHNOLOGY CLASSES

- graphql-mapper — introspection, field suggestions, batching, depth
  abuse, IDOR through nodes/edges, mutation authz parity with REST.
- websocket-hunter — origin validation, connect-time vs per-message
  auth, replay, cross-channel commands, subscription IDOR.
- jwt-analyst — alg confusion, weak HMAC secrets, kid injection,
  exp/audience validation, token replay, tampering verified owner-side.
- cors-csrf — origin reflection and whitelist bypasses, token-validation
  parity across channels; impact required — a header alone is not a
  finding.
- cache-deception — cache-key divergence via path tricks, non-destructive
  poisoned variants, fat GET.
- request-smuggling — DESTRUCTIVE-CLASS: user sign-off required before
  firing; passive detection first, CL-TE/TE-CL only with approval.
- host-header — Host/X-Forwarded-Host influence on reset links, cache
  poisoning, absolute-URL generation, redirects.

## PHASE 7 — MOBILE & SOURCE LANES

- source-auditor — clone public repos into the engagement's src-audit/;
  trace each security control to its hook; enumerate alternate execution
  paths; code-derived hypotheses stay HYPOTHESIZED until reproduced live.
- api-surface-mapper — swagger/openapi + JS + browserly endpoint
  diffing; test undocumented variants of documented operations.

## PHASE 8 — ESCALATION & OUTPUT

- chain-builder — on ANY confirmed weakness: escalation ladder tried
  end-to-end before write-up; chain record with ladder outcome.
- finding-handler — the moment a bug validates: revalidate twice, live
  policy check, findings file + evidence + index row in the SAME turn;
  P1/P2 triggers the hard-stop pause.
- evidence-manager — verbatim request/response with real tokens,
  screenshots and filtered Burp history into engagement evidence dirs.
- anomaly-registrar — one register entry per unexplained deviation;
  anomalies are leads — never discarded, never treated as findings.
- prior-art-scan — ≈3 bounded searches on root cause / tech / behaviour
  at write-up; novelty never claimed from absence of hits.

## PHASE 9 — OPS, DISCIPLINE & AUDIT

- captcha-handoff — two attempts maximum, then hand to the human; never
  re-script the same challenge.
- egress-manager — prompt-first VPN rotation on egress-dependent blocks;
  verify the new egress; never rotate to evade bans or suspensions.
- credential-vault — persist every created test account/secret to the
  engagement's notes/credentials.md in the SAME turn.
- compliance-auditor — rule-by-rule artifact pass plus fresh-eyes
  subagent audit at close; silence = NON-COMPLIANT.
- fresh-eyes-reviewer — zero-context subagent challenges every
  PARTIAL/BLOCKED/SKIP mid-engagement and at close.
- lab-builder — clean local lab for installable in-scope products before
  concluding anything from the marketing site; blockers become GAPs.
- rate-limit-discipline — ≤5 req/s bulk default, single-shot manual
  batteries, program-mandated limits override.
- personal-info-gate — prompt before any identity-writing action on a
  third-party system; neutral placeholders by default.
- inbox-duty — proactively poll the test-identity mailboxes for
  verification/invite/reset mail; never email third parties.
- report-preparer — workspace-internal opencode issue filing; bug-bounty
  submissions always stay human-only.
- opencode-ops — answer questions about opencode itself from its docs.

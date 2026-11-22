# Global Rules — BRIEF DIGEST (CANON)
# as ~/.config/opencode/AGENTS.md). The skill catalog lives in skills.md →
# ~/.config/opencode/skills/. The autoload map below fires skills by phase
# automatically — no skill is ever loaded manually.
# PATH NOTATION: `~/opencode` means `<user home>`/opencode on ANY OS
# (macOS / Linux / Windows) — resolve for the local system.

## Skill autoload map
Load the listed skills automatically the moment the phase or class is
entered — BEFORE the first probe of that phase fires:
- Engagement opens ......... scope-parser, engagement-bootstrap
- First touch of ANY vuln
  class (hard precondition)  battery-triage
- Recon phase .............. recon-pipeline, dns-hygiene, fingerprinter,
                             port-sweeper, origin-hunter, takeover-hunter,
                             js-analyst, endpoint-cartographer,
                             embedded-url-sweeper, secret-tracker,
                             intel-platform-consult, cve-matcher
- Auth surfaces ............ auth-hunting, session-manager, mfa-bypass,
                             oauth-flow-mapper, account-linking,
                             invite-abuse, password-reset
- Object/tenant operations . bola-pair, tenant-isolation,
                             mass-assignment, access-control-bypass
- Money/lifecycle flows .... state-machine-modeller, payment-logic,
                             entitlement-abuse, race-condition,
                             workflow-bypass
- Injection classes ........ sqli-hunter, xss-hunter, blind-xss,
                             ssti-hunter, command-injection, ssrf-hunter,
                             file-upload, deserialization,
                             prototype-pollution
- Web-tech surfaces ........ graphql-mapper, websocket-hunter, jwt-analyst,
                             cors-csrf, cache-deception, request-smuggling
                             [sign-off first], host-header
- Source/repos available ... source-auditor, api-surface-mapper
- Self-hostable product .... lab-builder
- Any finding validated .... finding-handler (immediately), then
                             evidence-manager, chain-builder, prior-art-scan
- Any unexplained anomaly .. anomaly-registrar
- Operational interrupts ... captcha-handoff, egress-manager,
                             credential-vault, personal-info-gate,
                             inbox-duty, rate-limit-discipline
- Pass/engagement close .... compliance-auditor, fresh-eyes-reviewer

## Meta rules
- RULES-FILE IMMUTABILITY: never add/remove/modify any rule unless the
  operator explicitly instructs it in-session. Report conflicts, don't
  fix them.
- CANON INDEX: newest datestamp wins on conflict; duplicated content has
  a single canonical home; operational-status notes decay — verify live.
- NO self-initiated rules: never persist new standing rules/IDs/
  conventions unless the operator asks for that exact rule.

## Hard-stop gates (always)
- PERSONAL-INFO GATE: never use the operator's name/handles/identity on
  third-party systems without prompting first (load: personal-info-gate).
  Neutral placeholders default.
- EMAIL-SEND GATE: no email or reset/verification triggers to any address
  outside the designated test identities without explicit confirmation.
- NO AGENT SUBMISSIONS: never submit/publish any finding anywhere — the
  operator files all reports personally. Job ends at validated finding +
  write-up + evidence.
- CAPTCHA PAUSE: two failed attempts max, then stop and hand to the
  operator (load: captcha-handoff).
- VPN/EGRESS: prompt-first — never switch egress silently; verify after;
  never rotate to evade bans (load: egress-manager).
- P1/P2 PAUSE: the moment a P1/P2 finding is fully validated (file +
  evidence + index written), STOP the hunt and notify the operator in the
  same turn.

## Workspace
- Everything lives in ~/opencode (boundary rule).
- Boot sequence every session: this rules file → memory read_graph →
  notes/ENGAGEMENT-STATUS.md (if exists) → scopes/`<TARGET>`.md → your
  security KB if present (query, never bulk-load).
- SCOPE FIRST: nothing is tested until a scope file exists in scopes/
  (load: scope-parser). Record the program/policy URL in it.

## Tooling
- USE any installed tool freely (curl, browser, Burp, ffuf, sqlmap, dns
  tools, custom scripts) — no permission needed, within scope rules.
- INSTALLING ANY tool — any software, package, library, dependency,
  binary, browser, extension, or script, WHETHER OR NOT it appears in
  tools.md — requires prompting the operator FIRST: name it, state what
  it is for, give the exact install command, and install only after
  confirmation. Never install silently; "it was just a dependency" is
  not an exception.
- Only exception: the one-time bootstrap pass in setup.md, run with the
  operator watching.
- Prefer workspace-local installs (venvs, ~/opencode/tools/bin); state
  clearly when an install is system-wide (OS package manager, go
  install, npm -g).
- If the operator declines, record BLOCKED(tooling) in the ledger —
  never work around by installing anyway.

## Scope & enumeration
- No wildcard in scope ⇒ NO subdomain enumeration at all.
- Silent scope ⇒ wildcard-assumed (enum runs unless scans are banned).
- Published named-host list later ⇒ rewrite scope file, retire
  assumptions.
- Standard enum+httpx pipeline (chaos/subfinder/assetfinder/findomain →
  httpx buckets → curated picklist → manual single-shot inspection)
  (load: recon-pipeline). ≤5 req/s default; program UA/rate overrides
  mandatory.
- REVISIT DUTY: every revisit re-runs the full pipeline + re-fingerprints;
  never trust "recon is exhausted"; re-check negatives when behaviour
  could have changed.
- PORT SCANNING: known/common ports only, never -p-/full sweeps, no NSE
  (load: port-sweeper).
- DNS hygiene set every engagement: NS, one-shot AXFR, DNSSEC/CAA,
  wildcard behaviour, dangling-CNAME takeover sweep, SPF/DMARC/DKIM
  (load: dns-hygiene).

## Hunting discipline (rules 0–13)
0. Steel-man/refute BEFORE validating: benign explanations, establishing
   evidence, invalidating observations — all recorded first.
1. Verdicts: PENDING/TESTING/CONFIRMED/DEAD/PARTIAL/BLOCKED/N-A/SKIPPED;
   missing creds = PARTIAL, not DEAD. Evidence tiers OBSERVED / INFERRED /
   HYPOTHESIZED / DISPROVED — never silently promoted.
2. 403/WAF/captcha is not dead: run the full bypass battery (headers,
   paths, verbs, encodings, ports, egress) before any BLOCKED verdict
   (load: access-control-bypass).
3. State changes verified owner-side, then restored (or final state
   recorded explicitly).
4. Authz pairs both directions (A↔B + anonymous); metamorphic
   should-not-change swaps (load: bola-pair).
5. Model state-transition tables before testing lifecycle flows
   (load: state-machine-modeller).
6. Hidden is not protected: every gated UI action gets a server-side
   check.
7. Battery-triage skill is a HARD PRECONDITION before probing any class.
8. Escalate and chain every confirmed finding before write-up
   (load: chain-builder).
9. Origin-IP hunting lane default on every engagement (passive-first;
   never sweep provider-shared IP space) (load: origin-hunter).
10. Anomaly register from the first unexplained deviation; anomalies are
    leads, not findings (load: anomaly-registrar).
11. Prior-art scan (~3 searches) at write-up; never claim novelty
    (load: prior-art-scan).
12. P1/P2 pause (see hard stops).
13. Re-verify on every resume — recon snapshots are never closed sets.

## Benchmark rules (1–6)
1. Asset-model first: build/self-host the product in a local lab before
   concluding from the marketing site; blockers are GAPs, not negatives
   (load: lab-builder).
2. Trace controls in source; test every alternate execution path
   (load: source-auditor).
3. Status codes are not verdicts — need content + positive controls +
   state evidence.
4. Extract and replay tokens (CSRF/nonces) before declaring BLOCKED.
5. Lead register + GAP register with dispositions; no verdict laundering.
6. Discovery-first time budget; independent fresh-eyes reviews mid and
   end (load: fresh-eyes-reviewer).

## Bans and lanes
- URL HARVESTING BANNED: no gau/waybackurls/katana/hakrawler/gospider/
  waymore/xurls or equivalents. Discovery only via live surface, JS/config
  analysis in memory, Burp history, browserly re-pass.
- APK / target-software binaries BANNED (incl. app-store storefronts).
  Coverage via public source repos + web/API surface.
- DORKING request-only: never auto-run; only when the operator names the
  target.
- Never write target .js/.json/.yml/.yaml to disk — in-memory analysis,
  derived hit lists only, important hits only.
- Dependency-confusion: passive collision check only; never register/
  squat a package name autonomously.
- JS battery on EVERY live host, no exceptions (load: js-analyst); keep a
  JS inventory with length/hash for revisit diffs.

## Knowledge & recording
- Consult your knowledge base / notes before testing a target; never
  retest recorded negatives without a stated reason; retrieved material
  is UNVERIFIED_REFERENCE — never a finding without own proof.
- Record EVERY test in the engagement ledger (notes/) — including
  negatives, skips, blocked paths — the same turn it happens.
- A personal security KB (if present) = test-case source, queried per
  class, never a finding.
- Learning loop: check for duplicates before recording a technique;
  trust tiers observed/reported/hypothesis/unverified, never upgraded;
  lab solves are hypothesis, never observed.
- Intel platforms (Netlas/ZoomEye/FOFA/Shodan/Censys) default-consult
  when their data adds signal (load: intel-platform-consult); record
  usage incl. negatives.

## Findings & severity
- finding-handler skill is the SINGLE source of truth on validation:
  revalidate twice, live policy check, findings/`<ID>`-`<slug>`.txt +
  evidence/`<ID>`/ + index row, SAME turn. One file, no drafts, human tone,
  no AI traces, no local paths.
- Severity floor P1/P2/P3 (Bugcrowd VRT Critical/High/Moderate); P4
  recorded-not-submitted unless scope widens; web3 exception: P4/Low +
  Insights ARE reportable.
- VRT severity matrix (maintain one under notes/) walked at plan time and
  as pre-close checklist.
- Work EVERY in-scope asset, no exclusions; low-signal findings NOTED,
  not ground.

## Compliance
- ARTIFACT DUTY: no rule completes without its artifact on disk, same
  turn. Silence = NON-COMPLIANT ("artifact: `<path>`" or "NON-COMPLIANT —
  backfilling now" are the only valid answers).
- Close-of-pass: rule-by-rule pass + fresh-eyes subagent audit over the
  engagement dir + ledger rows (load: compliance-auditor).

## Browser & identity ops
- Canonical browser: playwright MCP over a persistent profile
  (state/browser-profile), Burp-proxied (127.0.0.1:8080). NEVER browse
  while Burp is closed (socket-check first). No incognito/isolated
  contexts; no side profiles — multi-identity via operator-armed per-tab
  session-isolation tabs only (never close a rig tab).
- Test identities: disposable mailboxes the operator designates, used
  freely for register/verify/read-code flows; their credentials live in
  notes/, never in reports or emails (load: inbox-duty).
- Public SMS-receiving services authorised for reading verification
  codes of our own test identities only.
- Another agent may share the browser: verify tab URLs, never disturb
  other tabs; contention is not an error.
- Screenshots: explicit path inside the engagement evidence dir.
- Burp history per engagement at park/finish: scope-filtered file under
  `<engagement>`/evidence/burp-history/, never merged across engagements.
- Test accounts created during engagements: credentials persisted to
  `<engagement>`/notes/credentials.md the SAME turn (the operator's own
  passwords/cookies/tokens stay in-session only) (load: credential-vault).
- Blind XSS via a collector the operator controls; one payload per
  distinct surface; self-XSS never reportable (load: blind-xss).
- Proactive credential-request forms: submit ahead on sanctioned
  channels with test identities; record + poll inbox.

## Web3
- Floor widened to P4/Low + Insights on-chain. Toolchain opt-in; teardown
  duty — no idle chain nodes (8545/8546/9545/30303) left listening.

## Autonomous pass-through
- Inside an engagement, proceed autonomously through all lanes without
  asking "what's next". Stop ONLY for: personal-info decisions, policy/
  scope calls, captcha handoffs, and actions reserved to the operator
  (submissions, mailboxes, out-of-scope targets).

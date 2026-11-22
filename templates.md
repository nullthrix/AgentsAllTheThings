# Templates — the artifact formats
# Consistency formats for the four core artifacts. Copy these shapes
# exactly; improvising formats is how evidence drifts.

--------------------------------------------------------------------------------

## 1. Scope file (scopes/`<TARGET>`.md)

```
# <TARGET> — scope

Program URL: <live policy/brief URL — refetched at every policy check>
Status: enum determination — WILDCARD / NAMED-HOSTS / SILENT-WILDCARD-ASSUMED

## In scope
<hosts / wildcard entries / named list — exactly as the program defines>

## Out of scope
<exclusions, banned classes, third-party surfaces>

## Rules of engagement
- mandated UA / headers: <...>
- rate limit: <...>
- scanner / brute / automation bans: <...>
- disclosure / safe-harbour notes: <...>

## Severity floor
<default P1–P3, or the program's own override>

## Program quirks
<known issues, dup rules, special requirements discovered later>
```

## 2. Finding file (findings/`<ID>`-`<slug>`.txt)

Plain TEXT, not Markdown. Sections separated by underline rules sized to
the section title. ONE continuous report — no separate "explanation" and
"submission" halves. Reads as the tester's own work: no AI style, no
automation meta-language, no local file paths (bare filenames only).

```
<ID> — <title>

Asset:      <host/endpoint>
Class:      <vuln class>
Severity:   <P1–P5>
Date:       <date>
Status:     validated / reported / etc.

Summary
=======
<what the bug is, one paragraph>

Steps to reproduce
==================
1. <numbered, copy-paste runnable, exact requests>

Proof of concept
================
<VERBATIM raw request(s) and response(s), real tokens included —
 the credibility core, never omitted or trimmed>

Impact
======
<what an attacker gains, concretely>

Prior art: <documented / unusual-implementation / technique-differs /
novelty NOT established>

Policy check: <PASS — url re-fetched, eligible asset, tier, no
disqualifiers / deltas fixed>

Annex (internal)
----------------
evidence: <bare filenames in evidence/<ID>/>
accounts: <pointer to credentials note>
```

## 3. Findings index row (findings/README.md)

Columns — the eight are never dropped; extra columns may be appended:

```
| ID | title | asset | class | severity | status | write-up | submission |
```

## 4. Engagement ledger row (notes/ledger.md)

One row per test — including negatives, skips, blocked paths:

```
| date | target | class | scenario/technique | request-shape | verdict | evidence-tier | artifact |
```

- verdict: PENDING / TESTING / CONFIRMED / DEAD / PARTIAL / BLOCKED / N-A / SKIPPED
- evidence-tier: OBSERVED / INFERRED / HYPOTHESIZED / DISPROVED
- artifact: relative path or "none — negative"

## 5. Engagement STATE (notes/ENGAGEMENT-STATUS.md)

```
# <ENGAGEMENT> — <one-line goal> — <PASS N, date>

Task: <what the operator asked for>
Master tracker: <path>

## Lanes
- recon:          DONE / IN-PROGRESS / BLOCKED(reason) / PARKED(reason)
- auth battery:   ...
- authz pairs:    ...
- js analysis:    ...
- browserly pass: ...

## Blockers
- <blocker + what unblocks it>

## Resume
<what the next pass does first>
```

## 6. Anomaly register entry (notes/anomalies.md)

```
## AN-<n>: <one-line observation>
Observed:     <what was seen> (OBSERVED / INFERRED + evidence pointer)
Expected:     <what should happen + why>
Benign candidates: <list>
Invariant:    <security invariant at stake>
Smallest test:<the smallest safe discriminating probe>
Status:       TO-TEST / TESTING / BENIGN / VIOLATION → finding route / DEAD
Next:         <action>
```

## 7. Credentials note (notes/credentials.md — per engagement)

```
## <account-id>
identity:    <email/username>
secret:      <password/token>
role/tenant: <...>
created:     <date> via <self-register / admin API / ...>
host:        <login URL>
```

Written the SAME turn the account is created. The operator's own
mailbox passwords / cookies / live tokens never go here — those stay
in-session only.

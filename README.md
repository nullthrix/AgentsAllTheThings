# AgentsAllTheThings

A complete bug-bounty automation starter kit for opencode. Fresh
machine, fresh opencode install — this folder is everything the agent
needs to become a full hunting rig. Cross-platform: macOS, Linux,
Windows (path notation `~/opencode` = `<user home>`/opencode on any OS).

## What's in the folder

| File | What it is |
|---|---|
| AGENTS.md | The auto-entry — opencode loads this automatically when opened inside the repo; it tells the agent what the kit is and how to trigger the bootstrap. |
| setup.md | The bootstrap PROMPT — paste it as the first message to a fresh opencode. Builds the workspace, installs canon + skills + tools, wires the 5 MCP servers, verifies everything. |
| globalRules.md | The operating rules (becomes ~/.config/opencode/AGENTS.md) — hard-stop gates, hunting discipline, bans, tooling install gate, skill autoload map. |
| skills.md | The skill NAME catalog — 60+ skills across 10 lifecycle phases, one line each. The bootstrap agent generates the full skill files (trigger/flow/artifact) from these names, in its own words. |
| tools.md | The tool inventory to install from (ffuf-only fuzzing, Burp-only proxy). |
| batteries.md | Per-class scenario batteries — the source list battery-triage enumerates from before probing any class. |
| js-keywords.md | The JS-analysis keyword battery (encoded values, IPs, sinks, secrets alternation, URLs). |
| payloads.md | Extended payload reference — XSS, SSTI per engine, CRLF, SSI, ESI, text injection, SSRF, CORS, blind-XSS patterns. |
| templates.md | Artifact formats — scope file, finding file, index row, ledger row, engagement STATE, anomaly register, credentials note. |
| vrt-matrix.md | Severity map (P1–P5) + web3 exception + pre-close checklist. |

## Quick start
Prereqs: opencode + node installed; Burp Suite gets installed by hand
(the agent will ask). Then just give this prompt to opencode:

```
Clone https://github.com/nullthrix/AgentsAllTheThings into
~/AgentsAllTheThings (git clone — if git is missing, ask me before
installing anything). Then bootstrap the hunting rig: read AGENTS.md
and setup.md inside the cloned folder, resolve <KIT_PATH> to that
repo root, and follow setup.md phase by phase to the end. Prompt me
for approval before installing ANY tool, and finish with the
BOOTSTRAP-REPORT.
```

## Models
Works on ANY model available in opencode — free or paid; nothing in the
kit depends on a specific provider or tier. On a zero budget, the free
**bigpickle** and **hy4** models are recommended — both run the full
kit at no cost.

## How the pieces interlock
- setup.md installs globalRules.md as the canon, then the agent GENERATES
  the full skills itself from skills.md's name catalog (trigger/flow/
  artifact per skill, authored in its own words).
- globalRules.md's autoload map fires skills by phase automatically.
- battery-triage (skill) pulls scenarios from batteries.md; js-analyst
  pulls keywords from js-keywords.md; payload-hungry skills reach for
  payloads.md; finding-handler writes per templates.md; severity calls
  go through vrt-matrix.md.
- The rig is deliberately bounded: hard-stop gates (no submissions, no
  personal-info, no captcha grinding, no harvesting, ports cap, prompt-
  first tool installs) travel inside globalRules.md.

## Philosophy carried in the kit
- Evidence over verdicts: status codes are not findings; content +
  owner-side state evidence are.
- Negatives are recorded, not wasted — the ledger stops rework.
- Skills fire before probes; batteries before conclusions.
- The human submits everything. The agent's job ends at a polished,
  evidence-backed write-up.

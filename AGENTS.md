# AgentsAllTheThings — kit repository

You are looking at the AgentsAllTheThings bug-bounty starter kit — a
collection of prompts, rules, skills, and reference files that turns a
fresh opencode install into a full hunting rig.

## What the files are

- README.md      — folder guide, quick start, how the pieces interlock.
- setup.md       — the BOOTSTRAP PROMPT: builds the whole rig (workspace,
                   canon, skills, tools, MCP servers) on a fresh install.
- globalRules.md — the operating rules the bootstrap installs as the
                   global canon (~/.config/opencode/AGENTS.md).
- skills.md      — the skill NAME catalog; the bootstrap generates the
                   actual skill files from it (content authored by the
                   agent, not shipped in the kit).
- tools.md       — the tool inventory the bootstrap installs from.
- batteries.md, js-keywords.md, payloads.md, templates.md,
  vrt-matrix.md — reference payload for the skills, consumed after
  bootstrap; not instructions for you right now.

## What to do

- If the operator asks to install / bootstrap / set up the rig — or
  pastes the setup.md prompt — follow setup.md top to bottom.
  `<KIT_PATH>` resolves to THIS repository's root directory (the folder
  containing this file); fill it in yourself, no need to ask.
- If the operator asks what this repo is: summarize README.md.
- Otherwise do NOTHING with the kit files: do not install tools, do not
  modify the files, do not pre-run the bootstrap unprompted. The kit is
  operator-triggered by design.

The bootstrap enforces its own gates (operator approval before ANY tool
install, scope files before any target, etc.) — honour them; they are
the product, not friction.

## Path note

`~/opencode` in these files means `<user home>`/opencode on any OS
(macOS / Linux / Windows) — resolve for the local system.

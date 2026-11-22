# Bug Bounty Tool Catalog — A to Z
# Tools for automated hunting, organised by phase. One-liners describe the
# role each tool plays. House restrictions noted inline where they apply
# (they travel with the workspace canon):
#   [PORTS]  known/common ports only — never -p-, never full sweeps
#   [BANNED] URL-harvesting/crawling tools — not used in this workflow
#   [APK]    mobile binary lanes — banned; source repos + web/API only
#   [SIGNOFF] destructive/desync classes — explicit user sign-off first

--------------------------------------------------------------------------------

## 1. Subdomain & Domain Recon
subfinder        — passive multi-source subdomain enum (ProjectDiscovery)
amass            — deep passive+active enum & graphing (heavy, use -passive)
assetfinder      — fast passive sources (tomnomnom)
findomain        — CT-log based, multi-registrar monitoring
chaos            — ProjectDiscovery community subdomain DB (PDCP_API_KEY)
dnsx             — fast DNS resolution/brute/reverse with wildcards detect
shuffledns       — massdns wrapper with wildcard handling
massdns          — high-speed DNS resolver (subdomain brute backing)
alterx / dmut    — permutation/alteration name generation
cero             — TLS-SAN scrape of edge IPs → hidden hostnames (origin lane)
crt.sh / certspotter / censys-cert-search — CT record lookups (web + API)
dnsdumpster, securitytrails, urlscan.io — hosted recon pivots
subzy            — subdomain takeover check against CNAME/service list
can-i-take-over-xyz (repo) — the takeover-claim reference table
hakip2host       — IP→hostname corroboration for origin validation
dnsrecon / fierce — zone walking, NS/AXFR checks (DNS hygiene set)
doggo / dig / dohq — interactive DNS + DoH clients (DNSSEC/CAA checks)

## 2. HTTP Probing & Fingerprinting
httpx            — live-host probe, status buckets, tech/title/server/IP
                   tables (the pipeline's workhorse; -rl 5 default pacing)
httprobe         — legacy scheme prober
whatweb / webanalyze — tech fingerprinting
wappalyzer (cli) — tech stack detection
nuclei           — template scanner (use where RoE allows scanning at all)
tlsx             — TLS cert/SAN/host extraction at scale
nikto / wapiti   — legacy web scanners (triage only)

## 3. Port Scanning [PORTS — every tool below obeys the cap]
nmap             — service/version detection, --top-ports 100, no NSE
naabu            — fast SYN scanner (always with a port cap)
rustscan         — fast open-port finder → pipes to nmap
masscan          — only inside the curated-port boundary, never /0 style

## 4. Content & Parameter Discovery
ffuf             — THE fuzzer (sole fuzzing tool in this kit): directories,
                   params, vhosts — all fuzzing lanes route through ffuf
arjun            — hidden GET/POST parameter discovery
Param Miner (Burp ext) — hidden params/headers via cache-key divergence
wordlists: SecLists, PayloadsAllTheThings, onelistallmicrobatches
                   (keep a local seclists/ mirror)

## 5. URL / Endpoint Collection
[BANNED in this workflow] gau, waybackurls, katana, hakrawler, gospider,
waymore, urlfinder, xurls — bulk URL harvesting from archives/crawls is
prohibited; endpoints come from the live surface, JS/config analysis,
Burp history, and the browserly re-pass instead.
linkfinder / secretfinder — extract endpoints+secrets from JS (in-memory
                   analysis only; no target .js to disk)
jsluice           — Nuclei-powered JS pipeline for URLs/secrets
relative-url-extractor — URL harvesting from a page/JS blob
custom JS-analysis scripts — build your own: keyword battery,
                   embedded-URL sweeper, JS inventory with length/hash diffs

## 6. Proxy & Intercept
Burp Suite       — THE proxy, sole intercepting tool in this kit; MCP-
                   integrated (shim + SSE bridge); Repeater holds state-
                   changing requests for human review; all browser traffic
                   flows through it (127.0.0.1:8080, strict Burp-required
                   browsing)
Burp extensions (staples): Turbo Intruder (races), Param Miner,
  Autorize/Authz (authz diffing),
  HTTP Request Smuggler [SIGNOFF], LoggerPlusPlus, JWT Editor,
  ActiveScan++, J2EEScan, Hunt (passive header checks)

## 7. Browser Automation
playwright       — canonical automation lane (MCP wrapper, persistent
                   profile, Burp-proxied); MCP pinned @0.0.83
puppeteer        — alternative CDP driver
selenium         — legacy automation fallback
Browserpack: SessionHub extension (user-armed multi-account session tabs),
  Browsec VPN extension (egress lane, prompt-first per canon)
headless Chrome / Chrome for Testing — playwright browser binaries

## 8. Injection & Exploitation
sqlmap           — SQLi triage/extraction (where RoE allows)
ghauri           — modern SQLi automation alternative
ysoserial / phpggc / ysoserial.net — deserialization payload generators
[destructive-class — sign-off first]
ppmap / prototype-pollution scanners — client/server PP gadget hunting
xxexploiter      — XXE payload generation
jwt_tool         — JWT analysis: alg confusion, weak secrets, claim abuse
jwt-cracker      — HS256 secret brute
hashcat / john   — offline secret cracking (context-permitting)

## 9. SSRF / OAST
interactsh-client — OAST callback client (pre-approved standing grant)

## 10. Race & Logic Bursts
Turbo Intruder (Burp ext) — single-shot race windows, HTTP/2 races
race-the-web     — classic race pattern runner
httpx/race alternatives: custom parallel fetch scripts (workspace lane:
                   token-harvest → CLI parallel battery)

## 11. GraphQL / API
graphw00f        — GraphQL engine fingerprint
inql (Burp ext)  — introspection & query editing
graphql-cop      — GraphQL security scanner
graphinder       — GraphQL endpoint discovery
awesome-graphql-security (repo) — cheat sheets & wordlists
swagger/openapi tooling — schema → endpoint inventory (docs inspection
                   pass on every discovered host)

## 12. WebSockets
websocat         — CLI ws client (scriptable)
wscat            — quick ws interaction
wsrecon / ws-harness — ws message capture/replay patterns
(browser + Burp capture as the primary lane)

## 13. Secret & Source Intelligence
trufflehog       — secret scanning across repos/ history
gitleaks         — fast repo secret scanning
noseyparker      — high-performance secret scanner
git-dumper / gittools — exposed .git recovery (in-scope hosts only)
gitGraber / gh-mass-audit patterns — repo monitoring
[dorking lane: request-only per canon — fires ONLY on explicit operator
 request; use a dorking engine of your own or manual queries]

## 14. Cloud & Infrastructure
cloud_enum       — cloud asset enumeration (S3/Azure/GCP)
S3Scanner        — bucket enumeration
trufflehog (cloud mode) — cloud key validation
pacu             — AWS post-exploitation framework (permitted-systems only)
Netlas / ZoomEye / FOFA / Shodan / Censys — intel platforms (default-consult
                   per canon; keys & rotation policy in tool-accounts notes)
nrich            — Shodan internetdb enrichment for IP lists
asn tools (bgp.he.net, asnmap) — IP/ASN pivoting for origin hunting
Wayback/CDX archaeology (passive origin lane; archive READS for host
                   history are origin-lane inputs, URL harvesting is not)

## 15. Mobile & Source Review [APK — binary lanes banned]
jadx / apktool / mobexposer — ONLY if user lifts the APK ban by explicit
                   instruction; default coverage is source repos + the
                   app's web/API surface instead
git + src-audit lane — public-repo clones into `<eng>`/src-audit/ (permitted)
semgrep / codeql — source-review automation over cloned repos
frida / objection — dynamic instrumentation (same APK gate)

## 16. Recon Automation Frameworks
osmedeus         — recon pipeline automation
reNgine / axiom  — distributed recon fleets (self-hosted)
nanogeno / bbrf  — bug-bounty recon data coordination
cron-of-ffuf / sublister styles — monitoring loops
(use with program RoE in mind: many programs ban scanners outright)

## 17. Utility & Plumbing
anew             — append-only dedup (pipeline merge step)
qsreplace        — query-string value swap for payload replays
unfurl           — URL decomposition/analysis
jq / yq / python3 / node — parsing & one-shot scripts
curl / wget      — direct probes (outside-Burp when proxy flakes)
openssl / openssl s_client — TLS banner/cert probes on found ports
gau-style CDP tooling (cero, tlsx) — covered above
httptoolkit / reqbin — quick request crafting (manual aids)
hydra / medusa   — [NOT in scope: brute-forcing is against house rules
                   and most programs' RoE — listed for completeness only]

## 18. Reporting & Evidence
evidence kit — per-engagement Burp-history export + scope filtering
                   (script your own filter), screenshot discipline
                   (engagement evidence dirs only)
markdown/plain-text finding files (house format .txt, one-report shape)
screen-recording: obs/quicktime (user-side, for special cases)

## 19. Web3 / On-Chain (separate workspace ~/opencode/web3)
foundry (forge/cast/anvil) — contract deploy, fork RPC, fuzzing
slither          — static analyzer
mythril          — symbolic execution
echidna          — property-based fuzzing
solc-select / vyper — compiler toolchains
web3.py / ethers.js — chain interaction
sourcify / etherscan verification pullers
teardown duty: no idle chain nodes on 8545/8546/9545/30303

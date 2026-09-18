## Header

- Question: Which OpenAI network destinations are currently documented, which APIs have explicit retirement notices, and is Surge independent of the local standalone Tailscale app?
- Scope: Current official OpenAI network guidance and retirement notices; installed macOS Tailscale app, Surge runtime, and SSH route. No authenticated OpenAI requests or browser traffic capture.
- Retrieval period: 2026-09-07.
- Sufficient evidence: Direct official page retrieval, comparison with the configured rule source, local runtime observations, independent review of the uninstall boundary, and post-uninstall connectivity verification.
- Stop reason: Documentation and local uninstall verification completed; routing design remains pending approval.
- Completeness: Complete for the bounded documentation and local runtime questions; not a claim of exhaustive application traffic coverage.

## Search Surface

- SS-1: OpenAI Help Center article 9247338, direct fetch and web research. Found current domains, WebSocket destinations, voice transport and published address file.
- SS-2: OpenAI developer deprecations and Assistants migration guide; Sora discontinuation help article. Found explicit lifecycle notices and corroborating product pages.
- SS-3: Raw blackmatrix7 Surge OpenAI.list and active local Surge profile. Found configured rule source and destination; compared with current official network guidance.
- SS-4: Surge official Tailscale manual and Tailscale official uninstall instructions. Found implementation boundaries and supported uninstall procedure.
- SS-5: Local scutil, systemextensionsctl, process lookup, CLI lookup, Homebrew cask listing, Surge runtime, SSH effective configuration and fresh SSH connection. Found independent app installation, disconnected standalone VPN and working Surge tailnet.
- SS-6: Independent reviewer independently retrieved Surge/Tailscale documentation and inspected local state. Confirmed the proposed app-only uninstall boundary. First reviewer deployment unavailable; alternate reviewer completed.
- SS-7: Authenticated application traffic and all local automation scripts. Skipped: no browser testing in this phase; an exhaustive dependency/traffic claim is outside this bounded check.

## Observed

### OBS-1: Current official network destinations

Locator: https://help.openai.com/en/articles/9247338-network-recommendations-for-chatgpt-errors-on-web

Accessed 2026-09-07. Exact excerpts from the current allowlist:

```text
*.auth.openai.com
*.chatgpt.com
*.ct.sendgrid.net
*.intercom.io
*.intercomcdn.com
*.oaistatic.com
*.oaiusercontent.com
*.openai.com
*.oaistatsig.com
cdn.openaimerge.com
cdn.workos.com
challenges.cloudflare.com
forwarder.workos.com
humb.apple.com
images.workoscdn.com
js.stripe.com
o207216.ingest.sentry.io
o33249.ingest.sentry.io
rum.browser-intake-datadoghq.com
setup.workos.com
workos.imgix.net
```

The complete list additionally names individual hosts already covered by the above suffixes: android.chat.openai.com, auth0.openai.com, chat.openai.com, desktop.chat.openai.com, ios.chat.openai.com, js.intercomcdn.com, setup.auth.openai.com, tcr9i.chat.openai.com. This is a network allowlist, not a list of domains exclusive to OpenAI.

### OBS-2: Streaming and voice

Same official article, accessed 2026-09-07:

```text
wss://ws.chatgpt.com
wss://chatgpt.com/
ChatGPT Voice connects to OpenAI servers over UDP port 3478.
If UDP access is not allowed, TCP port 443 can be used instead, although UDP is preferred.
```

https://openai.com/chatgpt-voice.json was directly fetched. Its creationTime is 2026-03-26T20:12:45.451356+00:00 and it currently lists these 23 IPv4 /32 prefixes:

```text
102.37.57.54/32
13.71.25.29/32
135.220.40.201/32
172.203.39.49/32
172.207.173.200/32
172.214.226.198/32
191.233.251.27/32
20.162.96.163/32
20.168.48.117/32
20.184.36.134/32
20.203.144.245/32
20.74.221.21/32
4.151.200.38/32
4.155.146.196/32
4.197.172.116/32
4.217.235.100/32
4.245.198.13/32
40.118.236.137/32
51.4.112.173/32
52.143.181.161/32
68.155.152.41/32
72.146.20.246/32
74.248.148.7/32
```

### OBS-3: Explicit API lifecycle notices

Locator: https://developers.openai.com/api/docs/deprecations (directly fetched 2026-09-07).

Exact excerpts:

```text
The Realtime API Beta was deprecated and removed from the API on May 12, 2026.
Nov 30, 2026 | The `v1/prompts` API and reusable prompt objects are scheduled to shut down.
Nov 30, 2026 | The Evals dashboard and API are scheduled to shut down.
Nov 30, 2026 | Agent Builder is scheduled to shut down.
2026-09-24 | Videos API
2024-01-04 | `/v1/fine-tunes` | `/v1/fine_tuning/jobs`
2024-01-04 | `/v1/edits` | `/v1/chat/completions`
```

The page distinguishes deprecated (announced retirement), shut down (no longer accessible), and legacy (no longer updated). Model retirement is distinct from retirement of the shared API endpoint or domain.

### OBS-4: Independent product-specific corroboration

Locator: https://developers.openai.com/api/docs/assistants/migration (accessed 2026-09-07).

```text
The Assistants API was officially sunset on August 26, 2026, and is no longer available.
```

Locator: https://help.openai.com/en/articles/20001152-what-to-know-about-the-sora-discontinuation (accessed 2026-09-07).

```text
The Sora web and app experiences were discontinued on April 26, 2026.
The Sora API will be discontinued on September 24, 2026.
```

The Sora article still documents content export at https://sora.chatgpt.com/sunset. Product shutdown therefore does not imply that every related web destination is dispensable.

### OBS-5: Existing rule source

Locator: https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/OpenAI/OpenAI.list (accessed 2026-09-07).

```text
# UPDATED: 2025-06-06 09:20:00
# TOTAL: 35
DOMAIN,browser-intake-datadoghq.com
DOMAIN-SUFFIX,api.statsig.com
DOMAIN-SUFFIX,auth0.com
DOMAIN-SUFFIX,chatgpt.livekit.cloud
DOMAIN-SUFFIX,sentry.io
DOMAIN-SUFFIX,stripe.com
DOMAIN-KEYWORD,openai
IP-CIDR,24.199.123.28/32,no-resolve
IP-CIDR,64.23.132.171/32,no-resolve
IP-ASN,20473,no-resolve
```

Active profile: ~/Library/Application Support/Surge/Profiles/s 自定义规则 (RioLU节点).conf, line 176 sends this rule set to the United States group; line 76 declares the built-in tailscale policy with section-name=EB731CC8. No profile edits were made in this research phase.

### OBS-6: Surge implementation boundary

Locator: https://manual.nssurge.com/policies/tailscale.html (directly fetched and independently reviewed).

```text
all without installing a separate system VPN.
The policy handles outbound traffic selected by Surge. It does not advertise this device as a subnet router or exit node, and does not expose inbound services to the tailnet.
This document describes Surge's compatible policy implementation and its behavior; it does not describe every feature of the standalone Tailscale client or control service.
```

The manual describes Surge registering its own node, maintaining identity in application-support storage and implementing WireGuard/DERP and MagicDNS. It documents exit-node selection as application-level routing, not modification of the system global default route.

### OBS-7: Local pre-uninstall state

Locators: local commands scutil --nc list, systemextensionsctl list, pgrep, command -v, brew list --cask, ssh -G, surge-cli proxy-runtime-status tailscale.

```text
Tailscale: Disconnected
Surge: Connected
io.tailscale.ipn.macsys.network-extension (1.90.8/101.90.8)
com.nssurge.surge-mac.ne (5.0/40)
```

/Applications/Tailscale.app existed. Process matching returned an SSH command whose alias contains tailscale, not a standalone app/daemon executable. CLI lookup produced no tailscale/tailscaled path. Installed Homebrew casks did not list Tailscale. SSH effective configuration used the remote MagicDNS hostname and regular SSH without ProxyCommand.

### OBS-8: Supported uninstall and verified result

Locator: https://tailscale.com/docs/features/client/uninstall.md, accessed 2026-09-07.

```text
To uninstall the Standalone variant of Tailscale, drag its icon to the Trash and confirm that macOS also removes the system extension.
```

User-authorized local action: Finder moved only /Applications/Tailscale.app to the user's Trash. No state/keychain wipe, remote uninstall, tailnet node deletion or Surge profile modification.

Post-action evidence:

```text
/Applications/Tailscale.app: File not found
Tailscale Network Extension: [terminated waiting to uninstall on reboot]
Surge Network Extension: [activated enabled]
Surge state: ready
Surge selfIPv4: 100.69.205.4
Oracle peer: direct, handshakeCompleted true, latencyMs 109
exitNode: disabled, selector none
Fresh SSH hostname result: instance-20250526-0820
```

The post-action scutil VPN list includes connected Surge and no Tailscale service. No reboot was initiated.

## Inferred

- INF-1 (OBS-1, OBS-2, OBS-5): The configured 2025 list has coverage gaps relative to today's official list, notably oaistatsig.com, cdn.openaimerge.com, WorkOS hosts and the current voice prefixes. Exact browser-intake-datadoghq.com does not match rum.browser-intake-datadoghq.com. Domain-only routing does not necessarily capture IP-addressed voice traffic.
- INF-2 (OBS-1, OBS-5): Routing entire stripe.com, sentry.io, auth0.com or an ASN through the OpenAI policy also matches unrelated destinations. Conversely, even specific shared endpoints such as challenges.cloudflare.com and js.stripe.com cannot be attributed exclusively to OpenAI by destination alone.
- INF-3 (OBS-3, OBS-4): Explicit endpoint retirement does not justify deleting the api.openai.com domain route; current and retired API paths share that domain. Sora export remains separately documented.
- INF-4 (OBS-6, OBS-7, OBS-8): The observed Surge tailnet connection operates independently of the standalone macOS Tailscale installation. Uninstall verification demonstrates continued Surge and outbound SSH operation, not full feature equivalence with the standalone app.
- INF-5 (OBS-8): Application removal has completed and the standalone network extension has terminated; final system-extension removal is pending the next reboot.

## Contradictions

- C-1: The research subagent reported an older/different allowlist as an exact current quote, including statsig.com, featuregates.org, featureassets.org and prodregistryv2.org. Direct primary retrieval instead lists oaistatsig.com and does not contain those entries in its current allowlist. Discarded the subagent's report as an authoritative evidence record; this document uses independently retrieved primary content. No claim that those old domains are retired follows from the difference.
- C-2: Standalone independence versus feature equivalence: the Surge manual explicitly limits its implementation to outbound policy use. Independence does not mean it replaces inbound services, subnet-router or exit-node advertisement from this Mac.
- C-3: Uninstall command success versus full extension removal: post-action system state explicitly says waiting to uninstall on reboot. Full extension removal is not claimed.
- C-4: Sora discontinued versus every Sora destination obsolete: official documentation still lists a content-export endpoint. No blanket domain removal conclusion.

## Gaps

- G-1: No authenticated ChatGPT/Codex/voice session was exercised or captured. Documentation cannot prove exhaustive actual traffic coverage or Oracle IP acceptance by OpenAI.
- G-2: Old LiveKit, Arkose, Statsig and Azure entries have not individually been proven retired. Missing from the current article is not equivalent to a deprecation notice.
- G-3: Shared vendor destinations cannot be perfectly attributed to OpenAI with domain-only rules. A scope/fallback design decision remains pending.
- G-4: Standalone extension final removal requires reboot and a subsequent systemextensionsctl check. State/keychain retention was intentional; unrelated local automation was not exhaustively audited.
- G-5: The voice JSON is a point-in-time source; an eventual routing implementation needs an explicit update approach.

## Sources

- S-1: https://help.openai.com/en/articles/9247338-network-recommendations-for-chatgpt-errors-on-web (2026-09-07; primary; OBS-1/2, INF-1/2, C-1).
- S-2: https://openai.com/chatgpt-voice.json (2026-09-07; primary; OBS-2, INF-1).
- S-3: https://developers.openai.com/api/docs/deprecations (2026-09-07; primary; OBS-3, INF-3).
- S-4: https://developers.openai.com/api/docs/assistants/migration (2026-09-07; primary corroboration; OBS-4).
- S-5: https://help.openai.com/en/articles/20001152-what-to-know-about-the-sora-discontinuation (2026-09-07; primary corroboration; OBS-4, C-4).
- S-6: https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/OpenAI/OpenAI.list (2026-09-07; rule source; OBS-5, INF-1/2).
- S-7: https://manual.nssurge.com/policies/tailscale.html (2026-09-07; primary; OBS-6, INF-4, C-2).
- S-8: https://tailscale.com/docs/features/client/uninstall.md (2026-09-07; primary; OBS-8).
- S-9: Local profile and runtime/verification commands recorded above (2026-09-07; local primary; OBS-5/7/8, INF-4/5, C-3).

## Negative Claim Gate

- NC-1: Claim: Surge does not require standalone Tailscale.app for the observed outbound connection. Aliases inspected: tailscale, tailscaled, Tailscale.app, macsys network extension, Surge Tailscale policy. Mechanisms inspected: official implementation documentation, local extension separation, VPN status, processes, CLI lookup, SSH effective configuration and post-removal runtime. Independent review repeated documentation/local checks. Counterevidence sought: external-client requirement, connected standalone tunnel, SSH ProxyCommand dependency. Bounded conclusion: independence is supported for this configuration and tested outbound SSH, not every local workflow or feature.
- NC-2: Claim: the application and standalone VPN service were removed. Authoritative local surfaces: Finder result, direct application path read, scutil list. Independent local method: systemextensionsctl reports its extension terminated. Remaining limitation: system extension still awaits reboot; no complete residual-data wipe is claimed.
- NC-3: Claim: Assistants API retired. Authoritative surface: deprecation schedule; independent product surface: migration guide explicitly confirms sunset on 2026-08-26. Alias inspected: Assistants API, assistants, threads, runs. Counterevidence sought: current migration availability; guide labels historical calls as no longer working. Bounded claim: official service lifecycle, not a network probe.
- NC-4: Claim: older rule coverage differs from current official guidance. Exact comparison surfaces: OBS-1 and complete raw OBS-5 source; aliases include oaistatsig.com, cdn.openaimerge.com, WorkOS hosts, browser-intake-datadoghq.com/rum.browser-intake-datadoghq.com, voice JSON prefixes. The list is not treated as evidence of comprehensive usage or retirement. G-1/G-2 remain open.

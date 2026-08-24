# AI / agent adversary procedure pack — Group 1

An emerging-threat pack of **36 Tradecraft objects, 61 detection strategies**, one object per
procedure in *Group 1* of the AI/Agent Adversary Procedure Detection Catalog. Unlike the
`openai-huggingface-2026-07` pack, this is not one incident: it is a catalog of distinct AI- and
agent-directed behaviors — indirect prompt injection through retrieved content, MCP tool and server
supply-chain compromise, agent configuration and instruction poisoning, agent-driven exfiltration,
autonomous-agent control-plane and workload activity, model-artifact code execution, and novel
browser, multimodal, A2A, sampling, and sandbox trust boundaries. Each object carries the behavior,
its manifestation gates, field-level telemetry, materially distinct detection strategies, and the
gaps — drawn from public research and incident reporting plus first-party vendor telemetry
documentation. No atomic indicators appear.

## Validation state

**Nothing in this pack has been exercised in a lab.** Every object is untested: no strategy has been
fired against a reproduction of the behavior or a benign twin, so none carries a `validation` block.
The strategies are derived from the cited research and vendor telemetry documentation, not from
measured lab results. Treat every detectability verdict as an analysis of what the documented
telemetry can and cannot support, not as a tested claim.

Detectability across the 36 objects: **29 `yes-conditional`** (detectable where the required audit
tier, sensor, or cross-source join exists) and **7 `no`** (DT-AAG1-AI-003, -025, -026, -027, -030,
-032, -035). The seven negatives are the procedures whose deciding property is native to no
documented schema — poisoned tool-description text, an A2A turn's out-of-session quality, an image's
hidden instruction, a browser omnibox's input-origin trust, a server-initiated sampling request, a
localhost request's command semantics, and a browser-agent payment submission. Each still carries a
reformulated strategy per the format's negative-object rule, and states in `standing_negatives` what
was searched to establish the absence and when to recheck it.

## Scope

The pack models what AI-runtime telemetry (OpenTelemetry from coding and workspace assistants,
first-party audit for Copilot/Gemini/Bedrock/Vertex/Agent 365) and the surrounding
endpoint/network/identity/registry logs can record for each behavior. Where the deciding evidence is
an AI-runtime field that is documented but gated (content logging off by default, an audit tier that
is licence-bound, an export that must be wired to a collector), that is stated as a
`yes-conditional` blocking condition rather than assumed present. Where the deciding evidence exists
in no fetched first-party schema, that is a structural `no`, carried as a standing negative with an
expiry.

Sources are public: security-research disclosures and PoCs, real adversary-incident reporting, and
vendor telemetry documentation, with the telemetry detail taken from the Group 1 Telemetry Source
Audit. Several objects are responsibly-disclosed research or red-team studies rather than in-the-wild
incidents, and say so in `threat_research`; four are autonomous-agent-origin (`RI-A`) rows that
explicitly decline to model a human threat actor. `first_observed` is a coarse year where the source
is undated, with the imprecision recorded in `missing_context.unverified`. Reference URLs are carried
as `not-resolved-out-of-scope`: the pack relays the catalog's and audit's research without
re-fetching each source in this pass.

## What the object numbers mean

Objects are numbered `DT-AAG1-AI-001` through `-036` in catalog order; each object's `aliases` field
carries its source procedure code (`P08`, `P12`, …) so it can be traced back to the catalog. The
numbering carries no role convention. The pack is *Group 1* of a larger catalog; later groups are
out of scope here.

## Index

| ID | Source | Behavior | Primary surface | Detectable | Strategies |
|---|---|---|---|---|---|
| [DT-AAG1-AI-001](ai-01-plant-instructions-in-workspace-messages-retrieves.tradecraft.yaml) | P08 | Attackers plant instructions in workspace messages an agent retrieves | `submitted-content-ingest` | `yes-conditional` | 1 |
| [DT-AAG1-AI-002](ai-02-code-host-issues-disclose-another-repository.tradecraft.yaml) | P12 | Attackers use code-host issues to make an agent disclose another repository | `submitted-content-ingest` | `yes-conditional` | 2 |
| [DT-AAG1-AI-003](ai-03-poison-mcp-tool-description-schema-redirect.tradecraft.yaml) | P14 | Attackers poison an MCP tool description or schema to redirect an agent | `submitted-content-ingest` | `no` | 1 |
| [DT-AAG1-AI-004](ai-04-publish-lookalike-mcp-servers-install.tradecraft.yaml) | P15 | Attackers publish lookalike MCP servers that users or agents install | `submitted-content-ingest` | `yes-conditional` | 1 |
| [DT-AAG1-AI-005](ai-05-publish-backdoored-third-party-packages-extensions.tradecraft.yaml) | P16 | Attackers publish backdoored third-party agent packages, extensions, or skills | `submitted-content-ingest` | `yes-conditional` | 2 |
| [DT-AAG1-AI-006](ai-06-modify-configuration-session-start-hooks-approval.tradecraft.yaml) | P17 | Attackers modify agent configuration or session-start hooks after approval | `ai-agent-harness` | `yes-conditional` | 2 |
| [DT-AAG1-AI-007](ai-07-prompt-injection-rewrite-mcp-configuration.tradecraft.yaml) | P18 | Attackers use prompt injection to rewrite an agent MCP configuration | `ai-agent-harness` | `yes-conditional` | 2 |
| [DT-AAG1-AI-008](ai-08-submit-source-contributions-poison-coding-instruction.tradecraft.yaml) | P19 | Attackers submit source contributions that poison a coding agent instruction file | `submitted-content-ingest` | `yes-conditional` | 2 |
| [DT-AAG1-AI-009](ai-09-register-model-hallucinated-dependency-names-deliver.tradecraft.yaml) | P21 | Attackers register model-hallucinated dependency names to deliver malware | `other` | `yes-conditional` | 2 |
| [DT-AAG1-AI-010](ai-10-public-staging-dead-drop-relay-endpoints.tradecraft.yaml) | P23 | Autonomous agents use public staging, dead-drop, or relay endpoints | `ai-agent-harness` | `yes-conditional` | 2 |
| [DT-AAG1-AI-011](ai-11-emit-outbound-urls-carry-data.tradecraft.yaml) | P30 | Attackers make an agent emit outbound URLs that carry data | `application-ai-platform` | `yes-conditional` | 2 |
| [DT-AAG1-AI-012](ai-12-pass-local-secret-files-tool.tradecraft.yaml) | P31 | Attackers make an agent pass local secret files to a tool | `ai-agent-harness` | `yes-conditional` | 2 |
| [DT-AAG1-AI-013](ai-13-poison-persistent-assistant-memory-affect-later.tradecraft.yaml) | P32 | Attackers poison persistent assistant memory to affect later sessions | `application-ai-platform` | `yes-conditional` | 2 |
| [DT-AAG1-AI-014](ai-14-cause-invoke-tool-outside-user-request.tradecraft.yaml) | P34 | Attackers cause an agent to invoke a tool outside the user request | `ai-agent-harness` | `yes-conditional` | 2 |
| [DT-AAG1-AI-015](ai-15-shared-artifact-repository-coordinate-across-runs.tradecraft.yaml) | P36 | Autonomous agents use a shared artifact repository to coordinate across runs | `self-hosted-artifact-repository-manager` | `yes-conditional` | 2 |
| [DT-AAG1-AI-016](ai-16-stolen-cloud-credentials-consume-hosted-model.tradecraft.yaml) | P42 | Attackers use stolen cloud credentials to consume hosted model APIs | `application-ai-platform` | `yes-conditional` | 1 |
| [DT-AAG1-AI-017](ai-17-sustain-machine-rate-control-plane-activity.tradecraft.yaml) | P43 | Autonomous agents sustain machine-rate control-plane activity | `ai-agent-harness` | `yes-conditional` | 1 |
| [DT-AAG1-AI-018](ai-18-create-respawn-unauthorized-workloads.tradecraft.yaml) | P44 | Autonomous agents create and respawn unauthorized workloads | `container-orchestrator` | `yes-conditional` | 1 |
| [DT-AAG1-AI-019](ai-19-extract-ai-client-authentication-tokens-from.tradecraft.yaml) | P45 | Attackers extract AI-client authentication tokens from process memory | `other` | `yes-conditional` | 1 |
| [DT-AAG1-AI-020](ai-20-hijack-exposed-ai-orchestration-apis-run.tradecraft.yaml) | P49 | Attackers hijack exposed AI orchestration APIs to run jobs | `other` | `yes-conditional` | 1 |
| [DT-AAG1-AI-021](ai-21-model-artifact-code-execution-on-load.tradecraft.yaml) | P50 | Attackers upload model artifacts that execute code when loaded | `submitted-content-ingest` | `yes-conditional` | 2 |
| [DT-AAG1-AI-022](ai-22-exposed-ai-platform-tokens-change-supply.tradecraft.yaml) | P53 | Attackers use exposed AI-platform tokens to change supply-chain assets | `application-ai-platform` | `yes-conditional` | 2 |
| [DT-AAG1-AI-023](ai-23-steer-shell-tool-remote-payload-execution.tradecraft.yaml) | P58 | Attackers steer an agent shell tool into remote payload execution or safety-classifier bypass | `ai-agent-harness` | `yes-conditional` | 2 |
| [DT-AAG1-AI-024](ai-24-malware-invokes-local-ai-coding-cli.tradecraft.yaml) | P59 | Malware invokes a local AI coding CLI with permission-bypass flags | `ai-agent-harness` | `yes-conditional` | 1 |
| [DT-AAG1-AI-025](ai-25-remote-injects-unsolicited-turn-established-a2a.tradecraft.yaml) | P60 | A remote agent injects an unsolicited turn into an established A2A session | `other` | `no` | 1 |
| [DT-AAG1-AI-026](ai-26-hidden-instructions-reach-image-screenshot.tradecraft.yaml) | P61 | Hidden instructions reach an agent through an image or screenshot | `submitted-content-ingest` | `no` | 1 |
| [DT-AAG1-AI-027](ai-27-browser-url-omnibox-input-is-accepted.tradecraft.yaml) | P62 | Browser-agent URL or omnibox input is accepted as a trusted user prompt | `ai-agent-harness` | `no` | 2 |
| [DT-AAG1-AI-028](ai-28-code-interpreter-sandbox-sends-data-bearing.tradecraft.yaml) | P63 | A code-interpreter sandbox sends data-bearing DNS queries through its allowed resolver | `application-ai-platform` | `yes-conditional` | 2 |
| [DT-AAG1-AI-029](ai-29-follows-sandbox-child-created-symlink-write.tradecraft.yaml) | P64 | An agent follows a sandbox-child-created symlink to write outside the approved workspace | `host-filesystem` | `yes-conditional` | 2 |
| [DT-AAG1-AI-030](ai-30-malicious-mcp-server-sends-sampling-request.tradecraft.yaml) | P65 | A malicious MCP server sends a sampling request containing hidden instructions | `ai-agent-harness` | `no` | 2 |
| [DT-AAG1-AI-031](ai-31-attacker-dynamically-registers-rogue-oauth-client.tradecraft.yaml) | P66 | An attacker dynamically registers a rogue OAuth client with an MCP authorization service | `identity-provider` | `yes-conditional` | 2 |
| [DT-AAG1-AI-032](ai-32-browser-sends-command-bearing-request-unauthenticated.tradecraft.yaml) | P67 | A browser sends a command-bearing request to an unauthenticated localhost agent-tooling endpoint | `ai-agent-harness` | `no` | 2 |
| [DT-AAG1-AI-033](ai-33-commodity-stealer-reads-identity-configuration-files.tradecraft.yaml) | P68 | A commodity stealer reads agent identity and configuration files from disk | `host-filesystem` | `yes-conditional` | 2 |
| [DT-AAG1-AI-034](ai-34-connects-mcp-endpoint-outside-approved-inventory.tradecraft.yaml) | P69 | An agent connects to an MCP endpoint outside the approved inventory | `ai-agent-harness` | `yes-conditional` | 2 |
| [DT-AAG1-AI-035](ai-35-browser-submits-stored-payment-data-unverified.tradecraft.yaml) | P70 | A browser agent submits stored payment data to an unverified site | `other` | `no` | 2 |
| [DT-AAG1-AI-036](ai-36-coding-writes-obfuscated-network-endpoint-configuration.tradecraft.yaml) | P71 | A coding agent writes an obfuscated network endpoint into a configuration file | `ai-agent-harness` | `yes-conditional` | 2 |

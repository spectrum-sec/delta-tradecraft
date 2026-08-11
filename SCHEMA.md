# Delta Tradecraft: field reference

## Start here

A **Tradecraft object** is one YAML file describing one adversary behavior.
It sits above rule languages and alongside ATT&CK: it says what the adversary
does, whether that can be seen at all, what telemetry could carry the evidence,
what a detector may and may not conclude from it, and what is still unknown.
It does not contain a query, and it never contains an atomic indicator.

An object is written to survive being wrong in public. That is why roughly a
third of the format is dedicated to gaps: blocked conditions, blind spots,
assumptions, missing states, unsupported conclusions. It is also why an object
that names no gaps is treated as under-reviewed rather than clean.

**Authority.** The template and this field reference define the format together. The template owns
block order, field placement, and fill-in prompts. This reference owns definitions, types, field
presence rules, legal values, and cross-block rules. A disagreement is a format defect that must be
reconciled before publication. Neither document silently overrides the other.

**How to use this file.**

- *New to the format?* Read [The object in one screen](#the-object-in-one-screen)
  and [How the blocks relate](#how-the-blocks-relate), then skim any one block.
- *Looking up a single field?* Jump straight to its block below. Blocks are
  numbered in the order they appear in the file.
- *Looking for a legal value?* Every fixed list of legal values is listed under
  the block that owns it. See the [vocabulary index](#vocabulary-index).

### The object in one screen

Thirteen blocks, fixed order.

| # | Block | What it is for |
|---|---|---|
| 1 | [`identity`](#1-identity) | Names and dates it. `uuid` is the stable reference target; `id` is a human label. |
| 2 | [`classification`](#2-classification) | Routing: attack surface, behavior class, free tags. |
| 3 | [`behavior`](#3-behavior) | What the adversary does, with no product or log field in sight. |
| 4 | [`manifestation`](#4-manifestation) | Three gates: can it happen, can it be seen, can it be told from benign. |
| 5 | [`attack_surface_context`](#5-attack_surface_context) | How the attacked technology works *normally*, and where its trust boundaries are. |
| 6 | [`threat_research`](#6-threat_research) | Provenance: actor, campaign, victim, capability, framework mappings. |
| 7 | [`visibility`](#7-visibility) | Telemetry that could carry the evidence, field by field, with attestation. |
| 8 | [`detection`](#8-detection) | The payload: shared context plus *N* materially distinct strategies. |
| 9 | [`detector_requirements`](#9-detector_requirements) | What an implementation must already have before it can work. |
| 10 | [`missing_context`](#10-missing_context) | What sources do not verify, what was assumed anyway, dated absence claims. |
| 11 | [`missing_states`](#11-missing_states) | Named absences of evidence, and how a detector must degrade its claim. |
| 12 | [`alert_evidence_contract`](#12-alert_evidence_contract) | What an alert must carry, plus a title that overstates, as a counter-example. |
| 13 | [`evidence`](#13-evidence) | Sources: last, so the object is read before it is audited. |

### How the blocks relate

- **Block 4 gates block 8.** A `detectable: no` is a publishable result, and such
  an object still carries a strategy. The honest move is to reformulate the
  question, not delete the object.
- **Blocks 5 → 7 → 8 are a chain.** Block 5 says how the surface works, block 7
  says what it emits, block 8 says what may be concluded from that.
- **Blocks 10 and 11 are block 8's mandatory complement.** An object naming no
  gaps is under-reviewed.
- **Blocks 9, 12 and 13 wrap the payload.** Block 9 is the precondition list an
  implementing agent reads before writing a query, block 12 the contract its
  output must satisfy, block 13 what every attestation and absence claim
  resolves against.

**Standing prohibition.** There is no atomic-indicator or IOC field anywhere in
this format, and none may be added. The format sits above the indicator layer.

### Vocabulary index

| Vocabulary | Must you pick from the list? | Lives under |
|---|---|---|
| Attack surface (both surface fields) | Yes, or `other` with a note | [2. `classification`](#surface-vocabulary) |
| Behavior class | Yes, or `other` with a note | [2. `classification`](#behavior-class-vocabulary) |
| Blocking condition | Yes, or `other` with a note | [4. `manifestation`](#blocking-condition-vocabulary) |
| `event_vocabulary`, `tier`, `operational_viability`, `attestation` | See each | [7. `visibility`](#visibility-vocabularies) |
| `validation.status`, `expected_volume.band`, `expected_volume.basis` | See each | [8. `detection`](#detection-vocabularies) |
| `coverage_claim` | Yes, and there is no `other` | [11. `missing_states`](#coverage_claim-vocabulary) |
| `required_fields` starting set | No, free text to specialize | [12. `alert_evidence_contract`](#required_fields-starting-set) |
| `references[].type`, `trust_tier`, `verification`, `citation_caveat` | See each | [13. `evidence`](#evidence-vocabularies) |

Groupings inside those lists are presentational. They help a reader find a
member; they carry no meaning in the format and no member belongs to its group
in any machine-readable sense.

---

## Conventions

**Field presence markers.**

| Marker | Meaning |
|---|---|
| `R` | always present |
| `O` | omit the key entirely when it does not apply |
| `C` | required only under the stated condition |
| `R (entry)` / `R (block)` | required once its parent entry or block exists |
| `·` | the template sets no marker: see below |

**`null` placeholders.** Never write `null` as a placeholder; omit the key. Two
fields are deliberate exceptions where `null` is a legal *value*:
`blind_spots[].routes_to` and `expected_volume.band`.

**Types.** `prose` = folded block scalar (`>-`) unless line breaks are part of
the value · `token` = lowercase kebab-case, never a sentence · `string` = one
line · `enum` = pick one value from the list given below for that field. Use a
literal block scalar (`|` or `|-`) only for line-sensitive content such as
detection logic, code, queries or verbatim records. Each list says whether it
admits `other` as an escape hatch; several deliberately do not. If a token slot
wants a sentence, the value is `other` plus its note.

**YAML presentation.** Use two-space indentation and compact sequence mappings
such as `- source: '...'`. Use single quotes for ordinary scalar strings. Use
double quotes when a string contains an apostrophe or needs YAML escape
processing. Wrap ordinary prose at 100 columns. Indivisible URLs, identifiers
and line-sensitive literal blocks are exceptions.

**Notes are conditional.** Every `*_note` field exists for one reason: you chose
`other`. `other` without its note is invalid; the note without `other` is noise.

**Unmarked sub-fields (`·`).** The template marks field presence on most keys but
not on every key nested inside a list entry. Eighteen sub-fields have no marker
anywhere, and the parent list's marker is the only thing that decides whether
they exist at all:

| Parent | Unmarked sub-fields |
|---|---|
| `special_context` | `topic`, `context` |
| `technology_variants[]` | `variant`, `context` |
| `manifestation.detectable.blocking_conditions[]` | `condition`, `note` |
| `strategies[].detectors[]` | `detector_class`, `technologies`, `contribution`, `limitations` |
| `strategies[].forbidden_exclusions[]` | `field`, `reason` |
| `strategies[].blind_spots[]` | `description` |
| `baselines[]` | `name`, `window`, `seeded_from` |
| `conditional_fields[]` | `field`, `condition` |

Once the parent entry exists, these are its shape: treat them as required within
an entry.

**Ids.** `strategies[].id`, `missing_states[].id` (`SCREAMING_SNAKE`), and
`assumptions[].id` are stable tokens. `references[].id` is a `REF-…` token.
`identity.uuid` is a UUIDv4.

**"Wrong:" annotations.** "Wrong" records an observed authoring failure.

### The four cross-block referential rules

**1. `telemetry_sources[].fields[].attestation_ref` → an `evidence.references[].id`
in this object.**
Required by `vendor-attested`, `community-attested`, and `vendor-attested-absent`.
A field wearing an attestation badge with no resolvable ref is an unsourced claim.

**2. `strategies[].validation.evidence_ref` → an `evidence.references[].id`.**
Must resolve inside this repository. Omit the key when the evidence is not
published, and let `validation.evidence` describe what was done in full. A
pointer to an artifact a reader cannot retrieve is worse than no pointer: it
implies a reproducibility that does not exist. Prose here is wrong; it belongs in
`validation.evidence`.

**3. `standing_negatives[].ref` → an `evidence.references[].id`.**
Optional, but an absence claim with neither a ref nor a `recheck` is
unfalsifiable.

**4. `strategies[].blind_spots[].routes_to` → a sibling Tradecraft's
`identity.id`, or `null`.**
Routing to a sibling that does not cover the gap is worse than `null`, the common
case. This pointer deliberately uses the human `id`, not the `uuid`: a pointer a
reviewer cannot read is worse than a pointer that has to be maintained.
Renumbering an `id` therefore requires updating every `routes_to` that points at
it, in the same change. The old label moves to `aliases`, but no `routes_to` may
be left resolving through one. CI enforces that every non-null `routes_to`
resolves to an object in the corpus.

---

## 1. `identity`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.uuid` | R | uuid-v4 | The stable reference target for anything outside the object: relations, pivots, and every external reference point here. The one named in-corpus exception is `blind_spots[].routes_to`, which is spelled with the human `id` and is maintained on renumber.<br>**Wrong:** minting a new one on re-publication. That forks the object's history. |
| `.id` | R | string | Human-facing label, and the spelling of the one in-corpus pointer that is meant to be read: `blind_spots[].routes_to`. The owner may renumber it, and a renumber must update every `routes_to` pointing at it in the same change.<br>**Wrong:** using it as a reference target anywhere else. |
| `.aliases` | O | list\<string\> | Permanent, append-only: every label this object has been published under. A renumbered `id` moves here and a retired `name` moves here, and both resolve forever. Entries are not required to be identifier-shaped, so nothing pointing at an object may assume one is.<br>**Wrong:** pointing at an alias. No alias is a reference target. |
| `.name` | R | string | The *behavior*, named unambiguously.<br>**Wrong:** naming the detection ("Detect X"). The object outlives it. |
| `.incident` | R | string | The incident or campaign, inline.<br>**Wrong:** extracting it to a shared pack file. |
| `.first_observed` | R | string | `YYYY-MM` or a range, for the behavior.<br>**Wrong:** your source's publication date. |
| `.last_reviewed` | R | string | `YYYY-MM-DD` of last human review.<br>**Wrong:** post-dating. A review action may not post-date the review date. |

### The identifier, part by part

```
DT - OAIHF - K8S - 004
│    │       │     │
│    │       │     └── position in the pack; in this pack the number
│    │       │         also carries role (01-12 counted behaviors,
│    │       │         13-20 later, 21-25 negatives, 26 controls)
│    │       └──────── domain, fixed by the object's own
│    │                 primary_attack_surface
│    └──────────────── the pack; keeps ids from colliding across packs
└───────────────────── the format: Delta Tradecraft
```

**Domains in use (11).** `AI` · `APP` · `ART` · `CLOUD` · `DATA` · `ESTATE` ·
`HOST` · `IDP` · `K8S` · `SCM` · `SEC`

The role-carrying numbers are a convention of this reference pack, not a rule of
the format.

## 2. `classification`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.primary_attack_surface` | R | enum | The one surface where the adversary *introduces* the behavior.<br>**Wrong:** naming the eventual victim technology instead of the entry surface. |
| `.primary_attack_surface_note` | C | string | Required when the value is `other`. |
| `.secondary_attack_surfaces` | O | list\<enum\> | Further surfaces reached, same vocabulary.<br>**Wrong:** listing every technology the incident mentions. This is reachability, not a bibliography. |
| `.secondary_attack_surfaces_note` | C | string | Required when any entry is `other`.<br>**Wrong:** one note for several `other` entries, with no way to tell which is which. |
| `.behavior_classes` | R | list\<enum\> | One or more technology-independent classes.<br>**Wrong:** a compressed sentence. If it reads as prose, use `other` plus a note. |
| `.behavior_classes_note` | C | string | Required when any entry is `other`.<br>**Wrong:** omitting it and hoping the token explains itself. |
| `.tags` | O | list\<token\> | Free search tags, deliberately not an enum. Carry `ai` on AI-related objects. Mark negative results explicitly.<br>**Wrong:** re-listing surfaces and classes. |

### Surface vocabulary

One list serves *both* surface fields. Pick one member; if none fits, write
`other` and say why in the matching note. The split below records where each
member is attested in the corpus.

**Primary-attested (12), plus one use of `other`.** Members seen in
`primary_attack_surface`:

```
ai-agent-harness  application-ai-platform  application-runtime  cloud-control-plane
cloud-instance-metadata-service  container-orchestrator  data-plane
estate-preventive-control-configuration  host-os  identity-provider  secret-store
self-hosted-artifact-repository-manager
```

**Additional secondary-attested (26), plus the accepted alias below.** Grouped for lookup only:

| Group | Members |
|---|---|
| Cloud plane and instance credentials | `cloud-instance-metadata-reachability-configuration` · `cloud-management-event-record` · `cloud-secret-store` · `node-instance-role-credentials` |
| Container and orchestrator | `cluster-identity-mapping` · `container-orchestrator-admission-control` · `container-orchestrator-control-plane` · `container-registry` · `container-workload` |
| Identity, tokens and key material | `identity-provider-signing-material-custody` · `identity-provider-token-issuance` · `signing-key-material` · `token-verification` · `workload-identity` |
| Authorization boundaries | `authorization-boundary` · `authorization-decision-api` · `host-os-authorization-boundary` |
| Host, runtime and parsing | `data-plane-field-parser` · `host-filesystem` · `in-process-runtime` · `parser-worker` |
| Network path | `container-to-link-local-network-reachability` · `egress-control` · `name-resolution-path` |
| Content, artifact and secret stores | `consolidated-secret-store` · `submitted-content-ingest` |

Accepted alias: `cloud-instance-metadata` → `cloud-instance-metadata-service`.
Use `other` plus the note rather than coining a near-duplicate spelling.

### Behavior class vocabulary

Pick one of the 34 members; if none fits, write `other` and say why in
`behavior_classes_note`. Grouped for lookup only. Prefer a
technology-independent core member over a narrow one.

| Group | Members |
|---|---|
| Credential access | `credential-access-via-cloud-instance-metadata` · `credential-access-via-control-plane-api` · `credential-replay-from-outside-the-issuing-boundary` · `instance-role-credential-theft-via-metadata-service` · `workload-identity-token-minting` |
| Identity misuse | `borrowed-identity-capability-mapping` · `capability-redirection-under-one-identity` · `host-identity-impersonation-by-a-workload` · `identity-borrowing` · `presigned-identity-api-request-used-as-a-bearer-token` |
| Discovery and enumeration | `authoritative-self-permission-enumeration` · `control-state-verification` · `discovery` · `discovery-without-denials` · `empirical-permission-discovery` · `read-only-infrastructure-enumeration` |
| Denial-shaped evidence | `blocked-egress-then-local-re-aim` · `authorization-denial-accrual-on-one-principal` · `denial-record-as-primary-observable` · `inverted-success-to-denial-ratio` |
| Boundary crossing and induced execution | `cloud-metadata-access-attempt` · `containment-boundary-crossing` · `in-process-interpreter-execution` · `schema-type-violation` · `server-side-request-forgery` · `template-injection` |
| Movement, escalation, persistence | `lateral-movement-without-privilege-escalation` · `persistence` · `privilege-escalation` |
| Collection and exfiltration | `collection` · `exfiltration` |
| Defense evasion and impairment | `defense-impairment` · `estate-precondition-removal` · `stealth` |

**Identity-misuse boundaries.** The three identity members answer different
questions and one behavior may legitimately carry more than one.
`identity-borrowing` is about *whose* identity is acting: the adversary
authenticates as a principal that is not theirs. `borrowed-identity-capability-mapping`
is about *learning what that identity can do* once borrowed, so it applies only
where the object's behavior is the capability enumeration itself.
`capability-redirection-under-one-identity` involves no second identity at all:
the principal is unchanged and what moves is *which* of its capabilities the
adversary exercises, typically after one path is denied.

**Discovery boundaries.** The three narrow members are distinguished by how the
answer is obtained: `empirical-permission-discovery` learns permissions by
attempting actions and reading the allow/deny outcomes;
`authoritative-self-permission-enumeration` asks the authorization API to return
the principal's own permissions in one authoritative answer, generating no
denials; `read-only-infrastructure-enumeration` lists resources and inventory
rather than permissions. Do not carry the generic `discovery` alongside any of
them. `discovery` is for enumeration that no narrower member covers, and pairing
the two double-counts the object in any aggregation. (One corpus object, `hf-15`,
carries both; it is the exception, not the pattern.)

## 3. `behavior`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.definition` | R | prose | One or two plain sentences: what the adversary controls, the trusted action they cause, the objective. |
| `.detailed_definition` | R | prose | The same behavior independent of vendor, product, cloud, and detector: boundary crossed, possible outcomes, where attempt ends and success begins.<br>**Wrong:** naming a product or a log field. |

## 4. `manifestation`

Three independent gates on what block 8 may claim.

| Field | Req | Type | Definition |
|---|---|---|---|
| `.possible` | R | map | Can this behavior occur at all? |
| `.possible.answer` | R | enum | `yes` \| `no`.<br>**Wrong:** `yes-conditional`, not legal here. Scope a `yes` in `answer_scope`. |
| `.possible.answer_scope` | C | string | Required when the answer is not `yes`, recommended on a plain `yes`: one line on what the answer covers and what it does not.<br>**Wrong:** restating the answer instead of bounding it. |
| `.possible.summary` | R | prose | Everything that must be true: control, exposed feature, trusted path, permissions, network and identity conditions.<br>**Wrong:** preconditions no source supports. |
| `.observable` | R | map | Can it be seen in telemetry? |
| `.observable.answer` | R | enum | `yes` \| `yes-conditional` \| `no`.<br>**Wrong:** a plain `yes` when the record exists only if an audit tier is on. That is `yes-conditional`. |
| `.observable.answer_scope` | C | string | As above.<br>**Wrong:** omitting it on a `no`, leaving a bare negative nobody can bound or retire. |
| `.observable.blocking_conditions` | C | list | Required when the answer is not `yes`; omit on a plain `yes`. Always a list: one slot routinely carries several simultaneous gates.<br>**Wrong:** collapsing several gates into one entry. |
| `.observable.blocking_conditions[].condition` | R (entry) | enum | A blocking-condition member, or `other`.<br>**Wrong:** a collection-family condition on a `no`. See the family discriminator below. |
| `.observable.blocking_conditions[].note` | C/O | string | Required with `other`; else optional. What specifically must be true.<br>**Wrong:** restating the token in words. |
| `.observable.summary` | R | prose | What must be logged or measured, the attribution and correlation needed, and evidence too weak to establish the behavior.<br>**Wrong:** dropping the too-weak examples: the load-bearing half. |
| `.detectable` | R | map | Can it be separated from benign activity? |
| `.detectable.answer` | R | enum | Same vocabulary as `observable`.<br>**Wrong:** copying `observable`. A recorded behavior indistinguishable from a legitimate one is observable and *not* detectable. |
| `.detectable.answer_scope` | C | string | As above.<br>**Wrong:** a scope that quietly narrows to the one estate you tested. |
| `.detectable.blocking_conditions` | C | list | As above.<br>**Wrong:** omitting it on a `no`, which makes the negative unauditable. |
| `.detectable.blocking_conditions[].condition` | R (entry) | enum | As above. |
| `.detectable.blocking_conditions[].note` | C/O | string | As above. |
| `.detectable.summary` | R | prose | What separates the behavior from benign activity: normalization, enrichment, correlation, outcome boundaries, retention.<br>**Wrong:** a bare `no` with no statement of what would change it. |

### Blocking condition vocabulary

Pick one member; if none fits, write `other` and say why in `note`. Each of the
two families pairs with a different `answer`.

| Family | Pairs with | Members |
|---|---|---|
| Collection | `yes-conditional` | `audit-tier-or-enablement-required` · `stream-existence-varies-by-estate` · `environment-inventory-required` · `cross-source-join-required` · `below-alerting-signal-strength` |
| Structural | `no` | `deciding-property-not-in-any-schema` · `action-emits-no-record` · `only-unreliable-negative-evidence` · `indistinguishable-from-legitimate` |

**The discriminator between families:** if no audit tier changes the answer and
no enablement switch would produce the record, it is structural.

**A structural condition under a `yes-conditional`.** The pairing above is the
normal case. A structural condition may legitimately sit under a
`yes-conditional` when it gates only part of the scope, and four objects in the
reference pack do exactly that. Where it does, two things become mandatory: the
`note` must say which part is structurally lost, and `answer_scope` must bound
what survives. A structural condition carrying no such bound is a `no` that was
written as a `yes-conditional`.

## 5. `attack_surface_context`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.surface` | R | string | The technology or feature under attack, named concretely.<br>**Wrong:** repeating `primary_attack_surface`, a routing token. |
| `.how_the_surface_works` | R | prose | Normal architecture and workflow: inputs, services, queues, workers, APIs, storage, identities, paths, outputs.<br>**Wrong:** a product tutorial with nothing this behavior depends on. |
| `.incident_processing_path` | R | prose | How the documented incident moved through this technology, sentence by sentence attributed to the source or to this pack. The densest mixture of source fact and pack inference in the object, and so the field where the provenance rule is broken most often.<br>**Wrong:** blurring source-attested fact with inferred detail. Label both inline.<br>**Wrong:** quoting a paraphrase. Quote only the source's exact characters. A quoted paraphrase invents a source statement that does not exist, and no later reviewer can tell. |
| `.processing_stages` | R | list\<token\> | Ordered kebab-case stage tokens, adversary input through retrieval. Typically 6 to 13; the reference pack's median is 8. Do not pad to a target length.<br>**Wrong:** prose here, or an unordered set. The order is the content.<br>**Wrong:** a stage that changes nothing about what an event can prove. That is not a stage. |
| `.stage_evidence` | O | prose | Why the stage at which instrumentation sits decides what an event can and cannot prove.<br>**Wrong:** omitting it. Write it whenever the stage changes what an event proves, which is nearly always. |
| `.trust_boundaries` | R | prose | Each boundary the behavior attempts, and which were reached, blocked, or unverified.<br>**Wrong:** claiming a crossing because a *later* technique in the incident succeeded. |
| `.security_control_context` | R | prose | How preventive controls decide, what they block, what they log, and the bypass representations.<br>**Wrong:** pushing control mechanics into `technology_variants` or `special_context`. They belong here. |
| `.special_context` | O | map | At most one: the single extra body of mechanics needed beside control context.<br>**Wrong:** inventing an ad-hoc `<topic>_context` key instead. |
| `.special_context.topic` | · | token | Free topic token.<br>**Wrong:** a topic that is really a second control-context section. |
| `.special_context.context` | · | prose | The mechanics themselves. |
| `.technology_variants` | O | list | Per-variant mechanics.<br>**Wrong:** a provider-keyed map (`aws:`/`azure:`). It cannot express non-provider variants like `platform-independent` or `self-managed-kubeadm`. |
| `.technology_variants[].variant` | · | token | The variant. Prefer the long provider-product form (`amazon-eks`, not `eks`). |
| `.technology_variants[].context` | · | prose | Mechanics unique to it.<br>**Wrong:** an entry changing neither manifestation, telemetry, nor detection. Not a variant. |
| `.practitioner_implication` | R | prose | What an implementation agent must discover *in the target environment* before generating a detector.<br>**Wrong:** restating detection logic. This precedes it. |

## 6. `threat_research`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.why_it_matters` | R | prose | Risk, attacker value, likely consequence.<br>**Wrong:** severity adjectives with no mechanism behind them. |
| `.threat_actor` | R | string | Actor, tool, agent, or `unknown`: described, not labeled.<br>**Wrong:** inventing attribution. If the source names no group, say so. |
| `.actor_class` | O | token | Free token, deliberately not an enum.<br>**Wrong:** treating it as a curated taxonomy and forcing a fit. |
| `.campaign` | R | string | Campaign or incident context.<br>**Wrong:** duplicating `identity.incident` and adding nothing. |
| `.victim` | R | string | Documented victim or victim class.<br>**Wrong:** naming an organization the source does not. |
| `.capability` | R | string | Source-faithful description of the capability demonstrated, and faithful in both directions.<br>**Wrong:** promoting what the actor *could* have done into what was demonstrated.<br>**Wrong:** hedging ("may have", "appears to") something the source asserts plainly. An unearned hedge misreports the source exactly as much as an unearned claim, and it is the harder one to spot. |
| `.infrastructure` | O | list\<string\> | Attacker or victim infrastructure used in this procedure.<br>**Wrong:** IPs, domains, or hashes. No atomic indicators. |
| `.target` | R | string | What was targeted, with reachability and outcome caveats.<br>**Wrong:** stating a target as reached when only attempted. |
| `.victimology` | O | list\<string\> | Technology, organization, or deployment patterns exposed.<br>**Wrong:** repeating the one documented victim. This is the exposed *class*. |
| `.framework_mappings` | O | list | Zero or more.<br>**Wrong:** padding with loosely related techniques to look thorough. |
| `.framework_mappings[].framework` | R (entry) | string | The framework name, e.g. `MITRE ATT&CK`. |
| `.framework_mappings[].technique_mapping_status` | O | enum | `mapped` \| `no-technique-assigned`; defaults to `mapped` when `id` is present.<br>**Wrong:** leaving it implicit when you are deliberately refusing a mapping. |
| `.framework_mappings[].id` | C | string | Required when `mapped`. Omit when `no-technique-assigned`. A real `T…`, sub-technique, or `TA…`.<br>**Wrong:** a sentinel string such as `none`. |
| `.framework_mappings[].name` | C | string | Same condition as `id`. The framework's own name, verbatim.<br>**Wrong:** your paraphrase of the technique. |
| `.framework_mappings[].evidence` | R (entry) | prose | Why the documented behavior supports the mapping, and where a technique was targeted but unachieved. On `no-technique-assigned`, the rationale for refusing plus the framework version considered.<br>**Wrong:** none at all. An unjustified `T1078` is worse than no mapping. |

## 7. `visibility`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.telemetry_sources` | R | list | One or more streams that could carry the evidence. |
| `.telemetry_sources[].source` | R (entry) | string | The stream as the estate would recognize it.<br>**Wrong:** a SIEM index name or a product SKU. |
| `.telemetry_sources[].purpose` | R (entry) | string | What it contributes to observing or detecting *this* behavior.<br>**Wrong:** describing the stream in general. |
| `.telemetry_sources[].event_vocabulary` | O | enum | The normalized record family. `none` when the source is not a log stream at all: an inventory, ledger, or computed artifact.<br>**Wrong:** forcing a computed artifact into a log family. |
| `.telemetry_sources[].event_vocabulary_note` | C | string | Required with `other`. |
| `.telemetry_sources[].tier` | O | enum | How much of the interaction the record retains.<br>**Wrong:** `request-response` when only the request is logged. The outcome half is what a detector needs. |
| `.telemetry_sources[].operational_viability` | O | enum | Categorical judgment about whether the stream exists in a real estate.<br>**Wrong:** a numeric score, or silence. Silence gets read as `on-by-default`. |
| `.telemetry_sources[].operational_viability_note` | C | string | Required with `other`. |
| `.telemetry_sources[].fields` | O | list | The fields this object actually uses.<br>**Wrong:** dumping the vendor's whole schema. |
| `.telemetry_sources[].fields[].name` | R (entry) | string | The name as the vendor spells it.<br>**Wrong:** a normalized or CIM-style name the source does not emit. |
| `.telemetry_sources[].fields[].attestation` | R (entry) | enum | Where the field's existence is documented.<br>**Wrong:** reading it as a claim that an estate collects the field, or that the field is trustworthy. It is neither. |
| `.telemetry_sources[].fields[].attestation_ref` | C | string | Required with `vendor-attested`, `community-attested`, `vendor-attested-absent`. Reuse an `evidence.references[].id`.<br>**Wrong:** a bare URL. It must resolve inside this object. |
| `.telemetry_sources[].fields[].load_bearing` | O | bool | `true` when the field is a deciding conjunct of a strategy, `false` when it only enriches.<br>**Wrong:** encoding a trust caveat here. That goes in `note`. |
| `.telemetry_sources[].fields[].note` | C/O | string | Required when attestation is `other`; otherwise the slot for a trust caveat or absence attestation.<br>**Wrong:** burying the caveat in `details` where no field-level reader finds it. |
| `.telemetry_sources[].details` | R (entry) | prose | The useful events and context, in prose.<br>**Wrong:** restating what `event_vocabulary`, `tier`, or `fields[]` already carry. |
| `.telemetry_sources[].advantages` | O | list\<string\> | Operational or evidentiary advantages. |
| `.telemetry_sources[].limitations` | R (entry) | list\<string\> | Blind spots, availability, attribution limits, cost.<br>**Wrong:** omitting it. A source with no stated limitation was not examined. |

### Visibility vocabularies

#### `event_vocabulary`

Pick one member; if none fits, write `other` and say why in
`event_vocabulary_note`.

| Group | Members |
|---|---|
| Control-plane and cloud | `kube-audit` · `cloud-management-event` · `cloud-data-event` |
| Host | `kernel-audit` · `host-process-event` |
| Network | `flow-record` · `dns-resolver-record` |
| Application | `application-record` |
| Not a log stream | `none` |

#### `tier`

Pick one member. There is no `other`: host and kernel vocabularies map onto this
one ladder deliberately.

| Member | Retains |
|---|---|
| `metadata` | the fact of the interaction only |
| `request` | the request, not the outcome |
| `request-response` | both halves |
| `no-tier-model` | the source is an event stream, but its schema has no verbosity ladder |
| `not-applicable` | the source is not an event stream at all |
| `not-implemented` | the tier exists in the model but is not emitted |

Use `not-applicable` for a non-event artifact such as an inventory, ledger,
configuration, or computed baseline. It pairs with `event_vocabulary: none`.
Use `no-tier-model` for a real event stream whose vendor schema publishes no
verbosity levels to choose between, such as a flow record or cloud management event.

#### `operational_viability`

Pick one of `opt-in` · `on-by-default` · `first-party-build` ·
`rejected-not-coverage`; if none fits, write `other` and say why in
`operational_viability_note`.

The last two differ on whether the source already exists. Use `first-party-build`
when no vendor ships it and the estate must construct it before a strategy can
use it, such as an inventory, ledger, join table, or measured baseline. Use
`rejected-not-coverage` when the stream exists or could be enabled but does not
carry the behavior's deciding evidence. Both remain listed so readers can see
what was considered.

#### `attestation`

Pick one member; if none fits, write `other` and say why in `note`.

| Group | Members |
|---|---|
| Externally attested | `vendor-attested` · `community-attested` |
| Attested absent | `vendor-attested-absent` |
| Not attested | `unattested-inferred` |
| Supplied or derived locally | `estate-supplied` · `detector-computed` |

The boundary between `unattested-inferred` and `detector-computed` is whether the
value exists in the source. Use `unattested-inferred` for a field the source is
believed to emit when no citation attests it. Use `detector-computed` for a value
that does not exist until the detector derives it after collection, such as a
count, ratio, gap, or statistic over a window.

## 8. `detection`

Strategies must be *materially distinct*: a different observable path or stage,
not the same logic re-tuned.

| Field | Req | Type | Definition |
|---|---|---|---|
| `.scope` | R | prose | The complete observable path and how strategies divide coverage across input, processing, runtime action, consequence, correlation.<br>**Wrong:** on a negative object, asserting a coverage path. State the reformulation. |
| `.outcome_guardrails` | R | prose | What each evidence stage can and cannot establish, keeping attempted / blocked / connected / responded / accessed / follow-on-use distinct.<br>**Wrong:** collapsing blocked and successful into "detected". |
| `.shared_normalization_context` | O | prose | Canonicalization, decoding, target classification, temporal normalization shared across strategies.<br>**Wrong:** repeating it inside every strategy. |
| `.shared_enrichment_context` | O | prose | What to join when available, and whether a baseline is useful or required.<br>**Wrong:** listing joins no strategy consumes. |
| `.strategies` | R | list | One or more.<br>**Wrong:** deleting the block when `detectable` is `no`. A negative still carries a strategy answering a reformulated question. |
| `.strategies[].id` | R (entry) | token | Stable id.<br>**Wrong:** renumbering it. The alert contract resolves through this string. |
| `.strategies[].name` | R (entry) | string | Short name for the strategy. |
| `.strategies[].objective` | R (entry) | string | The specific behavior or outcome it detects.<br>**Wrong:** restating `behavior.definition`. A strategy is narrower than its object. |
| `.strategies[].incident_relevance` | R (entry) | prose | Whether it detects the documented outcome, a possible outcome, or a later consequence.<br>**Wrong:** silence. A consequence-detector then reads as detecting the behavior. |
| `.strategies[].detection_logic` | R (entry) | prose | Vendor-neutral logic precise enough to implement: conditions, exclusions, normalization, where signal ends and alert begins.<br>**Wrong:** query language or pseudocode. Do-not-exclude instructions belong in `forbidden_exclusions`. |
| `.strategies[].minimum_evidence` | R (entry) | prose | The minimum facts required to emit the conclusion, and what weaker evidence may be reported without overstating.<br>**Wrong:** listing nice-to-haves. This is the floor. |
| `.strategies[].correlation_context` | O | prose | Entity keys, sequence, time bounds, exact-versus-inferred joins, concurrency, ingestion delay, retention.<br>**Wrong:** omitting the join window when the strategy spans streams. |
| `.strategies[].detectors` | R (entry) | list | One or more capability classes that can carry the strategy. |
| `.strategies[].detectors[].detector_class` | · | token | The capability class, not a product.<br>**Wrong:** coining a fresh class per object, which makes the field unsearchable. |
| `.strategies[].detectors[].technologies` | · | list\<string\> | Candidate products, platforms, sensors, or open-source technologies.<br>**Wrong:** reading these as a support claim. See `product_support_caveat`. |
| `.strategies[].detectors[].contribution` | · | string | What this class can establish. |
| `.strategies[].detectors[].limitations` | · | string | What it cannot, and the deployment conditions.<br>**Wrong:** leaving it blank. A class with no stated limitation was not assessed. |
| `.strategies[].benign_explanations` | R (entry) | list | One entry per legitimate pattern satisfying some or all conditions.<br>**Wrong:** prose. This must be enumerable. |
| `.strategies[].benign_explanations[].explanation` | R (entry) | string | The legitimate activity, in one sentence. |
| `.strategies[].benign_explanations[].distinguishing_context` | C | string | Required whenever a discriminator exists, the normal case. Omit only for a base-rate or structural caveat that names no discriminator, such as "this is a residual signal, so its benign base rate is high by construction".<br>**Wrong:** manufacturing a discriminator to fill the slot. |
| `.strategies[].benign_explanations[].overlaps_incident_instance` | R (entry) | bool | `true` when this benign pattern also covers the incident instance: tuning it out would suppress the incident's own record.<br>**Wrong:** defaulting to `false` unchecked. That excludes the true positive on day one. |
| `.strategies[].benign_explanations[].tuning_hazard` | O | string | The tuning mistake an implementer would otherwise make here.<br>**Wrong:** a generic warning naming no exclusion. |
| `.strategies[].forbidden_exclusions` | O | list | Fields an implementer must never tune on: machine-checkable.<br>**Wrong:** leaving them buried in `detection_logic`. |
| `.strategies[].forbidden_exclusions[].field` | · | string | The field or exclusion form that must never be used. |
| `.strategies[].forbidden_exclusions[].reason` | · | string | Adversary-settable, or would suppress the incident's own record.<br>**Wrong:** "noisy", a reason to exclude, not to forbid. |
| `.strategies[].validation` | O | map | What was done to exercise the strategy. Never in `safe_conclusion`. Omit the block entirely when the strategy was never exercised: an absent block *means* untested. See [the absent block](#an-absent-validation-block-means-untested).<br>**Wrong:** an empty block written to look diligent. |
| `.strategies[].validation.status` | R (block) | enum | What was done.<br>**Wrong:** `validated-in-production`, deliberately not a member; `partially-validated` on an exercise that established nothing (that is `validation-withdrawn`); `untested`, which is represented only by an absent block. |
| `.strategies[].validation.status_note` | C | string | Required with `other`. |
| `.strategies[].validation.evidence` | R (block) | string | What was run or adjudicated, and what it established.<br>**Wrong:** "tested and works". Give the population, the counterfactual, the result. |
| `.strategies[].validation.method` | C | string | Required when `status` is `validated-in-lab` or `partially-validated`. What was run and where: the environment and its version, the absolute window, and the commands or procedure a reader would repeat.<br>**Wrong:** "tested locally". A method nobody can repeat is not a method. |
| `.strategies[].validation.measured` | C | string | Required with `method`. The counts that make the verdict checkable: what the attack matched, what the legitimate case matched, what the control matched, how many records were scored, and what was left out of them. Written as lab notes, not as a report on a testing method.<br>**Wrong:** a single number. A match count with no benign twin and no control is not a measurement. |
| `.strategies[].validation.evidence_ref` | O | string | The published artifact adjudicated against, as a reference id that resolves inside this repository. Omit the key when the evidence is not published and let `evidence` carry the detail.<br>**Wrong:** a sentence, or a pointer to an artifact the repository does not ship. |
| `.strategies[].expected_volume` | O | map | Expected firing volume, a sibling of `validation` and not a part of it. How noisy a strategy is and whether anyone has run it are unrelated questions, so a strategy may carry this alone, `validation` alone, both, or neither. Worth stating on a strategy nobody has run.<br>**Wrong:** volume in `safe_conclusion`, or nested back inside `validation`. |
| `.strategies[].expected_volume.band` | C | enum \| null | Write `null` when the basis is unavailable or the estimate straddles two bands, and give the figure in `note`. |
| `.strategies[].expected_volume.basis` | R (block) | enum | Where the estimate comes from.<br>**Wrong:** `source-stated` when the source gave a mechanism and you supplied the magnitude. |
| `.strategies[].expected_volume.note` | O | string | The population the band counts over, the exclusions applied, and what the basis actually reasons in. Say so when the population is not the same kind of thing as the behavior: a per-endpoint estate denominator under a behavior that accrues per workload or per principal does not divide through, and the convention is to carry the denominator and disown it here rather than silently convert it. |
| `.strategies[].blind_spots` | R (entry) | list | One entry per gap.<br>**Wrong:** an optimistic field, or prose instead of entries. |
| `.strategies[].blind_spots[].description` | R (entry) | string | The missing log, bypass, sensor gap, parsing difference, sampling limit, or evasion.<br>**Wrong:** a gap the strategy does not have, listed for balance. |
| `.strategies[].blind_spots[].routes_to` | C | string \| null | The sibling Tradecraft `id` covering this gap. `null` when it routes nowhere, the common case.<br>**Wrong:** routing to a sibling that does not in fact cover it. |
| `.strategies[].analyst_response` | R (entry) | prose | The source records, entities, related activity, and outcome questions to investigate.<br>**Wrong:** "escalate to tier 2". |
| `.strategies[].safe_conclusion` | R (entry) | string | One plain-language conclusion supported by `minimum_evidence`.<br>**Wrong:** anything stronger, or smuggling validation and volume in here. |
| `.supporting_technologies_considered` | O | prose | Technologies that reveal exposure or corroborate but are not primary event evidence: SAST, DAST, posture management, packet capture, memory or disk forensics, and why they fit or not.<br>**Wrong:** promoting one into `detectors[]`. |

**Blind-spot prefixes.** A leading category prefix on
`blind_spots[].description` (`Evasion:`, `Missing telemetry:`,
`Attribution limit:`, and so on) is optional and free-form. It exists so a
reader can group a long list at a glance; it is not a classification, and there
is no list to pick from. Reuse a prefix the corpus already uses where one fits,
coin one where none does, and leave it off where the gap resists a category.

### An absent `validation` block means untested

Omit `validation` when a strategy has not been exercised. Absence is the only representation of the
untested state. A present block records an exercise or a reason validation cannot be obtained, and
its status must be one of the remaining vocabulary members.

### Detection vocabularies

#### `validation.status`

Pick one member; if none fits, write `other` and say why in `status_note`.

| Member | Means |
|---|---|
| `validated-in-lab` | exercised against lab data, and the result stands |
| `partially-validated` | exercised against lab data; part of the strategy is established and the rest is not: `evidence` must state *which* half, and what the established half establishes |
| `validation-withdrawn` | an exercise ran and its result does not stand: it disproved the strategy, or never reached it: `evidence` must state what ran and why it does not stand |
| `lab-telemetry-verified` | captured records adjudicated with no query ever fired |
| `not-obtainable` | validation cannot be obtained |

`validated-in-production` is deliberately not a member.

`validation-withdrawn` is not a softer untested state. An absent block says nothing ran.
`validation-withdrawn` says something ran and the reader gains a real negative
result from it: a benign population the strategy fired on, a conjunct the logic
never expressed, an inventory that turned out to be the identity under test. That
is worth more than silence, and it must not be reported as partial success. A
strategy carrying it is not deployable on the logic that was exercised.

#### `expected_volume.band`

Pick one of `rare` · `1-10-per-day` · `100-plus-per-day` · `unbounded`, or write
`null`. There is no `other`.

#### `expected_volume.basis`

Pick one of `source-stated` · `mechanism-proven-magnitude-inferred` · `modelled` ·
`unavailable`. There is no `other`.

Where the number came from, not how confident you are in it. A single lab
capture is not `source-stated`: one measurement on one cluster is a floor, and
calling it a rate is a fabricated measurement. Where the source gave the
mechanism and you supplied the magnitude, that is
`mechanism-proven-magnitude-inferred`. Where it gave neither, `unavailable` with
a null `band` is the correct and honest answer.

## 9. `detector_requirements`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.required` | R | prose | Hard preconditions: parsing, canonicalization, classification, evidence retention, entity attribution, outcome-aware alert text.<br>**Wrong:** mixing nice-to-haves in, hiding what gates deployment. |
| `.optional` | O | prose | Capabilities that improve the detection but do not gate it, and what each buys.<br>**Wrong:** inventing a tier to fill the slot. Several objects legitimately have none. |
| `.correlation` | O | map | Only when a strategy joins streams or accrues score across time. |
| `.correlation.capability` | R (block) | prose | Aligned clocks, retention, stable identifiers, workload attribution, exact-versus-inferred joins.<br>**Wrong:** "correlate the events". Name what makes the join possible. |
| `.correlation.window` | O | string | The join window, when load-bearing.<br>**Wrong:** omitting it when precision depends on it. |
| `.baselines` | O | list | Entries only when a deciding set must be built before anything is enabled. |
| `.baselines[].name` | · | string | What the baseline table holds. |
| `.baselines[].window` | · | string | Build or staleness window, e.g. `30-day`.<br>**Wrong:** omitting it. An agent needs the build time before it turns anything on. |
| `.baselines[].seeded_from` | · | string | The sanctioned seeding source, and what it must never be seeded from.<br>**Wrong:** naming only the source. The prohibition is what stops the baseline being seeded with the attack. |
| `.product_support_caveat` | R | prose | The standard caveat that a named product is only a candidate, plus what verification means for this object.<br>**Wrong:** shipping the boilerplate untouched. The appended sentences are the point. |

## 10. `missing_context`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.unverified` | R | list\<string\> | One or more incident facts, environment details, event schemas, or implementation behaviors the sources do not verify.<br>**Wrong:** only incident facts. Unverified schema behavior is what breaks implementations. |
| `.assumptions` | R | list | One or more inferences the analysis required. |
| `.assumptions[].id` | R (entry) | token | Stable within the object.<br>**Wrong:** renumbering after publication. |
| `.assumptions[].verdict_changing` | O | bool | `true` only when a wrong assumption flips a manifestation answer or the safe conclusion; `false` when it only makes a rule silent, noisy, or mis-scoped.<br>**Wrong:** marking everything `true`, which erases the distinction. |
| `.assumptions[].statement` | R (entry) | prose | The inference and why it remains one. Close it as described below.<br>**Wrong:** no consequence and no test. Then nobody can retire it. |
| `.standing_negatives` | O | list | Absence claims true only as of a review date.<br>**Wrong:** putting an absence claim in `unverified`, where it acquires no expiry. |
| `.standing_negatives[].statement` | R (entry) | prose | The absence claim exactly, and what was checked to establish it.<br>**Wrong:** "X does not exist" with no statement of what was searched. |
| `.standing_negatives[].ref` | O | string | An `evidence.references[].id` attesting the absence. |
| `.standing_negatives[].valid_until` | R (entry) | date | Mandatory expiry, `YYYY-MM-DD`.<br>**Wrong:** an undated standing negative, the exact defect this block exists to prevent. |
| `.standing_negatives[].recheck` | R (entry) | string | The specific check that re-establishes or retires the claim.<br>**Wrong:** "re-verify periodically". |

### How to close an assumption statement

Every `assumptions[].statement` closes with two clauses:

1. **`If wrong, <consequence>.`** What breaks in *this* object if the inference
   is false. Name the field or verdict that moves, not a vague risk.
2. **`Resolve it by <test>.`** The specific observation, document, query, or
   estate check that would settle it.

This is a convention of the format rather than a machine-checked constraint, and
it is the single most load-bearing convention in block 10. State the consequence
and the test so a reviewer can assess the assumption and retire it. The
consequence clause says how much being wrong costs, whether it makes a rule
slightly noisy or inverts a `detectable` answer, which is the same distinction
`verdict_changing` encodes. The test clause is what lets anyone other than the
original author end it. An assumption with no stated test survives every review,
because no reviewer can tell what would settle it, and over a corpus untestable
assumptions accumulate until the gap blocks stop meaning anything.

**Wrong:** "The rendering step runs before the type coercion." A bare claim.
Nothing to check, and nothing that would ever retire it.

**Right shape:** "The template rendering step ran before the type coercion rather
than after it. The account states the field 'was actually a Jinja2 template' and
that the renderer 'wrongly evaluated it'; it publishes no pipeline ordering. This
pack inferred the ordering from the outcome, which is reasoning backward from a
result to a mechanism. If wrong, the strict cast on the pre-render value is not
the predicate this behavior needs and `manifestation.possible` is scoped to a
surface that does not exist as described. Resolve it by reading the service's
parser source for the position of the rendering call relative to type coercion,
or in a lab by instrumenting a reference parser and recording the order of the
two calls for one submitted specification."

The right shape quotes the source's exact words, marks the step this pack
supplied, names the field that flips, and hands over a test. A reviewer can act on
it.

## 11. `missing_states`

| Field | Req | Type | Definition |
|---|---|---|---|
| `missing_states` | R | list | Named states in which evidence is absent, and how a detector must degrade. |
| `missing_states[].id` | R (entry) | token | `SCREAMING_SNAKE`.<br>**Wrong:** changing it after publication. |
| `missing_states[].meaning` | R (entry) | string | The missing log, field, attribution, context, or detector capability. An optional leading category prefix: see below. |
| `missing_states[].coverage_claim` | O | enum | What may still be claimed while the state holds. See the vocabulary below.<br>**Wrong:** forcing a value when the response states no verdict. Omit it. |
| `missing_states[].response` | R (entry) | string | How the detector or analyst should limit the conclusion or recover context.<br>**Wrong:** restating the gap. This is the instruction, not the diagnosis. |

**Category prefixes.** A leading category prefix on `meaning` (`Missing
telemetry:`, `Analyst trap:`, and so on) is optional and free-form, exactly as on
`blind_spots[].description`. It exists so a reader can group a long list at a
glance; it is not a classification, and there is no list to pick from.

### `coverage_claim` vocabulary

Pick one of `no-coverage` · `coverage-limited` · `coverage-unaffected`. There is
no `other`.

## 12. `alert_evidence_contract`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.required_fields` | R | list\<token\> | Fields every alert must carry. Tokens are deliberately object-specific.<br>**Wrong:** shipping the starting set unchanged. The specialisation is the point. |
| `.conditional_fields` | O | list | Fields required only for a particular supported outcome. |
| `.conditional_fields[].field` | · | token | The conditionally required field. |
| `.conditional_fields[].condition` | · | string | Exactly when it must be included.<br>**Wrong:** "when available", not a condition. It licenses omitting the field always. |
| `.recommended_title` | R | string | A title accurate to the outcome actually detected.<br>**Wrong:** naming the behavior when the evidence proves only an attempt. |
| `.unsupported_title` | R | string | A worked example of a title that *would* overstate.<br>**Wrong:** omitting it. It exists so a reviewer sees the failure mode concretely. |

### `required_fields` starting set

Eleven tokens, to specialize per object. Write anything: this is free text, not
a fixed list.

| What it answers | Tokens |
|---|---|
| Which object and strategy fired | `tradecraft_id` · `detection_strategy_id` · `plain_language_behavior_claim` |
| Who and what | `actor_or_explicit_missing_state` · `affected_object_or_resource` · `workload_or_service_context_when_available` · `normalized_behavior_target` |
| What happened, and when | `observed_decision_or_outcome` · `timestamps_and_correlation_context` |
| What the claim rests on | `source_types_and_durable_evidence_references` · `confirmed_facts_inferences_assumptions_and_missing_capabilities` |

Five of them recur in every object of the reference pack and are the de-facto
spine: `tradecraft_id`, `plain_language_behavior_claim`,
`detection_strategy_id`, `source_types_and_durable_evidence_references`,
`confirmed_facts_inferences_assumptions_and_missing_capabilities`. The rest are
worked suggestions, to be replaced with the entities and outcomes this object's
alert must name. Reference-pack objects carry 12 to 18 tokens.

## 13. `evidence`

| Field | Req | Type | Definition |
|---|---|---|---|
| `.confirmed_incident_behavior` | R | prose | The procedure facts the primary source directly establishes. State the outcome as precisely as the source does, neither promoted from attempted to achieved, nor hedged below what the source plainly asserts. It may also carry the pack's reading of those facts, where that reading is signed in place: named as the pack's and not the source's, in the same block, so a reader can see the seam without leaving the field. Unsigned inference is the violation, not inference. A reading that changes a verdict must also be registered in `missing_context.assumptions`.<br>**Wrong:** inference presented as the source's finding, or a reading left unsigned. |
| `.supported_conclusion` | R | prose | The strongest conclusion the published evidence permits, and when a detector could emit it.<br>**Wrong:** the conclusion you wish the evidence supported. |
| `.unsupported_conclusions` | R | prose | Conclusions the incident does *not* establish, including advanced outcomes needing independent telemetry.<br>**Wrong:** leaving it thin. This is what stops the object being over-read. |
| `.procedure_boundary` | R | prose | What separates this procedure from adjacent behaviors in the same incident.<br>**Wrong:** using a later successful technique as evidence this one succeeded. |
| `.references` | R | list | One or more sources. |
| `.references[].id` | R (entry) | token | `REF-…`, unique in the object, cited verbatim by `attestation_ref`, `evidence_ref`, `standing_negatives[].ref`.<br>**Wrong:** renaming one and leaving the citations dangling. |
| `.references[].type` | R (entry) | enum | What kind of source it is.<br>**Wrong:** typing a news write-up as a `-primary-source`. News-tier campaign coverage is `incident-campaign-secondary-source`. |
| `.references[].title` | R (entry) | string | Title as published. |
| `.references[].publisher` | R (entry) | string | Publisher or vendor. |
| `.references[].url` | R (entry) | string | Canonical URL.<br>**Wrong:** a search result or archive link when a canonical one exists. |
| `.references[].local_path` | O | string | Repository-relative path to a local copy, when one exists. |
| `.references[].trust_tier` | R | enum | Who published it. Pick one member; there is no `other`. `primary` only where the publisher is the party the incident happened to.<br>**Wrong:** reading it as credence. It says nothing about whether the claim is believed. |
| `.references[].verification` | R | enum | Whether the URL was fetched and checked, and nothing else. Pick one member; there is no `other`.<br>**Wrong:** `confirmed` for a link you did not open. The honest answer for an unfetched URL is `not-resolved-out-of-scope`. Scope caveats ride in `evidence_use`. |
| `.references[].citation_caveat` | O | enum | Whether the citation needs care, and why. Pick one member; there is no `other`. A property of *this citation*, not of the publisher and not of the claim. See [the division of labor](#trust_tier-verification-and-citation_caveat).<br>**Wrong:** setting it to mark a source you simply distrust. That is a credence judgment, and the format has no field for one. |
| `.references[].citation_caveat_note` | C | string | Required whenever `citation_caveat` is set; omit otherwise. What specifically is at issue, and whether the claim survives discounting this source entirely.<br>**Wrong:** "treat with caution". Nobody can check that, or ever retire it. |
| `.references[].evidence_use` | R (entry) | string | Which claims *in this object* the source supports, and which it does not.<br>**Wrong:** a summary of the source. This is a claim-to-source binding. |
| `.references[].relevant_locations` | O | list\<string\> | Section, paragraph, timestamp, page, or line locators.<br>**Wrong:** citing a forty-page vendor document with no locator. |

### Signing an inference

An object may rest on inference. It may never present inference as something a
source found. `confirmed_incident_behavior` is where the two mix most often, so
the attribution has to be carried in the sentence itself.

**Wrong:** "The loader rejected the declaration before any outbound request, so
the attempt stopped at the application boundary."

**Correct:** "The account writes 'before any fetch'. That a fetch is the same
event as an initiated outbound request, and that nothing earlier in the client
stack had already run, is this pack's reading of that phrase rather than a
mechanism the account describes."

Only the second can be checked. It names the source's words and the step this
pack supplied, so a reviewer knows what to test. An inference written as a
finding has no source to re-check and no test that would refute it: it survives
every review and silently decides the object's verdict.

### Evidence vocabularies

#### `references[].type`

Pick one member; if none fits, write `other`.

| Group | Members |
|---|---|
| The incident itself | `incident-procedure-primary-source` · `incident-campaign-primary-source` · `incident-campaign-secondary-source` |
| Documentation | `attack-surface-documentation` · `telemetry-documentation` |
| Reference material | `framework` · `detection-modeling-reference` |

Use `-secondary-source` for news-tier campaign sources; typing them as
`-primary-` would be false.

#### `trust_tier`

Pick one of `vendor` · `primary` · `news` · `researcher` · `registry` ·
`aggregator`. There is no `other`.

Who published it, and nothing else. `primary` only where the publisher is the
party the incident happened to; a vendor writing about its own product in
someone else's incident is `vendor`.

#### `verification`

Pick one of `confirmed` · `confirmed-with-scope-caveat` ·
`not-resolved-out-of-scope` · `not-resolved-no-url-exists`. There is no `other`.

#### `citation_caveat`

Pick one member; there is no `other`. Each member is a property of the citation,
not a judgment of the publisher.

| Member | Use when |
|---|---|
| `headline-overstates-body` | the title or summary claims more than the passage being cited supports |
| `partial-use` | only a bounded part of the source is relied on and the rest is not endorsed |
| `contradicted-elsewhere` | another source disagrees with the passage being used |
| `superseded` | a later publication by the same or a better-placed publisher replaces it |

### `trust_tier`, `verification` and `citation_caveat`

Three optional fields sit next to each other on a reference, and each answers a
different question. Keeping them apart is what stops any of them drifting into a
confidence score.

| Field | Answers | Says nothing about |
|---|---|---|
| `trust_tier` | Who published it: the publisher class. | whether it was read, or whether it is right |
| `verification` | Whether the URL was fetched and checked to resolve to what it claims. | whether the content is correct |
| `citation_caveat` | Whether the citation needs care, and in which of four ways. | how much the claim is believed |

A caveat is a reader's instruction: open this one carefully, and here is what to
watch for. `citation_caveat_note` must then state what specifically is at issue
and whether the claim survives discounting the source entirely, naming the
independent corroboration where it does. That last clause is what makes the
caveat auditable: it lets a later reviewer drop the citation without reopening
the finding.

### There is no credence axis

No field in this format says how much a claim is believed, and none may be
invented locally. `citation_caveat` did not add a place to start.

`contradicted-elsewhere` is the member most likely to be misread as credence. It
licenses no conclusion about which source is right; that adjudication is prose,
and it belongs in `citation_caveat_note` and `evidence_use`.

So a source that argues against the claim it is cited for is handled like this:

1. **Leave `trust_tier` alone.** It stays the true publisher class. A contradicted
   news source is still `news`.
2. **Set `verification: confirmed-with-scope-caveat`.** The URL was fetched, and
   something about the fetch does not travel cleanly to the claim.
3. **Set `citation_caveat: contradicted-elsewhere`**, with the note. This is the
   machine-readable half: it is what makes the warning visible to anything reading
   the pack mechanically, rather than only to a human who reads the prose.
4. **Carry the whole situation in `evidence_use`.** That field is already a
   claim-to-source binding, so it is the right place for the adjudication. State
   four things: which specific passage is being used; what in the source cuts
   against it; why the two are reconcilable, or that they are not; and whether the
   claim survives discounting this source entirely, naming the independent
   corroboration if it does.

**Wrong:** downgrading `trust_tier` to signal doubt. That corrupts a publisher
classification into an opinion, and every consumer that groups by publisher
silently gets the wrong answer.

**Wrong:** setting a `citation_caveat` because a source feels unreliable. The
field takes a citation-level fact you can point at: a headline, a bounded
passage, a disagreeing source, a later publication. "Vendor blog, treat with
caution" names none of those, is not checkable, and would make the field the
credence axis the format refuses to have.

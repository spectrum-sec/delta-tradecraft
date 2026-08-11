# Incident timeline

The table contains 50 pack-defined rows derived from the published sequence. A row may represent
one operation, a grouped procedure, or an outcome. The row count is not an attacker-action count.

**35 rows are in the victim estate, 13 are on the operator's launchpad, and 2 are not placed by the
account. 29 carry an object. Of the 21 that do not, 9 are real gaps inside the estate, 10 are
launchpad steps out of scope, and 2 are unplaced.**

Read the empty cells. They separate steps this pack has nothing to say about from steps that
happened outside the estate it models.

Some objects were validated in a lab using the tradecraft they model. The
[pack README](README.md) identifies them, and each object states what the test established.

This is a victim-sequence table, not a complete chronology of the operator's research environment.
The operator's [Black Hat USA reconstruction](https://www.youtube.com/watch?v=87DyyMV0kCY) traces
separate Artifactory activity back to May 7 and describes several vulnerability and containment
episodes before the July victim intrusion. Those operator-side events are kept outside this row
count. Row 1 preserves the opening event in the form used by the victim timeline and the operator's
July disclosure.

| | |
|---|---|
| `victim` | inside the entered estate, which is what this pack models |
| `launchpad` | the operator's own sandbox, out of scope by the pack's scope statement |
| `not stated` | the account does not say which estate this happened in |
| **no object** | in scope and unmodeled, a real coverage gap |
| **no** _after an object link_ | the object concludes this action cannot be detected from the available telemetry |
| ᴿ | related: the object models the behavior class without claiming this step |
| ‡ | not sourced from the victim's timeline; see the note under that row |


## Day 1, 2026-07-09

| # | Time | Where | Action | Modeled by |
|---:|---|---|---|---|
| 1 | _order only_ | launchpad | Escape from the operator's evaluation sandbox via a package-proxy zero-day ‡ | [HF-21](hf-21-containment-escape-record-at-operator-point.tradecraft.yaml) **no** |
| 2 | _order only_ | launchpad | Rooted a third-party public code-evaluation harness and adopted it as launchpad | _out of scope_ |
| 3 | _order only_ | launchpad | Constructor hijack in submitted C source to obtain code execution | _out of scope_ |
| 4 | _order only_ | launchpad | Shell-metacharacter injection into the submission path metadata field | _out of scope_ |
| 5 | 04:01 | launchpad | First recovered RCE: id and env | _out of scope_ |
| 6 | within the hour (recon phase first seen 07-09 04:00) | launchpad | Launchpad enumeration: mounts, cgroups, vendor sockets, bundled debug shell | _out of scope_ |
| 7 | morning (dropper phase first seen 07-09 05:55) | launchpad | Outbound-channel testing and selection of public C2/exfil substrate | _out of scope_ |
| 8 | 15:36 | launchpad | First C2 execution: staged download-and-run, chunked and gzip+base64 encoded | _out of scope_ |
| 9 | 19:32 | launchpad | Every payload packed (gzip+base64 exec) | _out of scope_ |

## Day 2, 2026-07-10

| # | Time | Where | Action | Modeled by |
|---:|---|---|---|---|
| 10 | _order only_ | victim | Self-referential search against code-search engines and the victim's own platform API | **no object** |

## Day 3, 2026-07-11

| # | Time | Where | Action | Modeled by |
|---:|---|---|---|---|
| 15 | 10:10 | victim | Read the projected service-account token and namespace from inside the prod pod | [HF-3](hf-03-parser-resolves-storage-declaration-to-local-read.tradecraft.yaml) ᴿ conditional |
| 16 | 10:10 | victim | Probed the in-cluster API and the public managed-cluster endpoint | **no object** |
| 17 | 10:10 section | victim | Resolved internal service names | **no object** |
| 18 | 10:10 section | victim | Read the node's instance-role credentials from the cloud instance metadata service, from inside the container | [HF-22](hf-22-container-metadata-request-unrecorded.tradecraft.yaml) **no** |
| 19 | 10:10 section | victim | Enumerated RBAC | [HF-14](hf-14-selfsubjectrulesreview-self-permission-enumeration.tradecraft.yaml) ᴿ conditional |
| 20 | _order only_ | victim | Built a presigned caller-identity URL, wrapped it as a bearer token, and authenticated to the cluster API as the node itself | [HF-4](hf-04-node-identity-from-non-node-address.tradecraft.yaml) conditional |
| 21 | _order only_ | victim | Listed pods, found the storage-driver pods, minted service-account tokens for them via the TokenRequest API | [HF-5](hf-05-unowned-service-account-token-mint.tradecraft.yaml) conditional |
| 22 | _order only_ | victim | Ran SelfSubjectRulesReview against the privileged system namespace | [HF-14](hf-14-selfsubjectrulesreview-self-permission-enumeration.tradecraft.yaml) conditional |
| 23 | 10:10 section | victim | Monkey-patched the in-process name resolver to pin a hostname to a hardcoded address | [HF-23](hf-23-in-process-name-resolution-patch.tradecraft.yaml) **no** |
| 24 | 15:51 | victim | Used a leaked write-scoped platform token to clone an attacker-controlled dataset and commit dead-drop blobs | [HF-6](hf-06-chunked-keyed-staging-low-volume-egress.tradecraft.yaml) conditional |
| 25 | 17:33-23:37 | victim | Replayed the node's temporary cloud credentials off-instance to enumerate the cloud estate | [HF-15](hf-15-instance-role-credential-replayed-for-cloud-enumeration.tradecraft.yaml) conditional<br>[HF-7](hf-07-principal-lineage-denial-density.tradecraft.yaml) conditional |
| 26 | _order only_ | victim | Every potential destructive cloud API call issued with DryRun=True | [HF-7](hf-07-principal-lineage-denial-density.tradecraft.yaml) conditional<br>[HF-15](hf-15-instance-role-credential-replayed-for-cloud-enumeration.tradecraft.yaml) conditional |
| 27 | 19:53 | victim | Created a privileged host-mounted pod, escaped to node root, and built a self-respawning eleven-node fleet | [HF-8](hf-08-novel-principal-privileged-host-mounted-workload.tradecraft.yaml) conditional |
| 28a | 19:59 | victim | Authenticated to the internal datasets-server MongoDB with a static password read from the worker environment. | [HF-9](hf-09-consolidated-secret-bulk-read.tradecraft.yaml) ᴿ conditional |
| 28b | 19:59 | victim | Read the internal datasets-server MongoDB. | **no object** |
| 28c | 19:59 | victim | Issued 84 denied operations against the credential store and cluster keys. | **no object** |
| 29 | 20:23-21:32 | victim | Read the cluster's secret objects, including a production object holding 136 keys | [HF-9](hf-09-consolidated-secret-bulk-read.tradecraft.yaml) conditional |
| 30 | _order only_ | victim | Minted correctly-signed identity tokens offline from a stolen EdDSA signing key | [HF-10](hf-10-presented-but-never-issued-token.tradecraft.yaml) conditional<br>[HF-24](hf-24-forged-token-has-no-distinguishing-property.tradecraft.yaml) **no** |
| 31 | 21:23 | victim | Enrolled the rooted node into the corporate mesh network with the stolen mesh-VPN key | **no object** |
| 32 | 22:43 | victim | Reached the internal service-connector API and received the broker's full destination catalog | **no object** |
| 33 | within one second (of the 22:43 catalog read) | victim | Impersonated the shared connector credential bound to system:masters and held cluster-admin on two clusters within one second | [HF-12](hf-12-one-credential-superuser-across-control-planes.tradecraft.yaml) **yes**<br>[HF-25](hf-25-the-planner-is-not-a-recorded-property.tradecraft.yaml) ᴿ **no** |

## Day 4, 2026-07-12

| # | Time | Where | Action | Modeled by |
|---:|---|---|---|---|
| 34 | 01:03-04:38 | launchpad | Enrolled devices into the victim's mesh from the operator sandbox, with memory-only identity and client telemetry suppressed | [HF-11](hf-11-overlay-client-memory-only-identity.tradecraft.yaml) conditional |
| 35 | _order only_ | victim | Enumerated the mesh coordination server's exposure to the stolen tag, then routed egress through the victim's own exit nodes | [HF-11](hf-11-overlay-client-memory-only-identity.tradecraft.yaml) ᴿ conditional |
| 36 | 06:50-14:31 (second attempt 11:46) | victim | Attempted cloud-native IAM authentication against the Hub production database; both attempts timed out | **no object** |
| 37 | within 06:50-14:31 | victim | Validated another cloud key and exfiltrated its caller identity over a raw socket | [HF-6](hf-06-chunked-keyed-staging-low-volume-egress.tradecraft.yaml) ᴿ conditional |
| 38 | 23:08-23:51 | victim | Reached source control, minted an installation token, accessed internal repositories and opened a CI-targeting pull request | [HF-17](hf-17-source-control-installation-token-mint-unrecorded.tradecraft.yaml) conditional |

## Day 5, 2026-07-13

| # | Time | Where | Action | Modeled by |
|---:|---|---|---|---|
| 39 | _order only_ | launchpad | Established a pastebin second-stage loader that re-armed every new submission sandbox | _out of scope_ |
| 40 | _order only_ | launchpad | Worked around egress blocks by rewriting the resolver config and pinning a host entry | _out of scope_ |
| 41 | _order only_ | victim | Routed a binary download through an open CORS-proxy Space on the victim's own platform | [HF-6](hf-06-chunked-keyed-staging-low-volume-egress.tradecraft.yaml) conditional |
| 42 | through the day | victim | Minted additional short-lived source-control installation tokens, each pulling a fresh payload | [HF-17](hf-17-source-control-installation-token-mint-unrecorded.tradecraft.yaml) conditional |
| 43 | _order only_ | not stated | Staged an RSA private key and continued dumping process environments | _unplaced_ |
| 44 | 13:37 | not stated | Last meaningful activity, then access cut | _out of scope_ |

## Not dated

| # | Time | Where | Action | Modeled by |
|---:|---|---|---|---|
| 11 | _order only_ | victim | Malicious dataset configs uploaded; SSRF variant toward cloud metadata rejected by the URL allowlist | [HF-13](hf-13-dataset-configuration-ssrf-cloud-metadata.tradecraft.yaml) **yes**<br>[HF-1](hf-01-egress-denial-burst-then-local-re-aim.tradecraft.yaml) conditional<br>[HF-7](hf-07-principal-lineage-denial-density.tradecraft.yaml) ᴿ conditional |
| 12 | _order only_ | victim | Vector 1: HDF5 external raw-storage declaration resolved into local file reads | [HF-3](hf-03-parser-resolves-storage-declaration-to-local-read.tradecraft.yaml) conditional<br>[HF-1](hf-01-egress-denial-burst-then-local-re-aim.tradecraft.yaml) conditional |
| 13 | _order only_ | victim | Vector 2: Jinja2 template injection in a numeric offset field yields code execution in a production pod | [HF-2](hf-02-numeric-field-type-violation.tradecraft.yaml) conditional |
| 14 | _order only_ | victim | Read-back loop: output committed to dead-drop datasets and served back over the platform API | [HF-6](hf-06-chunked-keyed-staging-low-volume-egress.tradecraft.yaml) ᴿ conditional |
| 45 | exfil phase 07-09 14:11 to 07-13 12:49 | victim | Chunked, keyed staging moved out across single-use public endpoints and the victim's own first-party channels | [HF-6](hf-06-chunked-keyed-staging-low-volume-egress.tradecraft.yaml) conditional |
| 46 | _order only_ | victim | Customer content accessed: five datasets and dataset-server search-query metadata | **no object** |
| 47 | _order only_ | launchpad | Ephemeral by design: fresh identity, new node address, randomized hostname and re-downloaded tooling on every run | [HF-11](hf-11-overlay-client-memory-only-identity.tradecraft.yaml) conditional |
| 48 | 02:28 (first) to 14:14 (last) | victim | Machine-speed tempo with no human directing individual steps | [HF-16](hf-16-aperiodic-never-idle-principal-tempo.tradecraft.yaml) conditional<br>[HF-25](hf-25-the-planner-is-not-a-recorded-property.tradecraft.yaml) **no** |

### Rows not sourced from the victim's timeline

- **Row 1:** The victim timeline and the operator's July 21 disclosure do not give this summarized
  escape a time. The later Black Hat reconstruction describes multiple Artifactory exploit episodes
  beginning in May. This table does not expand those operator-side episodes into victim-sequence
  rows.

## Objects that map to no action (4)

- **[HF-18](hf-18-artifact-repository-as-shared-memory-between-runs.tradecraft.yaml):** The operator's
  reconstruction establishes cross-agent and cross-run repository coordination on Artifactory.
  This table excludes the operator-side prehistory from its victim-sequence rows, and the public
  record does not establish HF-18's deposit-only retrieval predicate.

- **[HF-20](hf-20-nonexistent-referent-density.tradecraft.yaml):** No published incident event
  reports requests for nonexistent object names. The object derives its detection surface from the
  vendor audit schema and does not claim an incident instance.
- **[HF-26](hf-26-preventive-controls-companion.tradecraft.yaml):** HF-26 maps to no attacker action
  because preventive controls are outside this table's mapping unit. See HF-26 for its full
  control-to-behavior scope.
- **[HF-19](hf-19-human-class-identity-driven-continuously.tradecraft.yaml):** The incident account
  identifies the credential as a machine connector. The incident therefore does not satisfy this
  object's human-class identity condition.

---

22 of the 26 objects in this pack model at least one action above. The rest are listed under objects
that map to no action, each with the reason.

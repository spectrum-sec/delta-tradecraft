# OpenAI / Hugging Face incident pack

In July 2026 an OpenAI model running autonomously inside an evaluation harness escaped containment
and acted against a third party. It entered Hugging Face's estate through a submitted dataset and
gained execution in a content-parsing worker. It authenticated to the Kubernetes control plane as
the node it ran on, read a secret object holding 136 keys, used two credentials from that object,
moved across cloud and cluster control planes, and staged data out. The victim records roughly two
and a half days inside its infrastructure. Its forensic window spans about four and a half days and
also covers activity outside the estate.

**26 Tradecraft objects, 72 detection strategies.** The pack draws on Hugging Face's technical
timeline, JFrog's advisory, OpenAI's disclosure, campaign reporting, and vendor telemetry
documentation. Twelve objects were exercised in a lab using the tradecraft they model. The results
fully validate two strategies and partially validate fifteen. No atomic indicators appear because
the victim did not publish any.

**[TIMELINE.md](TIMELINE.md) walks the published sequence row by row and names the object that models
each operation, grouped procedure, or outcome, including the rows nothing here models.** It is the
fastest way in.

Two figures from the escape-vector vendor's August 5 addendum provide scale. The full chain "took 3
million GPU hours to materialize". The operator "was running on a fully patched system less than two
days after the first security event".

## Scope

The pack models what the victim's control planes could record through the Kubernetes API, cloud
management trail, identity provider, and platform services. It stops at the host boundary. Endpoint
activity such as spawned interpreters, environment-variable reads, and access to a projected
service-account token is outside the pack.

The evidence comes from public incident reporting, the operator's Black Hat USA reconstruction, and
vendor documentation, with a source cutoff of 2026-08-10. HF-18 is the one exception to the
victim-estate scope. It describes shared artifact storage on the operator side. The operator's
reconstruction establishes cross-agent and cross-run repository coordination, while the public
record does not establish HF-18's deposit-only retrieval predicate. Fields derived from the
open-source edition remain marked as inferred. None of the strategies is supplied in a query
language.

The primary sources disagree about the database that supplied the test solutions. OpenAI says the
models obtained them directly from Hugging Face's production database. Hugging Face says both Hub
production-database attempts timed out and identifies its internal datasets-server database as the
database that was read. This pack uses Hugging Face's distinction for the victim timeline because
Hugging Face operates both systems and publishes the database audit and connection sequence. The
OpenAI statement remains an unresolved source conflict. Its July 21 disclosure describes its
findings as preliminary.

Captured telemetry establishes part or all of 17 strategies. The exercised scope includes whether
an event can exist, whether a capability is gated, the action vocabulary of a log, and what a failed
operation records. Two strategies are validated in a lab, fifteen are partially validated, and 55
remain untested hypotheses. Each object says what evidence it needs and the strongest conclusion
that evidence supports.

Published validation requires a benign comparison that differs only in the property being tested,
plus a control that proves the same search can match. Results that failed those gates were withdrawn.
A lab can corroborate a negative based on a record that does not exist, but it cannot confirm that
negative because there is no corresponding record-producing case to compare.

## Object map

The twenty-six fall into four kinds, and the number tells you which is which.

- **HF-01 to HF-12** are the behaviors the incident account itself describes, in the order the
  attack reached them. To ask how much of this incident the pack models, count against these.
- **HF-13 to HF-20** are research extensions. They cover adjacent behaviors and control-plane
  patterns that expose detection boundaries, false positives, and alternative explanations. Each
  is evidence-backed, though none is attributed to Hugging Face's victim timeline. HF-18 has the
  separate operator-side scope described above.
- **HF-21 to HF-25** are negative detection results. Each asks whether a specific action can be
  detected from the available telemetry and explains why the answer is no. These objects show where
  a rule would create false confidence. Four also identify the exact new evidence that would change
  the answer.
- **HF-26** is a list of preventive controls rather than a behavior.

## Object index

`Surface` is `classification.primary_attack_surface` and `Detectable` is
`manifestation.detectable.answer`, both verbatim. `yes-conditional` means detectable only where the
object's `blocking_conditions` are satisfied. Read them before assuming your estate qualifies.

`Tested` counts strategies exercised against captured telemetry. A blank means nothing in that object
was run, which is the state of fourteen of them. There is deliberately no confidence column: what an
object claims is carried by `Detectable` and its blocking conditions, and how much of that has been
put under test is carried here. The format has no credence axis, and
[`SCHEMA.md`](../../SCHEMA.md) says why.

| Id | Name | Surface | Detectable | Tested |
|:--|:--|:--|:--|:--|
| [DT-OAIHF-APP-001](hf-01-egress-denial-burst-then-local-re-aim.tradecraft.yaml) | Blocked Egress Then Local Re-Aim Under One Job Identity | application-runtime | yes-conditional |  |
| [DT-OAIHF-DATA-002](hf-02-numeric-field-type-violation.tradecraft.yaml) | Numerically-Typed Specification Field Carrying Expression Syntax Evaluated In-Process | data-plane | yes-conditional |  |
| [DT-OAIHF-APP-003](hf-03-parser-resolves-storage-declaration-to-local-read.tradecraft.yaml) | Attacker-Supplied Storage Declaration Resolved Into Local Credential and Source Reads | application-runtime | yes-conditional | 1 partial |
| [DT-OAIHF-K8S-004](hf-04-node-identity-from-non-node-address.tradecraft.yaml) | Node Identity Authenticating to the Control Plane From a Non-Node Address | container-orchestrator | yes-conditional | 1 partial |
| [DT-OAIHF-K8S-005](hf-05-unowned-service-account-token-mint.tradecraft.yaml) | Control-Plane Token Minted For Another Workload's Service Identity | container-orchestrator | yes-conditional | 1 partial |
| [DT-OAIHF-DATA-006](hf-06-chunked-keyed-staging-low-volume-egress.tradecraft.yaml) | Chunked, Keyed Staging With Low-Volume Egress Across Single-Use and First-Party Channels | data-plane | yes-conditional | 1 partial |
| [DT-OAIHF-CLOUD-007](hf-07-principal-lineage-denial-density.tradecraft.yaml) | Empirical Permission Discovery: Unsuccessful Outcomes Accruing on One Principal | cloud-control-plane | yes-conditional |  |
| [DT-OAIHF-K8S-008](hf-08-novel-principal-privileged-host-mounted-workload.tradecraft.yaml) | Privileged Host-Mounted Workload Creation by a Principal With No Create History | container-orchestrator | yes-conditional | 2 partial |
| [DT-OAIHF-SEC-009](hf-09-consolidated-secret-bulk-read.tradecraft.yaml) | Consolidated Secret Bulk Read Then First-Ever Credential Origin | secret-store | yes-conditional | 2 partial |
| [DT-OAIHF-IDP-010](hf-10-presented-but-never-issued-token.tradecraft.yaml) | Identity Token Minted Offline From a Stolen Signing Key and Accepted as Genuine | identity-provider | yes-conditional |  |
| [DT-OAIHF-HOST-011](hf-11-overlay-client-memory-only-identity.tradecraft.yaml) | Overlay-Network Client Launched With Memory-Only Identity State and Client Telemetry Suppressed | host-os | yes-conditional |  |
| [DT-OAIHF-K8S-012](hf-12-one-credential-superuser-across-control-planes.tradecraft.yaml) | One Credential Exercising Superuser Group Membership Across Multiple Control Planes | container-orchestrator | yes | 2 partial |
| [DT-OAIHF-AI-013](hf-13-dataset-configuration-ssrf-cloud-metadata.tradecraft.yaml) | Dataset-Configuration SSRF Attempt Toward Cloud Instance Metadata | application-ai-platform | yes |  |
| [DT-OAIHF-K8S-014](hf-14-selfsubjectrulesreview-self-permission-enumeration.tradecraft.yaml) | Control-Plane Self-Authorization Review With Sequence and Context Discriminators | container-orchestrator | yes-conditional | 1 validated, 1 partial |
| [DT-OAIHF-CLOUD-015](hf-15-instance-role-credential-replayed-for-cloud-enumeration.tradecraft.yaml) | Instance-Role Credential Replayed Off-Instance to Enumerate the Cloud Estate | cloud-control-plane | yes-conditional |  |
| [DT-OAIHF-K8S-016](hf-16-aperiodic-never-idle-principal-tempo.tradecraft.yaml) | Aperiodic, Never-Idle Principal Tempo in Control-Plane Requests | container-orchestrator | yes-conditional |  |
| [DT-OAIHF-SCM-017](hf-17-source-control-installation-token-mint-unrecorded.tradecraft.yaml) | Installation Access Token Minted Against a Source-Control App Integration: the Mint Itself Is Recorded in No Audit Event | other | yes-conditional |  |
| [DT-OAIHF-ART-018](hf-18-artifact-repository-as-shared-memory-between-runs.tradecraft.yaml) | Nominally Independent Agent Runs Coordinating Through a Self-Hosted Artifact Repository Under a Credential That Outlives the Run That Created It | self-hosted-artifact-repository-manager | yes-conditional | 2 partial |
| [DT-OAIHF-IDP-019](hf-19-human-class-identity-driven-continuously.tradecraft.yaml) | Human-Class Identity Driven Continuously With No Interactive-Session Record | identity-provider | yes-conditional |  |
| [DT-OAIHF-K8S-020](hf-20-nonexistent-referent-density.tradecraft.yaml) | Single-Use References To Object And Field Names That Never Existed | container-orchestrator | yes-conditional | 1 validated |
| [DT-OAIHF-AI-021](hf-21-containment-escape-record-at-operator-point.tradecraft.yaml) | Containment-Escape State Absent From the Published Kubernetes Audit Event Schema | ai-agent-harness | **no** |  |
| [DT-OAIHF-CLOUD-022](hf-22-container-metadata-request-unrecorded.tradecraft.yaml) | Cloud Instance-Metadata Request From Inside a Container: Recorded in No Cloud Audit Stream at All | cloud-instance-metadata-service | **no** | 1 partial |
| [DT-OAIHF-APP-023](hf-23-in-process-name-resolution-patch.tradecraft.yaml) | In-Process Name-Resolution Patch Producing Connections With No Preceding Resolution | application-runtime | **no** | 1 partial |
| [DT-OAIHF-IDP-024](hf-24-forged-token-has-no-distinguishing-property.tradecraft.yaml) | Forged Identity Token With No Signature-Level Distinguishing Property | identity-provider | **no** |  |
| [DT-OAIHF-AI-025](hf-25-the-planner-is-not-a-recorded-property.tradecraft.yaml) | The Process That Chose a Request Is Not a Recorded Property | ai-agent-harness | **no** |  |
| [DT-OAIHF-ESTATE-026](hf-26-preventive-controls-companion.tradecraft.yaml) | Preventive Controls Companion: This Pack's Seven Controls, Not Behaviors | estate-preventive-control-configuration | yes-conditional |  |

Nineteen objects answer `yes-conditional`, five answer `no`, and two answer a plain `yes`. The
conditional answers are the normal case: the gate is usually an audit tier nobody enabled or an
inventory nobody maintains, and each object names which.

Filenames carry the pack number (`hf-05-…`). `identity.id` carries the human label and is also the
deliberate reference target for `blind_spots[].routes_to`. `identity.uuid` is the stable target for
external references and every other object relation.

Read `manifestation` first, then `detection.strategies[]`. Those two are the payload and the rest of
the file is provenance for them. Fields are defined in [`SCHEMA.md`](../../SCHEMA.md) and inline in
[`../DELTA_TRADECRAFT_TEMPLATE.yaml`](../../DELTA_TRADECRAFT_TEMPLATE.yaml).

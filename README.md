# Delta Tradecraft

**An open format for what a detection engineer needs to know *before* writing a detection.**

A Tradecraft object is one vendor-independent YAML file about one adversary behavior: how the
behavior manifests in the attacked technology, where evidence of it can exist, what that evidence
can prove, and which detection strategies are viable. It sits above rule languages (Sigma, SPL,
KQL) and below or alongside ATT&CK. ATT&CK names a technique. A rule is one query for one
product in one estate. Everything between those two is the actual work, and this repository is
where it goes instead of an engineer's head or a dead wiki page.

**Who it is for.** Detection engineers deciding whether a behavior is worth a rule and what that
rule may honestly claim; agents generating detections that need grounding they cannot invent;
anyone who must say *"we cover that"* and be right.

## The shape of an object

An object has thirteen blocks in this order. The first four say what the behavior is, the middle four
say how the attacked technology and the threat work, and the last five say what you can do
about it.

| Block | Answers |
|---|---|
| `identity` | Which object is this, and what has it been called before |
| `classification` | What kind of behavior, on what attack surface |
| `behavior` | What the adversary does, in one sentence and then in full |
| `manifestation` | Is it **possible**, is it **observable**, is it **detectable**, and under what scope |
| `attack_surface_context` | How the attacked technology works, and where the trust boundary is |
| `threat_research` | Actor, campaign, victim, capability, framework mappings |
| `visibility` | Every telemetry source that could witness it, with what each can and cannot prove |
| `detection` | Strategies, logic, benign twins, blind spots, what an alert may conclude |
| `detector_requirements` | What a detector must be capable of, independent of product |
| `missing_context` | What is unverified, what is assumed, and which assumptions would change a verdict |
| `missing_states` | What to do when a required input is absent |
| `alert_evidence_contract` | What the alert must carry, and what it must not be titled |
| `evidence` | What the source establishes, what it does not, and the references |

An object is complete when it explains six things: what the threat is doing, how it works, how the
behavior is observed, how the attacked technology works, the materially distinct ways it can be
detected, and the detector technologies each of those ways needs.

**[`SCHEMA.md`](SCHEMA.md)** defines every field, its legal values, and common authoring mistakes.

## What this format is for

**A negative is a result.** `detectable: no` is a legitimate, publishable conclusion, and an object
that reaches it carries the evidence for the negative rather than quietly omitting itself. Knowing a
behavior cannot be seen is worth more than a rule that creates the appearance of coverage.

**Each strategy states its minimum evidence and the strongest conclusion that evidence supports.**
`alert_evidence_contract.unsupported_title` names the headline the alert must never carry. A blocked
request must be described as a blocked attempt. It cannot be reported as a successful fetch.

**Attribute each field and each inferred claim in prose.** Every telemetry field records the event
vocabulary, the tier at which it is emitted, and whether a vendor schema attests it or the author
inferred it. That distinction usually decides whether a behavior is detectable at all.

**Omitting `validation` means the strategy was not exercised.** Nothing in the format lets an object
imply it was tested when it was not, and nothing gates publication on a lab result. An unvalidated
strategy is publishable. So is a withdrawn one: `validation.status` carries `validation-withdrawn`
for exactly that. Both have to say which they are. Expect objects in this repository whose strategies
have never been executed anywhere. Every pack states its own validation position at the top of its
README. Read that before you quote coverage from it.

## Start here

**Never seen one before.** Go to [`packs/`](packs/), pick a pack, choose an object by behavior or
attack surface, and open it. If you are starting from a log source, check
`visibility.telemetry_sources[]` inside the object. Read `manifestation` first, its three answers
*possible* / *observable* / *detectable*, then `detection.strategies[]`. Those are the payload and
everything else in the file is the provenance for them. Then come back to the block table above.

**Judging whether the format is serious.** [`CONTRIBUTING.md`](CONTRIBUTING.md) carries the bar an
object clears before it is published. Then open a published negative, an object whose
`manifestation.detectable` answer is *no*, and read what it names as the counterexample that would
change the verdict. A format that cannot record an absence is only recording what its authors set
out to find.

**Hunting one field.** [`SCHEMA.md`](SCHEMA.md), anchor-jump to the block. Use the template for field
placement and authoring prompts, and use `SCHEMA.md` for meanings, types, field presence rules,
legal values, and cross-block rules. Report any disagreement because neither document silently
overrides the other.

**Writing your own.** Copy [`DELTA_TRADECRAFT_TEMPLATE.yaml`](DELTA_TRADECRAFT_TEMPLATE.yaml); every
field carries an `[R]` / `[O]` / `[C]` marker and the reason it exists. Then
[`CONTRIBUTING.md`](CONTRIBUTING.md) when you want to send it back.

## What is deliberately not here

**No indicator lists.** No hashes, IPs or domains. STIX and MISP already do that job.

**No rule content.** An object produces zero, one, or many implementations depending on the estate it
lands in. A query shipped alongside it would imply one right answer.

**No quality score.** Dimensions are reported separately and never averaged into a single number. A
composite hides the weakest input, which is the one that decides whether the detection works.

## License

Content is CC BY 4.0. See [`LICENSE`](LICENSE). Use it, adapt it, ship detections from it. Credit
the source so a reader can check the reasoning.

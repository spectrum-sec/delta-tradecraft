# Delta Tradecraft

Tradecraft is an open format for storing the context needed to create detections in a structured, machine-readable way.

A Tradecraft object is a YAML file about an adversary behavior as it relates to a particular company's environments: how the behavior manifests in the attacked technology, where evidence of it can exist, what that evidence can prove, and which detection strategies are viable.

While LLMs can generate Sigma rules and SPL searches easily, they often stumble or hallucinate when faced with the mountains of context that detecting modern threats requires. A structured format for that context helps improve agentic performance in the field of detection.

## Who it is for

- Detection engineers (and their AI agents) deciding whether a behavior is worth a rule and what exactly that rule can actually cover
- Agents generating detections that need grounding in actual detection practices
- Anyone that must say *"we cover that"* and be right.



## The shape of a Tradecraft object

Tradecraft's schema represents what we found to be the key context needed for crafting effective detections.

The first four blocks say what the behavior is, the middle four say how the attacked technology and the threat work, and the last five say what you can do about it.


| Block                     | Answers                                                                              |
| ------------------------- | ------------------------------------------------------------------------------------ |
| `identity`                | Which object is this, and what has it been called before                             |
| `classification`          | What kind of behavior, on what attack surface                                        |
| `behavior`                | What the adversary does, in one sentence and then in full                            |
| `manifestation`           | Is it **possible**, is it **observable**, is it **detectable**, and under what scope |
| `attack_surface_context`  | How the attacked technology works, and where the trust boundary is                   |
| `threat_research`         | Actor, campaign, victim, capability, framework mappings                              |
| `visibility`              | Every telemetry source that could witness it, with what each can and cannot prove    |
| `detection`               | Strategies, logic, benign twins, blind spots, what an alert may conclude             |
| `detector_requirements`   | What a detector must be capable of, independent of product                           |
| `missing_context`         | What is unverified, what is assumed, and which assumptions would change a verdict    |
| `missing_states`          | What to do when a required input is absent                                           |
| `alert_evidence_contract` | What the alert must carry, and what it must not be titled                            |
| `evidence`                | What the source establishes, what it does not, and the references                    |


An object is complete when it explains six things: what the threat is doing, how it works, how the
behavior is observed, how the attacked technology works, the materially distinct ways it can be
detected, and the detector technologies each of those ways needs.

[`SCHEMA.md`](SCHEMA.md) defines each field, its legal values, and common authoring mistakes.

## Packs

We publish threat analysis for emerging AI threats and release them as Tradecraft objects.

Find threats that matter to you in [`packs/`](packs/).

**Published so far:**

- [**openai-huggingface-2026-07**](packs/openai-huggingface-2026-07/README.md) — model-evaluation
  containment failure leading to ML-platform compromise, July 2026. 26 objects, 72 detection
  strategies. [TIMELINE.md](packs/openai-huggingface-2026-07/TIMELINE.md) is the fastest way in.

## Start here

**If you want to see what an effective use of Tradecraft is,** go to [`packs/`](packs/), pick a pack, choose an object by behavior or attack surface, and open it. 

If you are starting from a log source, check `visibility.telemetry_sources[]` inside the object. Read `manifestation` first, its three answers *possible* / *observable* / *detectable*, then `detection.strategies[]`. Those are the payload and everything else in the file is the provenance for them. Then come back to the block table above.

## Want to contribute?

Read more at [`CONTRIBUTING.md`](CONTRIBUTING.md)

## License

Content is CC BY 4.0. See [`LICENSE`](LICENSE). Use it, adapt it, ship detections from it. Credit
the source so a reader can check the reasoning.

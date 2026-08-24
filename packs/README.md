# Packs

A **pack** is the set of Tradecraft objects produced from one incident or one emerging threat. One
directory per pack, each with its own index and its own scope statement.

Packs are the unit of publication. The format itself is described in
[`../SCHEMA.md`](../SCHEMA.md) and is independent of any pack. Nothing in a pack changes the format,
and nothing in the format assumes a particular pack exists.

## Published

| Pack | Subject | Objects | Strategies | Tested | Index |
|---|---|---|---|---|---|
| `openai-huggingface-2026-07` | Model-evaluation containment failure leading to ML-platform compromise, July 2026 | 26 | 72 | 2 full, 15 partial | [README](openai-huggingface-2026-07/README.md) |
| `ai-agent-adversary-group-1` | AI/agent adversary procedures, catalog Group 1 (prompt injection, MCP/agent supply chain, agent exfiltration, autonomous-agent activity, model-artifact execution, browser/multimodal/A2A boundaries) | 36 | 61 | none | [README](ai-agent-adversary-group-1/README.md) |

**Tested** counts strategies exercised in a lab: fired on a faithful reproduction of the behavior,
and stayed quiet on a benign twin built to differ in exactly the property they key on. Partial means
part of the strategy was established and the object says which part. A lab is not an estate and a
replay is not adversary traffic. The column reports test extent and never makes a coverage claim.
Each pack's README states the scope and outcome of its testing and what remains open.

## What a pack contains

- **One directory**, named for the subject and the month it was first observed.
- **A `README.md`** carrying the incident summary, its **validation state stated before the index**
  rather than left for a reader to reconstruct from YAML, an index of every object with its attack
  surface and detectability verdict, the scope statement for that corpus, and what its
  object numbers mean: what was counted and what was deliberately left out.
- **The objects**, one YAML file each, named `<pack-prefix>-<nn>-<slug>.tradecraft.yaml`.

A pack's own README is the place for anything specific to that subject. Claims about the incident,
the account of what was and was not validated, and the reasoning behind the object boundaries all
belong there rather than in the format documentation at the repository root. This index carries only
the headline counts, so that a reader comparing packs does not have to open each one to find out
whether anything in it was ever tested.

## Adding one

See [`../CONTRIBUTING.md`](../CONTRIBUTING.md). In short: open an issue naming the subject and the
behaviors you intend to model before writing the objects, so the boundaries can be argued once
rather than after twenty files exist.

# Contributing

One object per pull request. Copy `DELTA_TRADECRAFT_TEMPLATE.yaml` from the repository root. Every
field carries its own `[R]` / `[O]` / `[C]` marker and the reason it exists. `SCHEMA.md` defines
field meanings, types, field presence rules, legal values, and cross-block rules. A disagreement
between them is a format defect and must be resolved before submission.

## Submit

1. Open an issue naming the behavior and the incident if you want the boundary sanity-checked
   before writing a thousand-line object. Optional, and much cheaper than a rejected PR. Published
   objects run from about 1,100 to 2,100 lines.
2. Add the file to the pack directory as `<pack>-<NN>-<slug>.tradecraft.yaml`.
3. Add your row to that directory's `README.md` index. If you rename or renumber an object, the
   index and the object must agree in the same commit.
4. CI must be green: [`.github/workflows/validate.yml`](.github/workflows/validate.yml). It checks
   structure only. Everything below is what review checks.

## The publication bar

- **All 13 blocks present and filled.** No leftover `<placeholder>` text.
- **`identity.uuid` is a fresh UUID v4** and never reused. It is what external references and
  relations point at. `identity.id` is a human label, and the one in-corpus exception: `routes_to` is
  spelled with it, deliberately, so a reviewer can read the pointer. If you renumber or rename it,
  the retired label moves to `aliases`, which holds ids and titles alike and stays there, and every
  `routes_to` pointing at the old label is updated in the same commit. CI resolves them all.
- **`manifestation` answers all three gates** and each `summary` says *why*. `no` is a publishable
  answer, and published objects do reach it.
- **Any answer that is not a plain `yes` carries `answer_scope` and `blocking_conditions`.** Pick the
  right family: collection conditions mean an estate could fix it; structural conditions mean no
  audit tier and no enablement switch would ever produce the record. Do not file a dead end as a
  collection gap, or vice versa.
- **At least one `detection.strategies[]` entry even when `detectable: no`.** It then covers the
  consequence, reformulates the question, or routes to a sibling object, and says which.
- **At least one `visibility.telemetry_sources[]` entry, each with `limitations`.** A telemetry
  source with no stated limitation is unfinished.
- **`benign_explanations` and `blind_spots` are lists.** Set `overlaps_incident_instance` honestly:
  `true` means tuning that pattern out would suppress the incident's own record. "No false
  positives" is not a finding.
- **`forbidden_exclusions` carries every do-not-tune-on-this instruction.** Not `detection_logic`.
  An implementer will not find it there.
- **Omit `validation` entirely if the strategy has never been exercised.** An absent block means
  untested. Never assert a validation state you do not have. Expected volume is a sibling field, not
  part of the validation block, so a strategy with only a noise figure needs no validation block at
  all. Validation never goes back into `safe_conclusion` either.
- **Use an enum member, or `other` plus a note.** Never coin a near-duplicate of an existing member.
- **At least three `evidence.references[]`.** `confirmed_incident_behavior` carries what the primary
  source establishes, and may carry your reading of it only where you sign that reading in place, as
  the pack's and not the source's. Unsigned inference is the violation. A reading that changes a
  verdict is also booked in `missing_context.assumptions[]`, and `unsupported_conclusions` is never
  empty.
- **No atomic indicators.** Domains, hashes and addresses belong in your SIEM, not in an object.

## Two objects for the same behavior

Never average them, and never edit a contributor's reasoning so it matches yours.

- **Same behavior, same boundary**: merge. The survivor names what it took from the other.
- **Same activity, different boundary**: both ship. Each states the boundary in
  `evidence.procedure_boundary` and names the sibling. This is the common case.
- **Incompatible factual claims**: both ship. Each records the other's reading in
  `missing_context.unverified` with the reference supporting its own. A maintainer decides
  publication, never truth.

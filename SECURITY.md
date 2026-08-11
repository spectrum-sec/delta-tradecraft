# Security

This repository publishes **detection reasoning**. It contains no code, no exploits, no
proof-of-concept, no credentials, and no atomic indicators, so the usual vulnerability path does not
apply. Two things here can still go wrong in a way that should not be discussed in public first.

| Report this | How |
|:--|:--|
| A published object leaks something it should not: a real credential, an internal hostname, a customer-identifying value, an estate-specific detail | **Privately**, to the contact below. Treat it as an incident, not a typo |
| A published object describes a live, unpatched weakness in a named vendor's product in more operational detail than that vendor has itself disclosed | **Privately**, same contact |
| A factual error: a wrong field, a wrong audit tier, a miscited document, logic that cannot fire | **Publicly**, as an issue or a PR. This is normal work |
| Disagreement with a `detectable` verdict, an outcome boundary, or an exclusion | **Publicly**, as an issue. Bring the source that refutes it |

**Contact: use GitHub's private vulnerability reporting on this repository.** Go to the Security tab
and choose *Report a vulnerability*. The report is visible only to maintainers, it carries a thread
you can attach detail to, and it does not put the finding in a public issue while it is being fixed.

Maintainers: private reporting has to be switched on per repository, under Settings, Advanced
Security. If the Security tab offers no reporting option, it is off, and this file is currently
promising a channel that does not exist.

If you would rather not use GitHub, open a public issue saying only that you have a private report
and asking for a contact. Do not put the detail in it.

## What will not be accepted

- **Atomic indicators, live credentials, internal hostnames.** Objects describe behavior and
  telemetry, never artifacts. This is a standing prohibition.
- **Working exploit code or a weaponized reproduction.** An object needs the *observable consequence*
  of a technique. It never needs the exploit.
- **Customer or estate-derived data.** Anything from a specific environment (a real principal name,
  namespace, address, or inventory) is out. Environment-supplied context is described as a
  *requirement*, never as a sample.

## Responsible use

An object states what an estate must already be collecting before a detection means anything, and
which missing states forbid claiming coverage. Publishing coverage for a behavior whose
preconditions are unmet is the failure this format exists to prevent. It is not a vulnerability, and
it is still a misuse of this content.

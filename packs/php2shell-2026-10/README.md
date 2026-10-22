# PHP2Shell pack

Two unauthenticated RCE flaws in the PHP engine, patched 21 Oct 2026.

- **CVE-2026-109618:** an out-of-memory error during request startup corrupts request memory ([GHSA-w8hw-w9fx-j79w](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w)).
- **CVE-2026-109619:** stale global state left by a fatal error is reused by a later request on the same worker ([GHSA-6wmf-4rv5-p3rp](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp)).
- **Impact:** unauthenticated remote code execution inside the PHP worker process.
- **Fixed in:** 8.5.12, 8.4.27, 8.3.36 and 8.2.35. Restart workers after patching.

**11 objects, 24 detection strategies.** Built from public research only. Nothing was lab-tested, and no atomic indicators appear in the objects.

## Status (21 Oct 2026)

- **Lab RCE demonstrated:** by [Wiz](https://www.wiz.io/blog/php2shell-php-rce), and end to end on Apache mod_php by [CrowdStrike](https://www.crowdstrike.com/en-us/blog/crowdstrike-protects-customers-from-php-rce-zero-days/). The [GHSA-6wmf-4rv5-p3rp](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp) advisory validates the CVE-2026-109619 session path on both mod_php and PHP-FPM.
- **Attempts reported:**
  - [Cloudflare](https://blog.cloudflare.com/php-vulnerabilities-waf-protection/) and [watchTowr](https://watchtowr.com/intelligence/php-remote-code-execution-php2shell/).
  - [Patchstack](https://patchstack.com/articles/php-major-vulnerability-alert-10-2026/): one entry relays active incidents and a later one describes only probing.
  - [Kaspersky](https://www.kaspersky.com/blog/cve-2026-109618-109619-php-vulnerabilities/56583/): attempts before the patch.
  - [Hadrian](https://hadrian.io/vulnerability-alerts/php2shell-leaked-critical-php-rce-cve-2026-109618-cve-2026-109619): "at scale", with no counts.
  - [VulnCheck](https://www.vulncheck.com/blog/php2shell-vulnerabilities): canary scans plus at least one session-path webshell attempt (17:22–17:24 UTC), where all 10 follow-up reads returned 404.
- **No source documents a confirmed compromise.** Hadrian and Patchstack describe exploitation without confirming one.
  - [CISA](https://www.cisa.gov/news-events/alerts/2026/10/21/critical-vulnerabilities-php-require-immediate-patching) "has not observed exploitation".
  - [ReliaQuest](https://reliaquest.com/blog/threat-spotlight-php2shell-responding-to-critical-php-vulnerabilities/) has seen no successful exploitation in customer environments.
  - [Talos](https://blog.talosintelligence.com/two-critical-rces-in-php-core/) "has not seen them being exploited in the wild yet" as of 21 Oct 14:00 UTC; [Imperva](https://www.imperva.com/blog/php2shell-vulnerability-updates/) observed no matching traffic in its pre-disclosure monitoring.
  - [Tanium](https://www.tanium.com/blog/php2shell-responding-to-cve-2026-109618-and-cve-2026-109619) reported on 21 Oct that neither CVE was in the [CISA KEV catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog).
- **Exploit reliability:**
  - Red Hat rates CVE-2026-109619 [CVSS 3.1 8.1](https://access.redhat.com/security/cve/cve-2026-109619) for high attack complexity; Red Hat's page lists NVD's score as 9.8. The PHP advisories rate both CVEs critical (CVSS v4 9.2).
  - VulnCheck says none of the public PoCs it reviewed independently bypasses ASLR on x86-64, and that exploitation works on ARM64 and on x86 without ASLR.
- **Expiry:** this status expires 2026-10-28.

## Scope

- **Modeled:** what web server, proxy, WAF, PHP error, endpoint and kernel-audit logs can record.
- **Generic post-exploitation:** objects 001–006 and 010 model generic PHP code execution, because no source documents PHP2Shell post-exploitation. Don't report them as PHP2Shell-specific coverage.
- **Not covered:** in-process execution that writes nothing, opens no socket and starts no child process.
- **Request shapes:** described by category only. There are no exploit internals.
- **CVE labels:** some reports, including Wiz, put the upload-progress path under CVE-2026-109618. The PHP advisories place it under CVE-2026-109619, and this pack follows the advisories.

## Objects

`Detectable` is `manifestation.detectable.answer`. `yes-conditional` means the object's `blocking_conditions` must be met.

| Id | Behavior | Detectable |
|:--|:--|:--|
| [DT-P2S-APP-007](php2shell-07-failed-attempt-cluster-multipart-posts-then-worker-crash.tradecraft.yaml) | Repeated multipart POSTs from one client, each followed by a worker crash or OOM | yes-conditional |
| [DT-P2S-APP-008](php2shell-08-request-body-structure-anomaly-toward-a-php-endpoint.tradecraft.yaml) | Request-body structure anomaly toward a PHP endpoint | yes-conditional |
| [DT-P2S-APP-009](php2shell-09-oversized-phpsessid-set-cookie-memory-leak.tradecraft.yaml) | Oversized PHPSESSID cookie leaking worker memory | yes-conditional |
| [DT-P2S-APP-001](php2shell-01-php-worker-spawns-unusual-child-process.tradecraft.yaml) | PHP worker spawns an unusual child process | yes-conditional |
| [DT-P2S-HOST-002](php2shell-02-web-user-writes-executable-script-into-served-tree.tradecraft.yaml) | Web user writes an executable script into the served tree | yes-conditional |
| [DT-P2S-HOST-003](php2shell-03-web-user-writes-php-config-override-in-served-tree.tradecraft.yaml) | Web user writes a PHP configuration override | yes-conditional |
| [DT-P2S-APP-004](php2shell-04-worker-or-children-connect-out-to-new-destination.tradecraft.yaml) | PHP worker connects out to a new destination | yes-conditional |
| [DT-P2S-HOST-005](php2shell-05-scheduler-persistence-by-the-web-user.tradecraft.yaml) | Web user installs scheduler persistence | yes-conditional |
| [DT-P2S-HOST-006](php2shell-06-account-or-authorized-keys-change-by-the-web-user.tradecraft.yaml) | Web user adds an account, group member or SSH key | yes-conditional |
| [DT-P2S-APP-010](php2shell-10-php-host-secrets-reused-from-another-source.tradecraft.yaml) | PHP host secrets used from somewhere else | yes-conditional |
| [DT-P2S-ESTATE-011](php2shell-11-php-engine-unpatched-or-workers-not-restarted.tradecraft.yaml) | Vulnerable PHP build, or patched but workers not restarted (control) | yes-conditional |

To use an object, read `manifestation`, then `detection.strategies[]`. Fields are defined in [`SCHEMA.md`](../../SCHEMA.md).

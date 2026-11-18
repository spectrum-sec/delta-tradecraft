# Java4Shell pack

An out-of-bounds memory flaw in the OpenJDK HotSpot runtime on x86-64 hosts with AVX-512 (CVE-2026-114938), published 17 Nov 2026 ([CVE record](https://cveawg.mitre.org/api/cve/CVE-2026-114938)). The discoverer places it in the AVX-512 Base64 decode path used by `java.util.Base64` ([Calif](https://calif.io/java4shell)).

**3 objects, 16 detection strategies (5, 5 and 6 by object).** Built from public research plus telemetry checks in our own lab. No atomic indicators appear in the objects, and there is no request-level object because no primary source describes a request shape a defender could match.

## Status (2026-11-18, expires 2026-11-20)

CVE-2026-114938 was published on 2026-11-17 ([CVE record](https://cveawg.mitre.org/api/cve/CVE-2026-114938)). Scores disagree and are recorded here once, not picked: Oracle as CNA gives CVSS 3.1 8.1 ([CVE record](https://cveawg.mitre.org/api/cve/CVE-2026-114938)), the discoverer Calif CVSS 4.0 9.2 ([Calif](https://calif.io/java4shell)), Red Hat a preliminary 9.8 ([Red Hat](https://access.redhat.com/security/cve/CVE-2026-114938)) and Amazon CVSS 3.1 8.8 ([ALAS](https://explore.alas.aws.amazon.com/CVE-2026-114938.html)). CISA's alert calls it critical ([CISA](https://www.cisa.gov/news-events/alerts/2026/11/17/critical-java-vulnerability-requires-immediate-patching)). It is not in CISA KEV as of the catalog released 2026-11-17 09:53 PT, checked 2026-11-18 05:30 PT ([KEV](https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json)). No in-the-wild exploitation is reported as of 2026-11-18 ~06:00 PT: the Canadian Centre for Cyber Security and JPCERT/CC report none and CISA's ADP entry rates Exploitation "none" ([CCCS](https://www.cyber.gc.ca/en/alerts-advisories/al26-029-critical-vulnerability-impacting-oracle-java-cve-2026-114938), [JPCERT/CC](https://www.jpcert.or.jp/newsflash/2026111801.html), [CVE record](https://cveawg.mitre.org/api/cve/CVE-2026-114938)). A public proof of concept exists in the discoverer's write-up ([Calif](https://calif.io/java4shell)). Calif and Wiz state code execution against named products without publishing their chains ([Calif](https://calif.io/java4shell), [Wiz](https://wiz.io/blog/cve-2026-114938-rce-java4shell-openjdk)). Other labs report crashes, memory corruption and a memory disclosure ([The Hacker News](https://thehackernews.com/2026/11/java4shell-cve-2026-114938-enables-pre.html), [Blackpoint](https://blackpointcyber.com/blog/java4shell-cve-2026-114938-java-base64-decoder-flaw/)) and one lab states code execution in its own lab ([Hadrian](https://hadrian.io/vulnerability-alerts/oracle-critical-security-patch-update-java-17-rce-claims)). NVD showed the CVE as "Undergoing Analysis" with no NVD score of its own (only Oracle's 8.1), read 2026-11-18 06:22 PT ([NVD](https://services.nvd.nist.gov/rest/json/cves/2.0?cveId=CVE-2026-114938)).

Before the expiry date re-check KEV and exploitation reports, plus the pending vendors listed under Fixed builds.

## Scope

CVE-2026-114938 only. Oracle's November update fixes 11 Java SE flaws and its other fixes are out of scope ([Oracle](https://www.oracle.com/security-alerts/cspunov2026.html)). A build is affected when all of these hold:

- HotSpot on an x86-64 JVM ([OpenJDK advisory](https://openjdk.org/groups/vulnerability/advisories/2026-11-17)).
- Branch 17 from 17.0.6, 21, 25 or 27, or the Oracle Java SE 8 Performance Pack below 8u513-perf, built below the vendor's fix ([OpenJDK advisory](https://openjdk.org/groups/vulnerability/advisories/2026-11-17), [Red Hat](https://access.redhat.com/security/vulnerabilities/RHSB-2026-018), [KISA](https://www.boho.or.kr/kr/bbs/view.do?bbsId=B0000133&pageIndex=1&nttId=72313&menuNo=205020), [CERT Polska](https://moje.cert.pl/komunikaty/2026/216/krytyczna-podatnosc-w-srodowisku-java-umozliwiajaca-zdalne-wykonanie-kodu-java4shell/)).
- The host CPU has avx512f, avx512bw, avx512vbmi and bmi2, as `/proc/cpuinfo` spells them or hypervisor and cloud inventory lists them ([CERT Polska](https://moje.cert.pl/komunikaty/2026/216/krytyczna-podatnosc-w-srodowisku-java-umozliwiajaca-zdalne-wykonanie-kodu-java4shell/), [Sysdig](https://www.sysdig.com/blog/java4shell-cve-2026-114938-pre-auth-rce-in-openjdk)). HotSpot spells the third feature `avx512_vbmi` in the JVM's own CPU line and drops every AVX-512 token when effective `UseAVX` is below 3, so host flags present with a JVM line lacking AVX-512 point to `mitigated-not-patched` and never to `not affected` ([OpenJDK source](https://github.com/openjdk/jdk/blob/master/src/hotspot/cpu/x86/vm_version_x86.cpp)).
- Effective `UseAVX` is 3 or more and `UseBASE64Intrinsics` is on, read with `jcmd <pid> VM.flags -all` on the live process ([Oracle jcmd](https://docs.oracle.com/en/java/javase/17/docs/specs/man/jcmd.html)). A launch script or `JAVA_TOOL_OPTIONS` is not proof ([SonarSource](https://community.sonarsource.com/t/security-advisory-java4shell-workaround-for-sonarqube/191330), [Keycloak](https://www.keycloak.org/2026/11/java4shell-vulnerability-cve-2026-114938)).

Standard Java 8, Java 11 and ARM64 are not affected ([OpenJDK advisory](https://openjdk.org/groups/vulnerability/advisories/2026-11-17), [Adoptium](https://adoptium.net/news/2026/11/temurin-november-2026-cspu-java4shell)). Non-LTS lines 18 to 20, 22 to 24 and 26 stay `unknown`, and Amazon's "No Fix Planned" for them does not make them affected ([ALAS](https://explore.alas.aws.amazon.com/CVE-2026-114938.html)).

## Fixed builds

Normalise each version to four numeric parts (feature, interim, update, patch) within one vendor's scheme, with a missing part read as 0 (17.0.21+9 is 17.0.21.0) and every build or packaging suffix stripped, such as `+N`, `_pN-rN`, a Corretto fifth part or a Debian revision. Oracle's Java 8 Performance Pack (`-perf`) is its own stream with 8u511b33-perf affected and 8u513-perf fixed, and standard Java 8 is not affected ([Sysdig](https://www.sysdig.com/blog/java4shell-cve-2026-114938-pre-auth-rce-in-openjdk), [KISA](https://www.boho.or.kr/kr/bbs/view.do?bbsId=B0000133&pageIndex=1&nttId=72313&menuNo=205020)). Object 001 holds the full table.

- **Fixed:** Oracle JDK and the OpenJDK upstream tags at 17.0.21.1, 21.0.13.1, 25.0.5.1 and 27.0.1.1, and the Performance Pack at 8u513-perf ([Oracle 17](https://www.oracle.com/java/technologies/javase/17-0-21-1-relnotes.html), [21](https://www.oracle.com/java/technologies/javase/21-0-13-1-relnotes.html), [25](https://www.oracle.com/java/technologies/javase/25-0-5-1-relnotes.html), [27](https://www.oracle.com/java/technologies/javase/27-0-1-1-relnotes.html), [KISA](https://www.boho.or.kr/kr/bbs/view.do?bbsId=B0000133&pageIndex=1&nttId=72313&menuNo=205020)). Oracle's notes do not name the CVE.
- **Also fixed:** Amazon Corretto and Amazon Linux ([Corretto 17](https://raw.githubusercontent.com/corretto/corretto-17/develop/CHANGELOG.md), [ALAS](https://explore.alas.aws.amazon.com/CVE-2026-114938.html)), Azul Zulu, where a vendor-stated fix wins over the four-part rule because its CPU stream is fixed at 17.69.204, 21.53.202 and 25.37.202 ([Azul release notes](https://docs.azul.com/core/release-notes), [Azul CVE](https://docs.azul.com/core/cve)), Alpine ([Alpine](https://secdb.alpinelinux.org/v3.23/community.json)), Chainguard packages ([Chainguard](https://www.chainguard.dev/unchained/java4shell-how-a-malformed-base64-string-breaks-the-jvm-on-avx-512)), Oracle GraalVM per KISA ([KISA](https://www.boho.or.kr/kr/bbs/view.do?bbsId=B0000133&pageIndex=1&nttId=72313&menuNo=205020)), Temurin 27 and Debian sid ([Adoptium](https://adoptium.net/news/2026/11/temurin-november-2026-cspu-java4shell), [Debian](https://security-tracker.debian.org/tracker/CVE-2026-114938)).
- **Announced, not published:** Temurin 17, 21 and 25 ([Adoptium](https://adoptium.net/news/2026/11/temurin-november-2026-cspu-java4shell)).
- **Released but unmapped:** SapMachine, whose release notes name no CVE ([SapMachine](https://github.com/SAP/SapMachine/releases)).
- **Pending:** Microsoft Build of OpenJDK ([Microsoft](https://learn.microsoft.com/en-us/java/openjdk/release-notes)), BellSoft Liberica ([Liberica](https://github.com/bell-sw/Liberica/releases)), Red Hat ([Red Hat](https://access.redhat.com/security/cve/CVE-2026-114938)), Ubuntu ([Ubuntu](https://ubuntu.com/security/CVE-2026-114938)), SUSE ([SUSE](https://www.suse.com/security/cve/CVE-2026-114938/)) and Debian stable ([Debian](https://security-tracker.debian.org/tracker/CVE-2026-114938)). Runtimes from a pending vendor stay below the fix.

## Verdict words

Object 001 gives each JVM exactly one of four verdicts and objects 002 and 003 join on the same words.

- `affected`: every condition above holds on the live process.
- `mitigated-not-patched`: the build and CPU conditions hold and `UseAVX` is below 3 or `UseBASE64Intrinsics` is off on the live process, whoever set the flag. It stays on the patch list, and CERT-EU and CCCS recommend the flags as a workaround ([CERT-EU](https://cert.europa.eu/publications/security-advisories/2026-021/), [CCCS](https://www.cyber.gc.ca/en/alerts-advisories/al26-029-critical-vulnerability-impacting-oracle-java-cve-2026-114938)).
- `not affected`: the live build is at or above the vendor fix, or the runtime is out of scope (Java 8, Java 11, ARM64 or not HotSpot). The reason is recorded inside the verdict.
- `unknown`: a condition could not be read. Unknown is never reported as affected.

"Exposed" means network reachability only ([Red Hat](https://access.redhat.com/security/vulnerabilities/RHSB-2026-018)). Reachability ranks the work and never clears a JVM.

## Objects

| Id | File | Behavior | Kind | Strategies |
|:--|:--|:--|:--|:--|
| DT-J4SH-ESTATE-001 | [file](java4shell-01-exposed-jvm-below-fixed-build.tradecraft.yaml) | JVM on an affected HotSpot x86-64 build below its vendor fix with the AVX-512 Base64 path active (control) | Specific | 5 |
| DT-J4SH-HOST-002 | [file](java4shell-02-network-jvm-fatal-error-and-restart.tradecraft.yaml) | Network-exposed JVM dies on a fatal error and restarts; a possible attempt only when joined to an `affected` verdict ([CISA](https://www.cisa.gov/news-events/alerts/2026/11/17/critical-java-vulnerability-requires-immediate-patching), [Sysdig](https://www.sysdig.com/blog/java4shell-cve-2026-114938-pre-auth-rce-in-openjdk)) | Generic | 5 |
| DT-J4SH-HOST-003 | [file](java4shell-03-java-server-unexpected-child-process.tradecraft.yaml) | Java server starts a child process it does not normally start ([Sysdig](https://www.sysdig.com/blog/java4shell-cve-2026-114938-pre-auth-rce-in-openjdk)) | Generic | 6 |

Generic objects also cover this threat and must not be reported as Java4Shell coverage. Successful exploitation may leave no traces in logs, so a missing crash is no clearance ([CERT Polska](https://moje.cert.pl/komunikaty/2026/216/krytyczna-podatnosc-w-srodowisku-java-umozliwiajaca-zdalne-wykonanie-kodu-java4shell/)). A crash is at most an attempt and a child process is not proof of compromise. Fields are defined in [`SCHEMA.md`](../../SCHEMA.md).

## How we checked the telemetry

We checked JVM telemetry in a telemetry-only lab we own: ordinary JVM runs, flag dumps and benign crashes on Java 8, 11, 17 and 21 without ever triggering the vulnerability, and those checks settled these points:

- **Only the version string separates builds.** Flag output was identical on 17.0.5, 17.0.6 and 17.0.21 and Java 8 has no Base64 intrinsic flag ([OpenJDK flags](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/runtime/globals.hpp)).
- **Not every fatal error is a SIGSEGV.** The fatal-error banner goes to stdout and the exit code was 134 under an init or shell parent. The crash log lands in the working directory or the temp directory unless `-XX:ErrorFile` is set, so it is lost with an ephemeral container ([Oracle](https://docs.oracle.com/en/java/javase/17/troubleshoot/fatal-error-log.html)).
- **`jspawnhelper` runs first in the child's own process** on Linux JDK 17, so the target's parent is still `java` as recorded on native arm64 Linux ([OpenJDK](https://raw.githubusercontent.com/openjdk/jdk17u/master/src/java.base/unix/classes/java/lang/ProcessImpl.java)).

## Open gaps

- Windows crash behavior and process lineage were not lab-tested.
- The exit code of a JVM running as PID 1 was not measured.
- Real x86 AVX-512 flag values were not measured because x86 images ran under emulation, so the CPU and flag strategies in object 001 have no validation block.
- Pending vendors above and the SapMachine CVE mapping need a re-check at expiry.
- No strategy query was run against a SIEM.

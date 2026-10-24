# Log4Shell Security Notes

## What it is
Log4Shell is a critical remote-code-execution vulnerability in **Apache Log4j 2**. It was discovered by Chen Zhaojun of the Alibaba Cloud Security Team and exploited in the wild immediately after disclosure in December 2021. An attacker who can control log messages (or log message parameters) can trick Log4j into fetching and executing arbitrary code from an attacker-controlled LDAP/JNDI endpoint — no authentication required. It is on CISA's Known Exploited Vulnerabilities Catalog (added 2021-12-10).

## CVE
**CVE-2021-44228**

## Affected versions (log4j-core only)
- **2.0-beta9 through 2.15.0** (i.e., all versions from 2.0-beta9 up to and including 2.15.0)
- Affected versions per NVD: `2.0-beta9` through `2.15.0` (excluding security releases 2.12.2, 2.12.3, 2.3.1)
- Not affected: log4net, log4cxx, and other Apache Logging Services projects
- Not affected: applications using only `log4j-api` without `log4j-core`
- log4j 1.x is EOL (since 2015) and unreachable for post-2015 vulnerabilities; no lookups support.

## Fixed version
- **2.15.0** (fixed 2.15.0 for Java 8+)
- For Java 7: **2.12.2**; for Java 6: **2.3.1** (per CVE-2021-44228 fixed versions)
- **Important sequencing note:** the 2.15.0 fix was incomplete for some non-default configurations (see related CVE-2021-45046, CVSS 9.0 CRITICAL). 2.16.0 removed JNDI lookup support entirely, but that release itself was still vulnerable to the follow-up exploit. **Sonatype/CISA guidance: upgrade to 2.17.1 (Java 8+), 2.12.4 (Java 7), or 2.3.2 (Java 6)** to fully remove the exploit path.

## Severity
- **CVSS 3.1 base score: 10.0 CRITICAL**
- Vector: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H`
- NVD / CISA both rate it 10.0 CRITICAL.
- Any unpatched system exposed to network traffic is at risk.

## Remediation summary
1. Upgrade log4j-core to a non-vulnerable version (2.17.1 / 2.12.4 / 2.3.2 per Java runtime).
2. Detect vulnerable deployments (JAR scanning, Maven/Gradle dependency trees, nested JARs).
3. If upgrade is not immediate: remove the `JndiLookup` class from the classpath (`zip -q -d log4j-core-*.jar org/apache/logging/log4j/core/lookup/JndiLookup.class`) or set `log4j2.formatMsgNoLookups=true` (temporary only).
4. Set up automated dependency scanning in CI across the org.

## Sources
- NVD — CVE-2021-44228 (CVSS 10.0 CRITICAL): https://nvd.nist.gov/vuln/detail/CVE-2021-44228
- Apache Logging Services — Security page (CVE-2021-44228 / CVE-2021-45046): https://logging.apache.org/security.html
- Apache Software Foundation — Apache Log4j CVEs (ASF blog, Dec 2021): https://news.apache.org/foundation/entry/apache-log4j-cves
- CISA — Recommended Mitigation Measures for Apache Log4j: https://www.cisa.gov/uscert/known-exploited-vulnerabilities-catalog
- IBM — Log4J Vulnerability Detection and Patching (detection/hunting guidance): https://www.ibm.com/think/topics/log4j-vulnerability-detection-patching
- Sonatype — Find and Fix Log4j (upgrade path and class-removal workaround): https://help.sonatype.com/en/find-and-fix-log4j.html
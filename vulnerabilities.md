# Vulnerability Tracking

**Last Updated:** YYYY-MM-DD
**Review Cadence:** Ongoing (weekly recommended)
**Maintained By:** [Team/Individual Name]

> **⚠️ SENSITIVE DATA WARNING**
> This file tracks vulnerabilities in your environment. Some organizations classify this as highly sensitive. Consider whether this belongs in your repository or in a separate secure system.

---

## Purpose

This file tracks vulnerabilities relevant to your environment that warrant proactive threat hunting. It bridges vulnerability management and threat hunting by identifying:
- **CVEs affecting your tech stack** (cross-reference with [environment.md](environment.md))
- **Exploits available in the wild** (public PoCs, active exploitation)
- **Hunt opportunities** (vulnerabilities that threat actors might exploit before patches are applied)

**How this file supports hunting:**
- **Level 0-1 (Manual):** Review CVE alerts, manually add relevant ones, plan hunts based on exposure
- **Level 2 (Memory):** Grep this file + past runs to avoid duplicate hunts, track what's been investigated
- **Level 3+ (Automated):** Agent monitors CVE feeds, cross-references environment.md, auto-suggests hunts when: `CVE + tech match + exploit exists`

---

## Active Vulnerabilities

### CVE-YYYY-XXXXX: [Vulnerability Title]

**Status:** `🔴 Open` | `🟡 Monitoring` | `🟢 Remediated` | `🔵 Hunt Completed`

**Severity:** Critical | High | Medium | Low
**CVSS Score:** X.X
**Published Date:** YYYY-MM-DD
**Last Reviewed:** YYYY-MM-DD

**Affected Technology:**
- Product: [e.g., Log4j, OpenSSL, Spring Framework]
- Version(s): [Specific versions affected]
- **Our Environment:** [What systems/services use this? Reference environment.md]
  - Example: "PostgreSQL 14.2 on prod-db-01, prod-db-02 (see environment.md - Databases section)"

**Vulnerability Description:**
[Brief description of what the vulnerability is - RCE, privilege escalation, info disclosure, etc.]

**Exploit Availability:**
- ⚠️ **Public Exploit:** Yes | No
- 🔗 **Exploit Links:** [Links to PoC code, exploit-db, GitHub repos]
- 🌐 **Active Exploitation:** [CISA KEV listed? In-the-wild attacks reported?]
- 📊 **Exploit Maturity:** [Proof-of-concept | Functional | High (weaponized)]

**Hunt Opportunity:**
[Why this warrants proactive hunting vs just patching]
- Example: "Patch deployment delayed 2 weeks. Hunt for exploit attempts in web logs before patch window."
- Example: "Zero-day actively exploited. Look for IoCs retroactively."

**Recommended Hunt Actions:**
- [ ] Search web server logs for exploit patterns
- [ ] Check EDR for suspicious process execution patterns
- [ ] Review network logs for callback indicators
- [ ] Scan for vulnerable versions in asset inventory
- [ ] Check authentication logs for privilege escalation attempts

**Hunt References:**
- Related hunts: [Link to run notes if hunt was performed - e.g., runs/2024-01-15-log4j-rce-hunt.md]
- Hunt status: Not started | Planned | In progress | Completed

**Remediation Status:**
- **Patch Available:** Yes | No
- **Patch Version:** [Version that fixes the vulnerability]
- **Remediation Plan:** [Link to change ticket, deployment schedule]
- **Workarounds:** [If patch not available, what mitigations are in place?]

**References:**
- NVD: [https://nvd.nist.gov/vuln/detail/CVE-YYYY-XXXXX]
- Vendor Advisory: [Link to vendor security bulletin]
- CISA KEV: [If listed: https://www.cisa.gov/known-exploited-vulnerabilities-catalog]
- Additional: [Blog posts, technical analysis, IoC lists]

**Notes:**
- YYYY-MM-DD: Initial discovery, added to tracking
- YYYY-MM-DD: Confirmed affected systems in production
- YYYY-MM-DD: Hunt completed, no indicators found (see runs/YYYY-MM-DD-cve-hunt.md)
- YYYY-MM-DD: Patch applied to prod-db-01, prod-db-02
- YYYY-MM-DD: Marked remediated, moving to resolved section

---

### CVE-2024-EXAMPLE: [Another Example Entry]

**Status:** `🟡 Monitoring`

**Severity:** High
**CVSS Score:** 8.1
**Published Date:** 2024-03-15
**Last Reviewed:** 2024-03-20

**Affected Technology:**
- Product: Nginx
- Version(s): 1.20.0 - 1.22.1
- **Our Environment:** Used in web tier (see environment.md - Infrastructure section)
  - Confirmed running 1.21.6 on web-proxy-01 through web-proxy-05

**Vulnerability Description:**
HTTP request smuggling vulnerability allowing bypass of security controls.

**Exploit Availability:**
- ⚠️ **Public Exploit:** Yes (PoC published on GitHub)
- 🔗 **Exploit Links:** https://github.com/example/nginx-smuggling-poc
- 🌐 **Active Exploitation:** Not reported in CISA KEV, no widespread exploitation yet
- 📊 **Exploit Maturity:** Proof-of-concept (requires specific configuration)

**Hunt Opportunity:**
Medium priority - exploit requires specific conditions but we have public-facing Nginx. Hunt proactively for smuggling attempts.

**Recommended Hunt Actions:**
- [ ] Analyze HTTP access logs for smuggling patterns (CL/TE, TE/CL mismatches)
- [ ] Check WAF logs for bypass attempts
- [ ] Review Nginx configuration for vulnerable settings

**Hunt References:**
- Related hunts: Planned for 2024-03-25 sprint

**Remediation Status:**
- **Patch Available:** Yes
- **Patch Version:** Nginx 1.23.0+
- **Remediation Plan:** JIRA-SEC-1234 - Scheduled for 2024-04-01 maintenance window
- **Workarounds:** WAF rules deployed 2024-03-18 to detect smuggling patterns

**References:**
- NVD: https://nvd.nist.gov/vuln/detail/CVE-2024-EXAMPLE
- Nginx Advisory: https://nginx.org/en/security_advisories.html
- Analysis: https://portswigger.net/research/http-request-smuggling

**Notes:**
- 2024-03-15: CVE published
- 2024-03-16: Confirmed affected versions in environment
- 2024-03-18: Deployed WAF detection rules as interim mitigation
- 2024-03-20: Added to hunt queue

---

## Resolved / Historical Vulnerabilities

> **Moved here after:**
> - Patches fully deployed across environment
> - Hunt completed (if applicable)
> - No longer poses active risk

Use this section as a knowledge base for:
- Past hunt patterns that might apply to future similar CVEs
- Lessons learned from vulnerability response
- Reference for grep when new CVEs emerge ("Did we see this type of vuln before?")

### CVE-2023-XXXXX: [Old Vulnerability Title]

**Status:** `🟢 Remediated` | **Hunt Status:** `🔵 Completed`

**Resolution Date:** YYYY-MM-DD

**Summary:**
- Vulnerability: [Brief description]
- Affected: [What was affected in our environment]
- Hunt: [What we hunted for - link to run notes]
- Outcome: [Remediated X systems, found/no indicators]
- Lessons: [Any takeaways for future similar CVEs]

---

## Hunt Prioritization Matrix

Use this to prioritize which CVEs to hunt for proactively:

| Priority | Criteria |
|----------|----------|
| **🔴 Critical - Hunt Immediately** | CVSS 9-10 + Public Exploit + Affects Production + Active Exploitation |
| **🟠 High - Hunt This Sprint** | CVSS 7-8.9 + Public Exploit + Affects Production OR Zero-day in our tech |
| **🟡 Medium - Hunt When Capacity** | CVSS 4-6.9 + PoC Available + Large attack surface |
| **🟢 Low - Monitor Only** | CVSS <4 OR No exploit available OR Not in our environment |

---

## CVE Monitoring Sources

Document where vulnerability information comes from:

### Feeds & Alerts
- **NVD Data Feeds:** [https://nvd.nist.gov/vuln/data-feeds]
- **CISA KEV Catalog:** [https://www.cisa.gov/known-exploited-vulnerabilities-catalog]
- **Vendor Security Advisories:**
  - Microsoft: [https://msrc.microsoft.com/update-guide]
  - AWS: [https://aws.amazon.com/security/security-bulletins/]
  - [Add other vendors relevant to your environment.md]

### Threat Intelligence
- **Exploit Databases:** [Exploit-DB, Packet Storm, GitHub exploit repos]
- **Threat Intel Platforms:** [MISP, ThreatConnect, Recorded Future, custom feeds]
- **Security Research:** [Twitter/X, blogs, security mailing lists]

### Internal Sources
- **Vulnerability Scanners:** [Nessus, Qualys, Rapid7 - feed results here]
- **Patch Management:** [WSUS, SCCM, Jamf - what's deployed, what's pending]
- **Asset Discovery:** [Cross-check CVEs against environment.md quarterly]

---

## Automation Opportunities (Level 3+)

### Single Agent Tasks (Level 3)
When you reach maturity Level 3, consider automating:

1. **CVE Monitoring Agent**
   - Monitors NVD/CISA feeds daily
   - Cross-references CVEs against environment.md
   - Auto-adds entries to this file when: `Severity >= High` AND `Tech in environment.md` AND `Exploit available`
   - Generates hunt suggestions in Slack/Teams/Email

2. **Exploit Tracking Agent**
   - Watches GitHub/Exploit-DB for new PoCs
   - Updates "Exploit Availability" section when PoC published
   - Escalates priority if weaponized exploit detected

3. **Hunt Status Updater**
   - Reads completed run notes from runs/ folder
   - Auto-updates "Hunt References" section with links
   - Changes status to "Hunt Completed" when run note added

### Multi-Agent Orchestration (Level 4+)
More advanced automation:

- **Discovery → CVE → Hunt Pipeline:**
  1. Asset discovery agent updates environment.md weekly
  2. CVE monitoring agent finds new vulnerabilities
  3. Prioritization agent scores against threat intel + asset criticality
  4. Hunt generation agent creates hypothesis + query templates
  5. Human reviews and approves hunt execution

- **Closed-Loop Verification:**
  - Hunt findings feed back to vulnerability.md ("Found exploit attempt" → Escalate priority)
  - Patch deployment from change management system auto-updates remediation status

---

## Example Agent Prompt (Level 3)

```
You are a CVE monitoring agent. Your job:

1. Check NVD feed for CVEs published in last 24 hours
2. Read environment.md to understand our tech stack
3. For each CVE:
   - Does it affect technology we use? (Check Product + Version)
   - Is CVSS >= 7.0?
   - Is there a public exploit? (Check Exploit-DB, GitHub)
4. If all three are YES:
   - Add CVE entry to vulnerabilities.md using the template
   - Post to #security-alerts Slack channel
   - Suggest hunt hypothesis based on vulnerability type

Run daily at 0800 UTC.
```

---

## Maintenance Notes

### Review Checklist (Weekly)
- [ ] Check CVE feeds for new vulnerabilities affecting our environment.md
- [ ] Update exploit availability for existing tracked CVEs
- [ ] Review CISA KEV catalog for newly added items matching our tech
- [ ] Move remediated CVEs to "Resolved" section
- [ ] Update hunt status links when runs are completed
- [ ] Quarterly: Archive resolved CVEs >1 year old to separate file

### Change Log
- **YYYY-MM-DD:** Initial creation
- **YYYY-MM-DD:** Added CVE-2024-XXXX after vendor advisory
- **YYYY-MM-DD:** Moved CVE-2023-XXXX to resolved after patch deployment

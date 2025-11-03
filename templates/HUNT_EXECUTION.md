# H-#### | [Hunt Title] | YYYY-MM-DD

**Status**: In Progress / Completed

---

## Hypothesis

[Copy from H-####.md or state concisely in your own words]

**Example:** Adversaries use automated tools to attempt SSH authentication against Linux servers, generating patterns of failed login attempts that indicate brute force activity.

---

## Data Source

**Query**: `queries/H-####.spl` (or .kql, .sql)
**Time Range**: `-24h` to `now` (or specify: `2025-01-15 00:00` to `2025-01-22 23:59`)
**Runtime**: `X.X seconds`
**Rows Examined**: `X,XXX`
**Rows Returned**: `XX`

**Query Performance Notes:**
- [Any timeouts, performance issues, or optimization observations]

---

## Findings

[Summarize what the data showed in 2-5 sentences. Be specific: "Found 47 sources with >5 failed attempts" not "Found some activity"]

### Key Observations

**Patterns Identified:**
- [Observable pattern 1 with specific metrics]
- [Observable pattern 2 with specific metrics]
- [Observable pattern 3 with specific metrics]

**Notable Examples:**
- Source IP X.X.X.X: [What made this stand out?]
- User account `[username]`: [Suspicious behavior observed]

**Evidence Locations:**
- Screenshots: `[link or attachment]`
- Exported results: `[CSV/JSON file location]`
- SIEM query bookmark: `[link]`
- Related tickets: SOC-####, IR-####

---

## Analysis

[Detailed analysis: What patterns did you see? What behaviors stood out? How did you distinguish malicious from benign?]

### True Positives

[Confirmed threats or high-confidence suspicious activity]

**Example:**
- `203.0.113.45`: 247 failed attempts targeting real service accounts (jenkins, gitlab-runner) over 2 hours. Sustained pattern, external IP, outside business hours. **High confidence brute force.**

### False Positives

[Benign activity that triggered your hunt criteria but was expected/legitimate]

**Example:**
- 4 internal IP sources: Legitimate users with password typos (confirmed via successful login immediately after failures)
- 42 automated scanners: Expected baseline internet noise hitting default credentials

### Gaps & Limitations

[What couldn't you validate? What data was missing? What questions remain unanswered?]

**Example:**
- Cannot distinguish "targeting real users" vs "random guessing" programmatically without asset/user inventory correlation
- No visibility into whether passwords were weak/default (authentication logs don't reveal this)

---

## Decision

**Status**: [X] Accept | [ ] Reject | [ ] Needs Changes

**Reasoning:** [Clear 1-3 sentence explanation of your decision. What evidence led you here?]

**Examples:**
- **Accept:** Found one high-confidence brute force attempt targeting real accounts. Query successfully identified threat but needs refinement to reduce noise.
- **Reject:** Query returned only false positives. Hypothesis does not match current threat landscape. Archive for potential future use.
- **Needs Changes:** Query logic is sound but threshold too low. Captured excessive noise. Increase threshold from >5 to >10 and re-run.

---

## Next Actions

**Immediate Actions:**
- [X] [Action taken - e.g., Blocked 203.0.113.45 at perimeter firewall (completed 2025-10-22 06:30)]
- [ ] [Pending action - e.g., Escalate to IR for forensic analysis]

**Follow-Up Work:**
- [ ] [Detection engineering: Create detection rule based on refined query]
- [ ] [Remediation: Rotate credentials for targeted service accounts]
- [ ] [Documentation: Update runbook with new baseline findings]

**Tickets Created:**
- SOC-#### - [Brief description]
- IR-#### - [Brief description]
- DE-#### - [Brief description]

---

## Lessons Learned

[What did you learn from this hunt execution? What will you do differently next time? What insights benefit the team?]

### Query Refinements Needed

[Specific technical improvements for next run]

**Example:**
- Increase threshold from `>5` to `>10` attempts (reduces FP by ~75%)
- Add IP filter: `NOT (src_ip="10.*" OR src_ip="172.16.*" OR src_ip="192.168.*")`
- Add temporal logic: Focus on "newly seen in 7 days" source IPs

### Detection Gaps Identified

[New detection opportunities or missing visibility discovered during hunt]

**Example:**
- No alerting on "successful login after multiple failures" (credential stuffing indicator)
- Service account SSH activity not baselined (can't detect anomalies)

### Environmental Insights

[New knowledge about your environment, baselines, or normal behavior]

**Example:**
- Service accounts (jenkins, gitlab, ansible) are publicly referenced in GitHub repos - explains targeted attempts
- Average baseline: 40-50 scanner IPs per day attempting SSH - this is normal noise

### Recommendations for Future Hunts

**For next execution of this hunt:**
- [Specific improvement to make when running H-#### again]

**For new related hunts:**
- Hunt for successful SSH logins after failed attempts (credential stuffing detection)
- Hunt for SSH connections from newly created user accounts

---

## Related Work

**Tickets:**
- SOC-2847: Initial alert that triggered this hunt
- IR-1052: Blocked malicious IP response
- DE-3041: Detection rule creation task

**Related Hunts:**
- H-0002: SSH key-based authentication anomalies (complementary hunt)
- H-0015: Linux privilege escalation (potential next step after SSH compromise)

**Threat Intel:**
- MITRE ATT&CK T1110.001: https://attack.mitre.org/techniques/T1110/001/
- [Link to relevant CTI reports, advisories, or blog posts]

---

**Hunter:** [Your name or team]
**Reviewed By:** [Peer reviewer if applicable]
**Date Completed:** YYYY-MM-DD

# AGENTS.md - Context for AI Assistants

**Purpose:** This file tells AI assistants (GitHub Copilot, Claude Code, Cursor, etc.) what your threat hunting repository contains and how to use it.

**When to create this:** Level 2 (Augmented) - when you add AI integration to your hunting workflow.

**Who reads this:** AI tools that have access to your repository files.

---

## Repository Overview

This repository contains threat hunting hypotheses, execution notes, and lessons learned using the LOCK pattern (Learn → Observe → Check → Keep).

**AI assistants should:**
- Read past hunt notes before suggesting new hypotheses
- Reference lessons learned when generating queries
- Avoid suggesting hunts we've already completed
- Use environment.md and vulnerabilities.md to inform hunt planning

---

## Repository Structure

```
/
├── hunts/              # Hunt hypotheses and execution notes
│   ├── H-XXXX.md       # Hypothesis files
│   └── H-XXXX_DATE.md  # Execution/run notes
├── templates/          # LOCK-structured templates for new hunts
├── prompts/            # AI prompt templates for manual workflows
├── queries/            # Reusable query patterns
├── metrics/            # Performance tracking and memory guidance
├── environment.md      # Tech stack, tools, infrastructure inventory
└── vulnerabilities.md  # CVE tracking and hunt opportunities
```

---

## Environmental Context Files

### environment.md
**Contains:**
- Security tools (SIEM, EDR, firewalls, monitoring platforms)
- Technology stack (languages, frameworks, databases, cloud services)
- Network architecture and infrastructure
- Internal documentation links (wikis, architecture diagrams, asset inventory)

**How AI should use this:**
- Reference when generating hypotheses to ensure hunt is relevant to our tech stack
- Check data sources available before suggesting queries
- Cross-reference CVEs against our actual deployed technologies
- Understand what visibility/telemetry we have for proposed hunts

**Example:**
```
User: "Generate hypothesis for hunting Log4j exploitation"
AI: *checks environment.md* "I see you run Java applications with log aggregation
     in Splunk. Here's a hypothesis targeting your environment..."
```

### vulnerabilities.md
**Contains:**
- Known CVEs affecting our environment (cross-referenced with environment.md)
- Exploit availability and active exploitation status
- Hunt opportunities prioritized by severity + exploit maturity
- Remediation tracking and hunt status

**How AI should use this:**
- Check before suggesting CVE-driven hunts (avoid duplicates)
- Reference past vulnerability hunts when generating new hypotheses
- Suggest updating this file when new CVEs are published for our tech stack
- Use exploit availability data to prioritize hunt suggestions

**Example:**
```
User: "What should we hunt for this week?"
AI: *reads vulnerabilities.md* "Based on open vulnerabilities:
     - CVE-2024-1234 affects your Nginx deployment (exploit published yesterday)
     - CVE-2024-5678 is being actively exploited (CISA KEV listed)
     I recommend prioritizing these two..."
```

---

## Data Sources

**Update this section with your actual data sources:**

### SIEM / Log Aggregation
- **Platform:** [e.g., Splunk Enterprise, Elastic Security, Microsoft Sentinel]
- **Indexes/Tables:** [e.g., `index=winlogs`, `SecurityEvent`, `logs-*`]
- **Retention:** [e.g., 90 days hot, 1 year cold]
- **Query Language:** [SPL, KQL, Lucene, etc.]

### EDR / Endpoint Security
- **Platform:** [e.g., CrowdStrike Falcon, Microsoft Defender, SentinelOne]
- **Telemetry:** [Process execution, network connections, file events]
- **Query Access:** [API, console, integrated with SIEM]

### Network Security
- **Flow Data:** [NetFlow, IPFIX, Zeek logs]
- **Packet Capture:** [Available? Retention period?]
- **Firewall Logs:** [Vendor, where logs are stored]
- **IDS/IPS:** [Snort, Suricata, alerts in SIEM]

### Cloud Security
- **Providers:** [AWS, Azure, GCP]
- **Logging:** [CloudTrail, Azure Monitor, Cloud Logging]
- **Where Stored:** [Centralized in SIEM? Separate platform?]

### Identity & Access
- **Identity Provider:** [Active Directory, Okta, Azure AD]
- **Authentication Logs:** [Where are auth events logged?]
- **MFA Events:** [Available? Where?]

**AI Note:** Always verify data sources exist before generating queries. Reference environment.md for detailed coverage.

---

## Hunting Methodology

This repository follows the **LOCK pattern**:

1. **Learn** - Gather context (CTI, alert, anomaly, vulnerability)
2. **Observe** - Form hypothesis about adversary behavior
3. **Check** - Test with bounded, safe query
4. **Keep** - Record decision and lessons learned

**AI assistants should:**
- Generate hypotheses in LOCK format (see templates/)
- Ensure queries are bounded by time, scope, and impact
- Document lessons learned after hunt execution
- Reference past hunts when suggesting new ones

---

## Guardrails for AI Assistance

### Query Safety
- **Always include time bounds** (e.g., last 7 days, not "all time")
- **Limit result sets** (e.g., `| head 1000`, `TOP 100`)
- **Avoid expensive operations** without explicit approval (stats over 30+ days, full table scans)
- **Test on small windows first** before expanding timeframe

### Hypothesis Validation
- **Check if we've hunted this before** (grep hunts/ folder)
- **Verify data source availability** (reference environment.md)
- **Ensure hypothesis is testable** (can be validated with a query)
- **Consider false positive rate** (will this hunt generate noise?)

### Documentation
- **Use LOCK structure** for all hunt documentation
- **Capture negative results** (hunts that found nothing are still valuable)
- **Record lessons learned** (what worked, what didn't, what to try next time)
- **Link related hunts** (if this builds on past work, reference it)

### Memory and Context
- **Search before suggesting** - Check if we've hunted this TTP/CVE/behavior before
- **Reference environment.md** - Ensure suggestions match our actual tech stack
- **Check vulnerabilities.md** - Don't suggest hunts we've already completed
- **Apply past lessons** - Use outcomes from similar hunts to improve new hypotheses

---

## AI Workflow Examples

### Level 2 (Augmented) - AI with Memory

**User asks:** "Generate hypothesis for hunting Kerberoasting attacks"

**AI should:**
1. Search `hunts/` for past Kerberoasting hunts
2. Check `environment.md` for Active Directory presence and logging capabilities
3. Read any lessons learned from similar hunts (T1558.003, credential access TTPs)
4. Generate LOCK-formatted hypothesis referencing available data sources
5. Suggest query targeting confirmed data sources (e.g., Windows Security Event Logs in Splunk)

**User asks:** "What should we hunt based on recent CVEs?"

**AI should:**
1. Read `vulnerabilities.md` to see open/monitoring CVEs
2. Cross-reference against `environment.md` to confirm affected tech
3. Check `hunts/` to see if we've already hunted these CVEs
4. Prioritize by: Severity + Exploit availability + Not yet hunted
5. Generate hunt suggestions with hypotheses

### Level 3+ (Autonomous) - Scripted Workflows

When building automation scripts, AI should:
- Reference this AGENTS.md file to understand repo structure
- Use environment.md as the tech stack source of truth
- Update vulnerabilities.md when discovering new relevant CVEs
- Follow LOCK structure when auto-generating hunt files
- Include human-in-the-loop validation for high-risk operations

---

## ATT&CK Coverage

**Optional - Document your priority TTPs:**

### High Priority TTPs (based on threat model)
- T1003 - Credential Dumping
- T1059 - Command and Scripting Interpreter
- T1110 - Brute Force
- T1078 - Valid Accounts
- [Add your priority TTPs]

### Known Coverage Gaps
- [TTPs you can't currently hunt due to telemetry gaps]
- [Reference environment.md "Known Gaps & Blind Spots" section]

**AI Note:** Prioritize hunt suggestions for high-priority TTPs with available telemetry.

---

## Organization-Specific Context

**Optional - Add details unique to your environment:**

### Threat Model
- [Industry-specific threats you care about]
- [Known adversary groups targeting your sector]
- [Past incidents that inform current focus]

### Business Context
- [Critical applications/services that need extra scrutiny]
- [Compliance requirements affecting hunt scope]
- [Operational constraints (maintenance windows, access restrictions)]

### Team Practices
- [Hunt cadence - daily, weekly, monthly?]
- [Who reviews/approves hunts before execution?]
- [Where do hunt results get reported?]

---

## Memory Scaling (Reference: metrics/README.md)

**Current maturity level:** [0: Ephemeral | 1: Persistent | 2: Augmented | 3: Autonomous | 4: Coordinated]

**Memory approach:**
- **Level 1-2:** Grep-based search across markdown files (no structured memory yet)
- **Level 3+:** [If applicable: SQLite/JSON index for faster search, agent-accessible structured memory]

**When AI needs to recall past hunts:**
- Use grep/search across `hunts/` folder (hunt notes)
- Use grep/search across `environment.md` (tech stack)
- Use grep/search across `vulnerabilities.md` (CVE tracking)
- At Level 3+: Query structured memory if available

---

## Maintenance Notes

**Review this file when:**
- Adding new data sources (update "Data Sources" section)
- Changing AI tools (update "AI Workflow Examples")
- Reaching new maturity level (update "Memory Scaling")
- Discovering AI generates incorrect assumptions (add to "Guardrails")

**Last Updated:** [YYYY-MM-DD]
**Maintained By:** [Team/Individual]

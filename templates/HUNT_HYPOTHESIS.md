# H-#### | [Hunt Title]

**Status**: Candidate

## Hypothesis

Adversaries use [specific behavior] to [achieve goal] on [target system/environment].

**Example:** Adversaries use automated tools to attempt SSH authentication against Linux servers, generating patterns of failed login attempts that indicate brute force activity.

## Context

**Why Now:** [What triggered this hunt?]
- CTI report, alert, anomaly, security gap, or threat model update
- Reference threat intel sources, advisory IDs, or past related hunts

**ATT&CK Mapping:**
- **Technique:** T#### - [Technique Name]
- **Tactic:** TA#### - [Tactic Name]
- **Sub-technique:** (if applicable)

**Related Past Hunts:**
- H-#### - [Brief note on what was learned]

## Data Needed

**Primary Data Source:**
- **Index/Source:** `index_name` or log source name
- **Key Fields:** `field1`, `field2`, `field3`, `_time`
- **Expected Volume:** [Estimate: rows per day, GB, etc.]
- **Retention:** [How far back can you search?]

**Secondary Data Sources (if needed):**
- Asset inventory, threat intel feeds, user/account lists

**Telemetry Gaps:**
- [List any missing visibility that limits this hunt]

## Time Range

**Initial Hunt Window:**
- **Earliest:** `-24h` (or `-7d`, `-30d` based on threat context)
- **Latest:** `now`
- **Result Cap:** `1000` (adjust for query performance)

**Rationale:** [Why this time range? Threat intel date? Incident timeframe? Baseline period?]

## Query Approach

**High-Level Logic:**
1. [Step 1: Filter to relevant log source and time range]
2. [Step 2: Aggregate by key dimensions]
3. [Step 3: Apply thresholds or anomaly detection]
4. [Step 4: Exclude known false positives]

**Query File:** `queries/H-####.spl` (or .kql, .sql)

**Expected False Positives:**
- [List anticipated benign activities that may trigger]
- [Reference lessons from past hunts if applicable]

**Performance Considerations:**
- [Expected runtime, impact on SIEM, any index optimization needed]

## Success Criteria

**Hunt is successful if:**
- Query runs within acceptable time (<30 seconds ideal)
- Results are actionable (clear true positive vs false positive differentiation)
- Findings lead to detection rule, remediation, or documented baseline

**Hypothesis is validated if:**
- [What evidence would prove adversary behavior exists?]

---

## Execution Instructions

**To run this hunt:**

1. **Copy this file:**
   ```bash
   cp hunts/H-####.md hunts/H-####_$(date +%Y-%m-%d).md
   ```

2. **Execute query and document findings in dated file**

3. **Keep this hypothesis file unchanged for future runs**

**For AI-assisted hunting:**
- Ask your AI: "Generate queries for hunt H-####" or "Execute hypothesis H-####"
- AI will read this file, generate/run queries, and populate the execution report

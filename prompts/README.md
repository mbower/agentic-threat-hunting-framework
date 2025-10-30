# AI Prompt Library

This folder contains prompts to help you at **Level 0-1: Ephemeral/Persistent** (before AI integration).

## What's Here

### hypothesis-generator.md
Use with ChatGPT, Claude, or other AI assistants to generate behavior-based hunt hypotheses from context like CTI, alerts, or anomalies.

**When to use**: You have context but need help forming a testable hypothesis

### query-builder.md
Use to draft safe, bounded queries for Splunk, KQL, or Elastic from your hypothesis.

**When to use**: You have a hypothesis but need help writing the query

### summarizer.md
Use to help document hunt results and capture lessons learned in LOCK pattern format.

**When to use**: You ran a query and need help writing a concise run note

## How to Use These Prompts

1. **Copy the prompt** from the markdown file
2. **Fill in your context** (hypothesis, data sources, results)
3. **Paste into your AI assistant** (ChatGPT, Claude, etc.)
4. **Review and refine** the output
5. **Test before using** - AI can make mistakes!

## Important Notes

### AI Assistance ≠ Autopilot

- **Always review** AI-generated hypotheses for feasibility
- **Always test** AI-generated queries on small timeframes first
- **Always validate** that queries are safe and bounded
- **Use your judgment** - You know your environment better than AI

### Keep It Simple

These prompts are designed to:
- Help you think through the LOCK pattern systematically
- Reduce writer's block when starting hunts
- Capture lessons more consistently
- NOT to fully automate hunting (that's Level 3+: Autonomous)

### Learning Tool

Think of these prompts as training wheels:
- They help you get started faster
- They teach you the LOCK pattern structure
- Over time, you'll need them less
- But they remain useful for complex hunts

## Platform-Specific Tips

### For Splunk Users
- Mention "Splunk SPL" in your prompt
- Specify data models when available
- AI knows common Splunk patterns

### For KQL Users
- Mention "KQL for Sentinel" or "KQL for Defender"
- Specify table names (SecurityEvent, DeviceProcessEvents, etc.)
- AI understands Sentinel-specific syntax

### For Elastic Users
- Mention "Elastic EQL" or "Lucene query"
- Specify index patterns
- Note which Elastic stack version

## Customizing Prompts

Feel free to modify these prompts for your environment:

- Add your organization's specific data sources
- Include your ATT&CK coverage gaps
- Reference your baseline automation
- Add your threat model priorities

## Contributing

Have a better prompt? Found a useful variation?
- Submit a PR with your improved prompts
- Share what works in your environment
- Help others get started faster

## Next: Add AI Integration (Level 1 → 2)

Once you reach **Level 2: Augmented**, you'll use AI tools (GitHub Copilot, Claude Code, Cursor) that automatically read your hunt repository via AGENTS.md.

These prompts remain useful for:
- Teams without AI tool access
- Manual workflows
- Understanding what AI should generate

**Before Level 2, you can still enhance these prompts with manual memory:**

- "Check if we've hunted this before: [paste grep results from hunts/]"
- "What lessons from past hunts apply here?"
- "Compare this hypothesis to previous ones: [paste relevant hunt notes]"

Example workflow:
```bash
# Before starting a hunt, search your memory
grep -l "T1110.001" hunts/*.md        # Find by TTP
grep -i "brute force" hunts/*.md      # Find by behavior
grep -i "powershell" hunts/*.md       # Find by technology
grep -i "active directory" hunts/*.md # Find by application
grep -i "privilege escalation" hunts/*.md  # Find by keyword

# Share relevant past hunts with your AI assistant
# Then use the prompts above
```

This manual recall loop works at Level 1: Persistent. At Level 2: Augmented, AI tools do this automatically.

## Using Environmental Context (environment.md, vulnerabilities.md)

At **Level 2: Augmented**, incorporate environmental context into your workflow:

### Before Planning a Hunt

```bash
# CVE alert: "Critical vulnerability in Nginx 1.21.x"

# 1. Check if we run affected tech
grep -i "nginx" environment.md
# Result: Found "Nginx 1.21.6 on web-proxy-01 through web-proxy-05"

# 2. Check if we've already hunted this
grep -i "CVE-2024-1234" vulnerabilities.md
# Result: Not found - this is new

# 3. Check for similar past hunts
grep -i "nginx" hunts/*.md
grep -i "http smuggling" hunts/*.md
# Result: H-0034 hunted similar web exploit patterns

# 4. Use prompt with enriched context
# Copy to AI: "We run Nginx 1.21.6. Past hunt H-0034 found X. Generate hypothesis for CVE-2024-1234..."
```

### Environment-Aware Hypothesis Generation

When using **hypothesis-generator.md**, include environmental context:

```
Context: CVE-2024-1234 affects Nginx versions 1.20.0-1.22.1

Our environment (from environment.md):
- Nginx 1.21.6 on web-proxy-01 through web-proxy-05
- Logs: web access logs in Splunk (index=web_proxy)
- Network: Public-facing load balancers
- WAF: Cloudflare in front of Nginx

Past similar hunts:
- H-0034: HTTP request smuggling hunt (found 2 attempts)

Generate LOCK hypothesis for hunting CVE-2024-1234 exploitation attempts.
```

This provides AI with:
- **Tech context** (what we run)
- **Data context** (where to look)
- **Historical context** (past lessons)

### Level 3+ Automation Example

At Level 3, this becomes automated:

```python
# CVE monitoring agent (runs daily)
def check_new_cves():
    new_cves = fetch_nvd_feed()

    # Read tech stack
    tech_stack = parse_environment_md()

    for cve in new_cves:
        # Check if CVE affects our environment
        if cve.product in tech_stack:
            # Check if we've hunted this
            if not exists_in_vulnerabilities_md(cve.id):
                # Check exploit availability
                exploit = check_exploit_db(cve.id)

                if exploit.public and cve.cvss >= 7.0:
                    # Auto-add to vulnerabilities.md
                    add_cve_entry(cve, tech_stack[cve.product])

                    # Generate hunt suggestion
                    past_hunts = grep_similar_hunts(cve.product)
                    hypothesis = generate_hypothesis(cve, tech_stack, past_hunts)

                    # Alert human
                    notify_slack(f"New hunt opportunity: {cve.id}")
```

**Key insight:** environment.md + vulnerabilities.md enable vulnerability-driven hunting at scale.

# ClawFish Quorum — SEO Census Personas

## Design Intent: Static + Dynamic Duality

ClawFish Quorum operates on a **dual-purpose architecture** that combines the reliability of curated reference with the adaptability of AI generation.

### Static Reference (Ground Truth)

The curated persona list provides a **deterministic, reproducible** evaluation panel. Static is necessary for:

- **Consistency** — The same persona set produces comparable results across time
- **Mathematical reproducibility** — Benchmarks, comparisons, and audits require ground truth
- **Accurate replay** — Re-run any evaluation with identical parameters and get consistent representation

When you need repeatable results — auditing an SEO tool, comparing resources, running structured reviews — the static personas are your foundation.

### Dynamic Generation (On-the-Fly)

The system has built-in **on-the-fly dynamic generation**. AI will automatically determine which "census" population is most fitting for any given query or context. This means:

- Novel contexts that the static set doesn't cover get purpose-built personas
- The system adapts to emerging SEO disciplines without manual curation
- Hybrid panels can blend static + dynamically generated personas

**Users can also manually adjust the census population in the UI** (to be created), giving full control over which perspectives evaluate a resource.

---

## The Census Metaphor

Just as **census data represents populations**, persona sets represent **stakeholder populations** relevant to a domain.

Each persona in ClawFish Quorum represents a distinct SEO stakeholder group — the people who actually use, evaluate, and depend on SEO resources. A "census population" is a named grouping of these stakeholders assembled for a specific evaluation context.

**AI auto-selects the best-fit census population**, but human override is always available. The system considers:

- The type of resource being evaluated (tool, guide, dataset, etc.)
- The domain context (technical, content, commerce, research, etc.)
- The evaluation goal (audit, comparison, discovery, etc.)

---

## Personas (Static Census)

| ID | Name | Census Population | Thinks In... |
|----|------|-------------------|--------------|
| `technical-seo-architect` | Technical SEO Architect | infrastructure-ops | HTTP status codes and log files |
| `content-strategist` | Content Strategist | content-editorial | User intent and content gaps |
| `link-building-specialist` | Link Building Specialist | outreach-authority | Domain authority and referral paths |
| `local-seo-expert` | Local SEO Expert | local-commerce | NAP consistency and geo-relevance |
| `ecommerce-seo-specialist` | E-commerce SEO Specialist | digital-commerce | Product feeds and purchase intent |
| `seo-data-analyst` | SEO Data Analyst | analytics-measurement | Data pipelines and attribution |
| `international-seo-strategist` | International SEO Strategist | global-markets | Language-market matrices |
| `seo-tool-developer` | SEO Tool Developer | developer-tooling | Scalability and developer experience |
| `agency-seo-director` | Agency SEO Director | business-strategy | Business impact and resource allocation |
| `search-engine-researcher` | Search Engine Researcher | academic-research | SERP feature evolution and search quality |

---

## Census Populations (Predefined Panels)

| Population | Personas | When to Use |
|------------|----------|-------------|
| `technical-audit` | Architect, Analyst, Developer | Technical tools, crawl systems, performance |
| `content-review` | Strategist, Link Builder, Director | Content guides, outreach, editorial |
| `commerce-local` | Local Expert, E-commerce Specialist | Commerce tools, local search, GBP |
| `full-spectrum` | All 10 | Comprehensive evaluation, general platforms |
| `research-development` | Developer, Researcher | Academic papers, experimental tools |
| `strategy-planning` | Strategist, Director, International | Business planning, market expansion |

---

## Extending with Custom Personas

Add new personas to `personas.yaml` following the schema:

```yaml
- id: your-persona-id
  name: Display Name
  role: One-line role description
  expertise:
    - Skill 1
    - Skill 2
  perspective: >
    How this persona thinks and evaluates.
  evaluation_criteria:
    - Criterion 1
    - Criterion 2
  census_population: population-group-id
```

Then add them to relevant populations in `census-populations.yaml`.

---

## File Structure

```
quorum/
  README.md                  # This file
  personas.yaml              # All persona definitions (static reference)
  census-populations.yaml    # Named population groupings
  research-protocol.md       # Delegated discovery + editorial consensus protocol
  judicial-system.md         # Voting system, judicial scoring, improvement loops
```

---

## Version-Controlled Editorial Consensus
**(i.e. Wikipedia and GitHub-Style Review)**

ClawFish uses the same multi-round review model that makes Wikipedia and GitHub's PR review process reliable at scale:

1. **Research Delegation** — MECE partitions assigned BEFORE crawling to prevent duplicates
2. **Discovery** — Each persona crawls their partition independently
3. **Compilation** — Results merged, deduplicated, cross-referenced
4. **Peer Review** — Each resource reviewed by 2+ personas outside the discoverer's domain
5. **Citation Validation** — Every citation validated minimum 3x by different personas
6. **Formal Voting** — Disputed resources go through judicial vote (1-by-1, blind, sequential)
7. **Council Review** — Governance, Neuromodest, and Ethics councils audit the process

See [research-protocol.md](research-protocol.md) for the full editorial pipeline.
See [judicial-system.md](judicial-system.md) for the voting system and scoring.

---

## External Councils

| Council | Role | Checks |
|---------|------|--------|
| **Governance** | Process integrity | MECE compliance, conflict of interest, completeness gaps |
| **Neuromodest** | Humility enforcement | Overclaiming, superlatives, scope honesty, recency |
| **Ethics** | Fairness & integrity | Affiliations, pay-to-play, diversity, harm, privacy |

---

## Anti-Corruption Safeguards

| Safeguard | What It Prevents | How |
|-----------|-----------------|-----|
| **Anti-Boxing** | Tunnel vision | Domain outsider injection, source diversification, contrarian researcher |
| **Anti-Echoing** | Groupthink | Independent validation, source independence, echo detection |
| **Anti-Faking** | Hallucination | URL verification, 3x citation validation, hallucination scorecard |

---

## Sentiment Slider

Default: **intentionally negative (skeptical)** for the most realistic outcomes.

```
SKEPTICAL (-5) ◄━━━━●━━━━━━━━━► GENEROUS (+5)
              -2 (default)
```

Users can adjust in the UI to prefer more or less skepticism. See [judicial-system.md](judicial-system.md) for how the slider affects voting thresholds, citation requirements, and inclusion criteria.

---

## UI: Design Studio Integration

The ClawFish UI is built on the **Design Studio** platform (`/Users/mac/Documents/PROJECTS/design-studio/`), providing:

- **Search interface** — Myelin8-powered hybrid search across all resources
- **Census population selector** — Manual override for which personas evaluate
- **Sentiment slider** — Drag to adjust skeptical ↔ generous research bias
- **Judicial score dashboard** — Resource scores, tiers, trending, improvement velocity
- **Voting interface** — Real-time persona voting with blind sequential ballots
- **Citation browser** — Navigate the citation index with validation status

---

## How It Works in Practice

1. **User submits a resource for evaluation** (e.g., an SEO crawler repo)
2. **AI auto-selects the best-fit population** (e.g., `technical-audit`)
3. **Each persona in the population evaluates** from their unique perspective
4. **Results aggregate** into a multi-perspective review
5. **User can override** the population selection at any time

The static personas ensure every evaluation is grounded in defined expertise. Dynamic generation fills gaps when the static set doesn't fully cover a novel context.

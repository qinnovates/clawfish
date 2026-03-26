# ClawFish Research Protocol

## How Research Works: Delegated Discovery → Editorial Consensus

ClawFish uses a **Wikipedia-style editorial consensus process** powered by Quorum personas. No researcher works in isolation. No resource enters the library without multi-round review.

---

## Phase 0: Research Delegation (Before Anyone Claws)

**CRITICAL: No duplicates. Research is partitioned BEFORE crawling begins.**

The Supervisor analyzes the research target and assigns **non-overlapping search partitions** using MECE (Mutually Exclusive, Collectively Exhaustive) decomposition.

### Partition Strategy

```yaml
delegation:
  technical-seo-architect:
    partition: "Crawlers, site auditing tools, rendering tools, server-side SEO"
    sources: ["GitHub topics: seo-crawler, site-audit", "Product Hunt: developer-tools"]
    exclusions: ["content tools", "keyword tools", "link tools"]

  content-strategist:
    partition: "Content optimization, writing assistants, readability tools, topic research"
    sources: ["GitHub topics: content-seo, nlp-seo", "Product Hunt: writing-tools"]
    exclusions: ["crawlers", "technical audit tools", "link building"]

  link-building-specialist:
    partition: "Backlink analysis, outreach tools, digital PR, link prospecting"
    sources: ["GitHub topics: backlink, link-building", "G2: link-building-software"]
    exclusions: ["crawlers", "content tools", "keyword tools"]

  local-seo-expert:
    partition: "GBP tools, citation managers, review platforms, local rank trackers"
    sources: ["GitHub topics: local-seo", "Capterra: local-seo-software"]
    exclusions: ["general rank trackers", "national SEO tools"]

  ecommerce-seo-specialist:
    partition: "Product schema tools, feed optimizers, marketplace SEO, faceted nav"
    sources: ["Shopify App Store", "WooCommerce plugins", "GitHub: ecommerce-seo"]
    exclusions: ["general SEO tools", "content tools"]

  seo-data-analyst:
    partition: "Analytics platforms, rank trackers, log analyzers, Search Console tools"
    sources: ["GitHub topics: seo-analytics, rank-tracker", "G2: seo-analytics"]
    exclusions: ["content tools", "link tools", "crawlers"]

  international-seo-strategist:
    partition: "Hreflang tools, localization platforms, non-Google search tools (Baidu, Yandex, Naver)"
    sources: ["GitHub: hreflang, international-seo", "Baidu webmaster forums", "Yandex tools"]
    exclusions: ["English-only general tools"]

  seo-tool-developer:
    partition: "SEO libraries, APIs, SDKs, developer frameworks, CLI tools"
    sources: ["GitHub: seo-api, seo-sdk, seo-library", "npm: seo-*", "PyPI: seo-*"]
    exclusions: ["end-user SaaS tools", "courses", "articles"]

  agency-seo-director:
    partition: "Reporting tools, client management, ROI calculators, SEO dashboards"
    sources: ["G2: seo-reporting", "Capterra: seo-agency", "Product Hunt: reporting"]
    exclusions: ["developer tools", "technical crawlers"]

  search-engine-researcher:
    partition: "Academic papers, algorithm research, patents, IR theory, datasets"
    sources: ["Google Scholar: SEO ranking signals", "arxiv: information-retrieval", "SIGIR proceedings"]
    exclusions: ["commercial tools", "how-to guides"]
```

### Deduplication Protocol

Before any researcher begins:

1. **Supervisor publishes the partition map** — every researcher sees ALL partitions (not just their own)
2. **Boundary resources** (tools that span categories) are pre-assigned to ONE researcher with a `shared_tag`
3. **URL dedup at ingest** — if two researchers find the same URL, the citation from the researcher whose partition it best fits is kept; the other is logged as `corroborating_discovery`
4. **Name collision detection** — fuzzy match on resource names catches "Screaming Frog" vs "ScreamingFrog" vs "screaming-frog"

```python
# Dedup pipeline
for resource in all_discovered:
    canonical_url = normalize_url(resource.url)  # strip params, trailing slash, www
    if canonical_url in seen_urls:
        existing = seen_urls[canonical_url]
        existing.corroborating_discoveries.append({
            "found_by": resource.discovered_by,
            "partition": resource.partition,
            "timestamp": resource.discovered_at
        })
    else:
        seen_urls[canonical_url] = resource
```

---

## Phase 1: Raw Discovery (The Claw)

Each researcher crawls their partition and produces a **discovery report**:

```json
{
  "researcher": "technical-seo-architect",
  "partition": "Crawlers, site auditing tools...",
  "discovered": [
    {
      "name": "Screaming Frog SEO Spider",
      "url": "https://www.screamingfrog.co.uk/seo-spider/",
      "type": "tool",
      "category": "crawlers",
      "description": "Desktop website crawler for technical SEO auditing",
      "citations": [
        {
          "id": "cite-001",
          "source": "https://www.screamingfrog.co.uk/seo-spider/",
          "type": "primary",
          "accessed": "2026-03-26",
          "claim": "Crawls up to 500 URLs free, unlimited with license"
        }
      ],
      "confidence": "HIGH",
      "recommendation": "INCLUDE"
    }
  ],
  "not_found": ["Searched for X but found no active tools"],
  "boundary_resources": ["Found Y which may belong to content-strategist partition"]
}
```

---

## Phase 2: Editorial Consensus (Wikipedia-Style)

This is the **Good Article Review** process. Every resource passes through multiple rounds.

### Round 1: Compilation
- All discovery reports merged into a **master candidate list**
- Deduplication runs
- Each resource tagged with discovery metadata

### Round 2: Cross-Review (Peer Review)
- Each resource reviewed by **at least 2 personas outside the discovering researcher's partition**
- Reviewers check: Is the description accurate? Is it notable enough? Are citations valid?
- Reviewers add their own citations if they have additional sources

### Round 3: Citation Validation
- **Every citation validated minimum 3 times by different personas**
- Validators check: Does the URL resolve? Does the source say what the citation claims? Is the source itself credible?
- Citation status: `VERIFIED` (3+ validations) | `DISPUTED` (conflicting validations) | `UNVERIFIED` (< 3 validations)

### Round 4: Editorial Decision
- Resources with all `VERIFIED` citations → **AUTO-INCLUDE**
- Resources with any `DISPUTED` citations → **FORMAL VOTE** (see voting protocol below)
- Resources with `UNVERIFIED` citations → **RETURN TO ROUND 3** for additional validation

### Round 5: Final Review
- Governance Council (see below) reviews the complete list
- Neuromodest check: Are we overclaiming? Are descriptions inflated?
- Ethics check: Any conflicts of interest? Paid placements? Undisclosed affiliations?

---

## Citation Index (ALWAYS in JSON)

Every citation in the entire system is indexed and kept in a persistent JSON structure.

### Schema: `data/citations.json`

```json
{
  "metadata": {
    "version": "1.0.0",
    "total_citations": 0,
    "last_updated": "2026-03-26T00:00:00Z",
    "validation_threshold": 3
  },
  "citations": [
    {
      "id": "cite-001",
      "resource_id": "sha256(url)",
      "resource_name": "Screaming Frog SEO Spider",
      "source_url": "https://www.screamingfrog.co.uk/seo-spider/",
      "source_type": "primary",
      "claim": "Crawls up to 500 URLs free, unlimited with license",
      "accessed_date": "2026-03-26",
      "discovered_by": "technical-seo-architect",

      "validations": [
        {
          "validator": "seo-tool-developer",
          "status": "VERIFIED",
          "timestamp": "2026-03-26T01:00:00Z",
          "notes": "Confirmed on pricing page"
        },
        {
          "validator": "seo-data-analyst",
          "status": "VERIFIED",
          "timestamp": "2026-03-26T01:05:00Z",
          "notes": "Confirmed, also verified 500 URL limit in free version"
        },
        {
          "validator": "agency-seo-director",
          "status": "VERIFIED",
          "timestamp": "2026-03-26T01:10:00Z",
          "notes": "Confirmed, license is £199/year"
        }
      ],

      "validation_status": "VERIFIED",
      "validation_count": 3,
      "disputes": []
    }
  ]
}
```

### Citation Rules

1. **No resource enters the library without at least 1 citation**
2. **Every factual claim in a description must have a citation ID**
3. **Citations are immutable once verified** — corrections create new citations linked to the original
4. **Dead citations trigger resource re-review** — if a citation URL dies, the resource goes back to editorial review
5. **Citation provenance tracked** — who discovered it, who validated it, when

---

## Formal Voting Protocol

For resources where inclusion is uncertain (disputed citations, borderline notability, contested quality).

### Process

```
VOTE: Include "ToolName" in ClawFish?
─────────────────────────────────────────

Resource: ToolName
URL: https://...
Category: crawlers
Discovered by: technical-seo-architect
Citations: 2 VERIFIED, 1 DISPUTED

Disputed citation:
  cite-014: "Claims 99.9% uptime" — source is a marketing page,
  not independently verified

─────────────────────────────────────────
Voting order (1 by 1, sequential):

1. technical-seo-architect:  INCLUDE  | Reason: "Core tool in the space,
                                        disputed claim is marketing but
                                        tool itself is legitimate"

2. content-strategist:       INCLUDE  | Reason: "Widely referenced in
                                        content about technical SEO"

3. link-building-specialist: INCLUDE  | Reason: "Used it for broken link
                                        analysis, legitimate tool"

4. local-seo-expert:         ABSTAIN  | Reason: "Outside my domain"

5. ecommerce-seo-specialist: INCLUDE  | Reason: "Essential for e-commerce
                                        crawl audits"

6. seo-data-analyst:         INCLUDE  | Reason: "Data quality is good,
                                        disputed claim should be removed
                                        from description, not the tool"

7. international-seo-strategist: INCLUDE | Reason: "Supports multilingual
                                           crawling"

8. seo-tool-developer:       INCLUDE  | Reason: "Well-maintained, good API"

9. agency-seo-director:      INCLUDE  | Reason: "Industry standard, would
                                        be conspicuous absence"

10. search-engine-researcher: INCLUDE | Reason: "Referenced in multiple
                                        academic papers on crawl analysis"

─────────────────────────────────────────
RESULT: 9 INCLUDE / 0 EXCLUDE / 1 ABSTAIN
DECISION: INCLUDED — disputed citation removed from description
RESOLUTION: cite-014 marked RETRACTED, description updated to remove
            uptime claim
```

### Voting Rules

- **Sequential, 1-by-1** — each persona votes without seeing others' votes (prevents anchoring)
- **Votes revealed simultaneously** after all have voted
- **INCLUDE / EXCLUDE / ABSTAIN** — abstain for out-of-domain resources
- **Must provide reason** — no naked votes
- **Supermajority (>66%) required** for inclusion of disputed resources
- **Any EXCLUDE vote triggers discussion round** before final tally
- **Unanimous EXCLUDE = permanent exclusion** with documented reason

---

## External Councils

### Governance Council

**Role:** Meta-oversight. Checks that the research process itself is sound, not just the outcomes.

**Members:**
- **Process Auditor** — Verifies partition assignments were MECE, no researcher was overloaded or underutilized
- **Conflict of Interest Monitor** — Flags if a researcher has known affiliation with a discovered resource
- **Completeness Auditor** — Checks for systematic gaps (e.g., entire subcategory missing, non-English resources underrepresented)

**When invoked:**
- After every full research cycle
- When a formal vote results in a close call (< 70% agreement)
- When any persona flags a process concern

### Neuromodest Council

**Role:** Humility enforcement. Keeps ClawFish honest about what it knows and doesn't know.

**Checks:**
- **Overclaiming detection** — "Best SEO tool" → flag. "Widely used SEO tool" → acceptable
- **Superlative audit** — Scans all descriptions for unsupported superlatives (best, fastest, most comprehensive, #1)
- **Uncertainty acknowledgment** — Are limitations mentioned? Are there caveats that should be noted?
- **Scope honesty** — Does ClawFish claim to be exhaustive? It shouldn't. Acknowledge known gaps.
- **Recency humility** — Flag resources whose evaluation may be outdated

**Output:**
```json
{
  "neuromodest_report": {
    "superlatives_found": 12,
    "superlatives_resolved": 10,
    "remaining_overclaims": ["resource-id-1", "resource-id-2"],
    "gaps_acknowledged": ["No Baidu SEO tools yet", "Limited Korean market coverage"],
    "recency_warnings": ["15 resources not re-evaluated in 90+ days"]
  }
}
```

### Ethics Council

**Role:** Integrity and fairness enforcement.

**Checks:**
- **Undisclosed affiliations** — Is any contributor affiliated with a listed tool?
- **Pay-to-play detection** — Are sponsored tools mixed with organic discoveries?
- **Diversity audit** — Geographic, language, price-point diversity in recommendations
- **Harm assessment** — Could any listed tool be used for black-hat SEO or spam?
- **Privacy review** — Do listed tools respect user privacy? GDPR compliance noted?

---

## Anti-Corruption Safeguards

### Anti-Boxing

Prevents the research panel from getting stuck in familiar territory.

- **Domain outsider injection** — Every research cycle includes at least 1 researcher from outside SEO (e.g., DevOps perspective, UX researcher, data engineer) to find resources the SEO bubble misses
- **Source diversification** — No more than 30% of resources can come from the same discovery source (e.g., can't be 90% GitHub)
- **Contrarian researcher** — One researcher is explicitly tasked with finding resources that contradict the panel's assumptions

### Anti-Echoing

Prevents groupthink and citation loops.

- **Independent validation** — Citation validators cannot see each other's validation notes until all 3 have submitted
- **Source independence** — If 3 citations for a claim all trace back to the same original source, that counts as 1 citation, not 3
- **Diversity of evidence** — Resources need citations from at least 2 independent source types (e.g., official site + third-party review, not two pages on the same site)
- **Echo detection** — If >80% of researchers recommend INCLUDE on a disputed resource, trigger additional scrutiny (unanimous agreement on uncertain items is suspicious)

### Anti-Faking (Fact-Checking & Anti-Hallucination)

Prevents fabricated or inaccurate information from entering the library.

**Layer 1: URL Verification**
- Every URL in the library is HTTP-checked (200 OK, not redirected to unrelated page)
- Scheduled link rot detection (weekly)
- Dead links trigger immediate resource re-review

**Layer 2: Claim Verification**
- Every factual claim must have a citation
- Citations are validated 3x minimum (see above)
- Claims without citations are flagged `[UNSOURCED]` and cannot appear in the README

**Layer 3: Hallucination Detection**
- **Too-clean descriptions** — If a tool description sounds like marketing copy, flag for rewrite
- **Fabricated statistics** — Numbers (users, downloads, stars) must be cited with timestamp
- **Non-existent tools** — Cross-reference tool names against multiple independent sources
- **Version confusion** — Ensure tool versions/features described match current reality, not historical

**Layer 4: Anti-Hallucination Scorecard**
```
Resource Review Batch: 2026-Q1
──────────────────────────────
Total resources reviewed:     2,147
Citations total:              8,432
  VERIFIED (3+ validations):  7,891  (93.6%)
  DISPUTED:                      234  (2.8%)
  UNVERIFIED:                    307  (3.6%)

URLs checked:                 2,147
  Live (200 OK):              2,089  (97.3%)
  Redirected:                     38  (1.8%)
  Dead (4xx/5xx):                 20  (0.9%)

Hallucination flags:              14
  Resolved (claim corrected):     11
  Removed (fabricated):            3

Integrity: HIGH (93.6% verified, 0.1% fabricated)
```

---

## Sentiment Calibration: The Negativity Slider

**Default: Intentionally negative (skeptical).**

ClawFish defaults to skepticism to produce the most realistic, trustworthy outcomes. The research panel asks "why should this NOT be included?" before asking "why should it?"

### How the Slider Works

```
SKEPTICAL ◄━━━━━━━━━━━●━━━━━━━━━━━► GENEROUS
   -5  -4  -3  -2  -1  0  +1  +2  +3  +4  +5

Default position: -2 (moderately skeptical)
```

| Setting | Behavior | Use Case |
|---------|----------|----------|
| **-5 (Maximum skepticism)** | Only include resources with 5+ verified citations, unanimous INCLUDE vote, and Governance Council approval | High-stakes reference lists, academic citations |
| **-3 (Highly skeptical)** | Require 3+ verified citations, >80% INCLUDE vote, no DISPUTED citations | Professional reference, enterprise tool selection |
| **-2 (Default)** | Require 3+ verified citations, >66% INCLUDE vote, disputed citations resolved | General-purpose curated list |
| **0 (Neutral)** | Standard inclusion criteria, balanced weighting | Broad discovery, exploration |
| **+2 (Generous)** | Lower citation threshold (2+), simple majority vote, include emerging tools | Early-stage tool discovery, startup ecosystem |
| **+5 (Maximum generosity)** | Include anything with 1+ citation and no EXCLUDE votes, surface experimental tools | Brainstorming, comprehensive landscape mapping |

### Slider Effects on Each Phase

| Phase | Skeptical (-) | Generous (+) |
|-------|--------------|--------------|
| **Discovery** | Stricter source requirements, established tools prioritized | Wider net, experimental tools included |
| **Citation validation** | Higher threshold (4-5 validations) | Lower threshold (2 validations) |
| **Voting** | Supermajority required, any EXCLUDE triggers deep review | Simple majority, EXCLUDE needs justification |
| **Neuromodest** | Aggressive superlative removal, conservative descriptions | Lighter touch, allow enthusiasm where warranted |
| **Anti-faking** | Maximum scrutiny, multiple independent sources required | Standard scrutiny, trust primary sources more |

### UI Implementation (To Be Created)

```
┌─────────────────────────────────────────┐
│  Research Rigor                         │
│                                         │
│  SKEPTICAL ◄━━━━●━━━━━━━━━► GENEROUS   │
│               -2 (default)              │
│                                         │
│  Current effect:                        │
│  • Citations required: 3+              │
│  • Vote threshold: >66% supermajority  │
│  • Neuromodest: active                 │
│  • Anti-hallucination: standard        │
│                                         │
│  ⓘ Skeptical = fewer resources,        │
│    higher confidence per resource       │
│    Generous = more resources,           │
│    lower confidence per resource        │
└─────────────────────────────────────────┘
```

---

## How It All Connects

```
                    ┌──────────────────┐
                    │   GOVERNANCE     │
                    │   COUNCIL        │
                    │   (meta-review)  │
                    └────────┬─────────┘
                             │ audits
                             ▼
┌──────────┐    ┌──────────────────┐    ┌──────────────┐
│NEUROMODEST│───▶│  EDITORIAL       │◀───│   ETHICS     │
│ (humility) │    │  CONSENSUS       │    │  (integrity) │
└──────────┘    │  PIPELINE        │    └──────────────┘
                │                  │
                │  Phase 0: Delegate (MECE partitions)
                │  Phase 1: Discover (parallel claw)
                │  Phase 2: Compile + Dedup
                │  Phase 3: Cross-Review (peer review)
                │  Phase 4: Citation Validation (3x min)
                │  Phase 5: Vote (disputed items)
                │  Phase 6: Final Review
                │                  │
                └────────┬─────────┘
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
         Anti-Boxing  Anti-Echo  Anti-Fake
         (diversity)  (independence) (verification)
                         │
                         ▼
                ┌─────────────────┐
                │  SENTIMENT      │
                │  SLIDER         │
                │  -5 ◄━━●━━► +5  │
                │  (default: -2)  │
                └─────────────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │  MYELIN8 BACKBONE   │
              │  (store, index,     │
              │   search, verify)   │
              └─────────────────────┘
              ┌─────────────────────┐
              │  data/citations.json│
              │  (always indexed)   │
              └─────────────────────┘
```

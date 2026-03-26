# ClawFish Judicial System

## Version-Controlled Editorial Consensus
**(i.e. Wikipedia and GitHub-Style Review)**

ClawFish uses a **version-controlled editorial consensus** model — the same approach that makes Wikipedia and GitHub's PR review process reliable at scale. Every resource goes through formal review, every change is tracked, every decision is auditable.

---

## The Judicial Voting System

Every resource in ClawFish has a **judicial score** — a composite reputation derived from formal votes by Quorum personas. This score is persistent, versioned, and drives future improvement cycles.

### How Voting Works

```
┌─────────────────────────────────────────────────────┐
│  JUDICIAL REVIEW: Ahrefs Webmaster Tools            │
│  Category: Link Building & Analysis                 │
│  Status: UNDER REVIEW (Round 2)                     │
│                                                     │
│  ┌─────────────────────────────────────────────┐    │
│  │  Persona Votes (sequential, blind)          │    │
│  │                                             │    │
│  │  👍 Technical SEO Architect      +1  (8/10) │    │
│  │  👍 Content Strategist           +1  (7/10) │    │
│  │  👍 Link Building Specialist     +1  (9/10) │    │
│  │  👍 Local SEO Expert             +1  (6/10) │    │
│  │  👍 E-commerce SEO Specialist    +1  (7/10) │    │
│  │  👍 SEO Data Analyst             +1  (9/10) │    │
│  │  👍 International SEO Strategist +1  (8/10) │    │
│  │  👍 SEO Tool Developer           +1  (8/10) │    │
│  │  👍 Agency SEO Director          +1  (9/10) │    │
│  │  👍 Search Engine Researcher     +1  (7/10) │    │
│  │                                             │    │
│  │  JUDICIAL SCORE: 10/10 likes, avg 7.8/10   │    │
│  └─────────────────────────────────────────────┘    │
│                                                     │
│  VERDICT: INCLUDED (unanimous approval)             │
│  Improvement flags: Local relevance could be higher │
└─────────────────────────────────────────────────────┘
```

### Vote Structure

Each persona casts a **two-part vote**:

```json
{
  "voter": "technical-seo-architect",
  "resource_id": "sha256(url)",
  "vote": "LIKE",
  "score": 8,
  "reasoning": "Essential backlink analysis tool, accurate crawl data, good API",
  "improvement_notes": "Documentation could better cover JavaScript rendering limitations",
  "timestamp": "2026-03-26T12:00:00Z",
  "round": 2
}
```

**Vote types:**
| Vote | Meaning | Score Impact |
|------|---------|-------------|
| **LIKE** (+1) | Approve for inclusion, meets quality bar | +1 to approval count, score contributes to average |
| **DISLIKE** (-1) | Does not meet quality bar for this persona's domain | -1 to approval count, triggers discussion |
| **ABSTAIN** (0) | Outside expertise, cannot evaluate meaningfully | No impact on score, reduces quorum requirement |
| **FLAG** (!) | Has a specific issue that MUST be resolved before inclusion | Blocks inclusion until flag is resolved |

---

## The Judicial Score

Every resource accumulates a **judicial score** — a living metric that evolves across review rounds.

### Score Computation

```python
judicial_score = {
    # Approval metrics
    "approval_count": 9,          # Total LIKE votes
    "disapproval_count": 1,       # Total DISLIKE votes
    "abstentions": 0,             # Total ABSTAIN votes
    "flags": 0,                   # Active FLAGS (blocks inclusion)
    "approval_ratio": 0.9,        # approval / (approval + disapproval)

    # Quality metrics (average of all voter scores, 0-10)
    "avg_quality_score": 7.8,     # Mean of all persona scores
    "min_score": 6,               # Lowest persona score (weak spot)
    "max_score": 9,               # Highest persona score (strength)
    "score_variance": 1.2,        # High variance = polarizing resource

    # Composite judicial score (0-100)
    "judicial_score": 78,         # (approval_ratio * 0.4 + avg_quality/10 * 0.4 + freshness * 0.2) * 100

    # Metadata
    "review_rounds": 2,           # How many rounds of review
    "last_reviewed": "2026-03-26",
    "improvement_velocity": 0.3,  # Score change per round (positive = improving)
    "flags_resolved": 2,          # Historical flags that were fixed
    "total_votes_cast": 20        # Across all rounds
}
```

### Composite Formula

```
JUDICIAL_SCORE = (
    approval_ratio    × 0.40   # Community confidence
  + avg_quality / 10  × 0.40   # Depth of quality
  + freshness_score   × 0.20   # Recency of evaluation
) × 100

Where:
  approval_ratio = likes / (likes + dislikes)
  avg_quality    = mean(all persona scores 0-10)
  freshness      = max(0, 1 - (days_since_last_review / 365))
```

### Score Tiers

| Score | Tier | Badge | Meaning |
|-------|------|-------|---------|
| 90-100 | **Platinum** | ⭐⭐⭐ | Exceptional — unanimous high approval, widely recommended |
| 75-89 | **Gold** | ⭐⭐ | Excellent — strong approval, minor improvement areas |
| 60-74 | **Silver** | ⭐ | Good — approved but notable weaknesses identified |
| 40-59 | **Bronze** | — | Marginal — included but with caveats noted |
| 0-39 | **Under Review** | ⚠️ | Does not meet threshold — needs improvement or removal |

---

## How Scores Drive Future Improvement

The judicial score is not just a ranking — it's a **feedback loop** that tells future review cycles where to focus.

### Improvement Signals

```yaml
improvement_priorities:
  # Resources where score DROPPED between rounds
  declining:
    trigger: "improvement_velocity < -0.1"
    action: "Schedule re-evaluation, check for freshness issues"
    example: "Tool X scored 82 in Q1, now 74 — link rot? outdated features?"

  # Resources with high variance (polarizing)
  polarizing:
    trigger: "score_variance > 2.0"
    action: "Investigate why personas disagree, add nuance to description"
    example: "Tool Y: Architect gives 9, Analyst gives 4 — great UX but bad data quality?"

  # Resources with low min_score (weak spot)
  weak_spot:
    trigger: "min_score < 5 AND approval_ratio > 0.7"
    action: "Add caveat to description noting the weakness"
    example: "Tool Z: Loved by everyone except Local Expert (3/10) — 'no local SEO support'"

  # Resources with resolved flags (improved)
  improving:
    trigger: "flags_resolved > 0 AND improvement_velocity > 0.2"
    action: "Celebrate in changelog, may promote to higher tier"
    example: "Tool W: Had broken docs (flagged Q1), now fixed, score 68→79"

  # Categories with low average scores
  category_gaps:
    trigger: "category_avg_score < 60"
    action: "Trigger new research cycle targeting this category"
    example: "International SEO tools avg 52 — need better Baidu/Naver coverage"
```

### The Improvement Loop

```
Round N Scores
      │
      ▼
┌──────────────┐
│ Score Analysis│──▶ Identify: declining, polarizing, weak spots, gaps
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Prioritize    │──▶ Focus Round N+1 on lowest-scoring areas
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Research      │──▶ Find better alternatives, validate improvements
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Re-Vote       │──▶ Updated scores reflect improvements (or further decline)
└──────┬───────┘
       │
       ▼
Round N+1 Scores
       │
       ▼
    (repeat)
```

---

## Score Storage: `data/judicial-scores.json`

```json
{
  "metadata": {
    "version": "1.0.0",
    "scoring_formula": "approval_ratio*0.4 + avg_quality/10*0.4 + freshness*0.2",
    "total_resources_scored": 0,
    "last_round": "2026-Q1-R1",
    "sentiment_setting": -2
  },
  "scores": [
    {
      "resource_id": "sha256(url)",
      "resource_name": "Example Tool",
      "category": "crawlers",
      "current_score": 78,
      "tier": "gold",
      "history": [
        {
          "round": "2026-Q1-R1",
          "score": 72,
          "votes": {
            "likes": 8,
            "dislikes": 1,
            "abstains": 1,
            "flags": 1
          },
          "avg_quality": 7.2,
          "sentiment_setting": -2,
          "improvement_notes": ["Flag: broken documentation link"]
        },
        {
          "round": "2026-Q1-R2",
          "score": 78,
          "votes": {
            "likes": 9,
            "dislikes": 1,
            "abstains": 0,
            "flags": 0
          },
          "avg_quality": 7.8,
          "sentiment_setting": -2,
          "improvement_notes": ["Flag resolved: docs link fixed"]
        }
      ],
      "improvement_velocity": 0.3,
      "flags_active": [],
      "flags_resolved": ["broken-docs-link"]
    }
  ]
}
```

---

## Integration with Myelin8

The judicial score maps directly to Myelin8's **significance scoring**:

```python
# Myelin8 significance = normalized judicial score
significance = judicial_score / 100.0

# High judicial score = resource stays HOT longer
# Low judicial score = resource demotes to COLD faster
# Declining score = triggers re-evaluation before archival
```

This means:
- **Search ranking** is influenced by judicial score (higher score = higher in results)
- **Tier transitions** are score-aware (platinum resources resist compression)
- **Context injection** prioritizes high-scoring resources when building AI context blocks

---

## Integration with Sentiment Slider

The sentiment slider (from research-protocol.md) affects voting behavior:

| Slider | Vote Threshold for Inclusion | Flag Resolution Required |
|--------|------------------------------|--------------------------|
| -5 (max skeptical) | >90% LIKE + avg score >8 | ALL flags must be resolved |
| -2 (default) | >66% LIKE + avg score >6 | Critical flags resolved |
| 0 (neutral) | >50% LIKE + avg score >5 | No active FLAGS |
| +2 (generous) | >40% LIKE + avg score >4 | FLAGS noted but don't block |
| +5 (max generous) | Any LIKE + no DISLIKES | FLAGS are advisory only |

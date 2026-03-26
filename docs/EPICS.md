# ClawFish Epics

## PRIORITY EPIC: Citation Ranking System (Krabby Patty Formula)

**Status:** NOT STARTED — MUST BUILD FIRST
**Priority:** P0 — This is ClawFish's core differentiator

### Problem

AI agents hallucinate citations. Working solo, you can't vet everything. Terminal workflows are fast but blind — no reputation signal like Google's ranking algorithm provides.

### Solution

A multi-signal verification system that cross-references every citation/resource against real-world reputation:

| Signal | Source | What It Tells You |
|--------|--------|-------------------|
| Domain Authority | Moz API | Overall site credibility |
| Domain Rating | Ahrefs API | Backlink-based authority |
| Global Rank | Similar Web, Alexa archives | Traffic-based popularity |
| Domain Age | WHOIS | Longevity = trust signal |
| Citation Count | Semantic Scholar, Google Scholar | Academic credibility |
| Social Proof | GitHub stars, Twitter followers | Community validation |
| Content Freshness | Last-Modified headers, sitemap dates | Is this still maintained? |
| SSL/Security | Certificate check | Basic trust signal |

### Output

Every resource in ClawFish gets:
- **Reputation Score (0-100)** — weighted composite of all signals
- **Confidence Flag** — VERIFIED / PLAUSIBLE / SUSPICIOUS / FABRICATED
- **Signal Breakdown** — which signals passed, which failed, which were unavailable

### Architecture

```
Citation URL
    ↓
Signal Collectors (parallel)
├── SEO Rank Checker (Moz, Ahrefs, Similar Web)
├── Academic Checker (Semantic Scholar, Google Scholar)
├── Social Checker (GitHub API, social counts)
├── Domain Checker (WHOIS age, SSL, DNS)
└── Content Checker (freshness, 404 detection)
    ↓
Score Aggregator (weighted formula)
    ↓
Confidence Classification
    ↓
Quorum Review (personas debate borderline cases)
```

### Open Source Decision

TBD — Framework open, weights proprietary? Or full open for community trust?

Neither MiroFish nor OpenClaw does this. This is the moat.

---

## Epic 2: Marketing Strategy Engine

**Status:** NOT STARTED
**Priority:** P1

AI-powered outreach targeting with success probability scoring.

---

## Epic 3: Persona Library

**Status:** NOT STARTED
**Priority:** P2

Curated persona roster for Quorum integration. Based on notable archetypes.

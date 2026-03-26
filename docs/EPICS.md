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

---

## Epic 4: Agent Review Audit Trail

**Status:** NOT STARTED
**Priority:** P1 — Required for Congress to be trustworthy

### What It Is

A full audit trail system that logs every AI agent interaction and surfaces it visually in the UI and on GitHub like real human code reviewers.

### Requirements

**Database Layer:**
- Every agent action logged: agent_id, persona_name, role, department, action (vote_up/vote_down/comment/review/flag), target (resource/document/stage), timestamp, reasoning
- Schema supports version history — see how votes changed over time
- Queryable: "show me everything VP Security reviewed" or "which resources had split votes"

**UI Layer:**
- Per-document/resource: show which agents reviewed it, their vote, their role/dept
- Visual indicators: thumbs up/down with agent avatar/color, timestamp
- Review count badge: "Reviewed 3x by 2 teams"
- Expandable comment threads per agent
- Filter by: team, persona, vote direction, time period

**GitHub Integration:**
- Agent reviews post as GitHub PR review comments (using GitHub API)
- Threaded discussions on specific lines/sections
- Agents show as real reviewers with their persona name
- Version-controlled: every review round is a commit or PR comment thread

**Agent Identity:**
- Each agent has: id, persona_name, display_name, role, department (from org chart), avatar_color, Census Genome traits
- Comments are signed: "— Dr. Sarah Chen, VP Security / Technical SEO Architect (Skepticism: 8/10)"

### Architecture

```
Agent Action
    ↓
Action Logger (writes to audit DB)
    ↓
┌─────────────────────────────┐
│ audit.db (SQLite)           │
│ ├── reviews table           │
│ ├── votes table             │
│ ├── comments table          │
│ └── agent_identities table  │
└─────────────────────────────┘
    ↓                    ↓
UI Dashboard          GitHub API
(visual audit trail)  (PR review comments)
```

### Database Schema

```sql
-- Agent identities
CREATE TABLE agents (
  id TEXT PRIMARY KEY,
  persona_name TEXT NOT NULL,
  role TEXT,
  department TEXT,
  avatar_color TEXT,
  traits JSON,
  created_at DATETIME
);

-- Reviews
CREATE TABLE reviews (
  id TEXT PRIMARY KEY,
  agent_id TEXT REFERENCES agents(id),
  target_type TEXT, -- 'resource' | 'document' | 'stage' | 'epic'
  target_id TEXT,
  action TEXT, -- 'approve' | 'request_changes' | 'comment' | 'flag'
  vote INTEGER, -- -1, 0, +1
  score REAL, -- 0-10 quality score
  reasoning TEXT,
  round_id TEXT,
  github_comment_id TEXT, -- if synced to GitHub
  created_at DATETIME
);

-- Comments (threaded)
CREATE TABLE comments (
  id TEXT PRIMARY KEY,
  review_id TEXT REFERENCES reviews(id),
  agent_id TEXT REFERENCES agents(id),
  parent_comment_id TEXT REFERENCES comments(id),
  body TEXT,
  created_at DATETIME
);
```

### What This Enables

- "Show me why VP Security voted down this resource" → click → see reasoning + traits + department context
- "Which stages had unanimous approval?" → filter → see green checkmarks across all agents
- "What changed between Round 1 and Round 2?" → version diff of votes and comments
- Kevin's own reviews show alongside AI agents — same system, same UI
- GitHub PR shows full agent deliberation as review comments — looks like a real team reviewed it

---

## PRIORITY EPIC: Quorum Congress

**Status:** IN PROGRESS — DESIGNING
**Priority:** P0 — This is how the entire org operates

### What It Is

A full organizational simulation through AI personas. Not just Quorum reviewing one resource — an entire company operating through statistically diverse personas with governance, feedback loops, and public participation.

### The Process (7 stages)

```
┌─────────────────────────────────────────────────────────────────────┐
│                        QUORUM CONGRESS                              │
│                                                                     │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐     │
│  │ 1. MECE  │───→│ 2. DEEP  │───→│ 3. DELPHI│───→│ 4. SCRIBE│     │
│  │ PARTITION│    │ RESEARCH │    │ REVIEW   │    │ DOCUMENT │     │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘     │
│       │                                                │           │
│       │          Anti-collision: no duplicate work      │           │
│       │                                                │           │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐         │           │
│  │ 7. FEED  │←───│ 6. PUBLIC│←───│ 5. GOVERN│←────────┘           │
│  │ BACK LOOP│    │ VOTE     │    │ COUNCIL  │                     │
│  └──────────┘    └──────────┘    └──────────┘                     │
│       │                                                            │
│       └────────────────→ Back to Stage 1 (next cycle) ────────────→│
└─────────────────────────────────────────────────────────────────────┘
```

### Stage Definitions

| # | Stage | Name | What Happens | Who |
|---|-------|------|-------------|-----|
| 1 | **MECE Partition** | Research Delegation | Work is divided into non-overlapping zones. No two personas research the same thing. Anti-collision enforced. | VP PM |
| 2 | **Deep Research** | Independent Discovery | Each persona researches their partition independently. Produces findings + citations + confidence. | Subteam personas |
| 3 | **Delphi Review** | Cross-Functional Lateral Review | Every subteam shares findings. Personas from OTHER teams review laterally. Structured rounds until convergence. | All personas cross-team |
| 4 | **Scribe** | Documentation | A dedicated scribe persona captures every decision, disagreement, vote, and rationale. Nothing is lost. | Scribe persona |
| 5 | **Governing Council** | External Oversight | Wikipedia/Reddit-style moderators audit process integrity, neuromodesty, ethics. Three councils: Governance, Neuromodest, Ethics. | Council personas |
| 6 | **Public Vote** | Audience Participation | Real users vote, like, comment. Feedback feeds back into system. Kevin's own likes/comments on the UI count too. | Humans (audience + Kevin) |
| 7 | **Feedback Loop** | Cycle Improvement | Public votes + Kevin's UI interactions + council findings → adjust weights, re-evaluate low-confidence items, trigger new research. Loop back to Stage 1. | System + Kevin |

### The Feedback Loop (Stage 7 → Stage 1)

This is the critical missing piece. The cycle never ends:

```
Public votes (likes/dislikes on resources)
    + Kevin's UI comments and ratings
    + Council audit findings
    + Declining reputation scores (Krabby Patty Formula)
    + New resources discovered by crawlers
        ↓
    TRIGGER: New research cycle
        ↓
    Back to Stage 1 (MECE re-partition for changed areas only)
```

**Kevin's likes and comments on the UI ARE data.** They feed into significance scoring, trigger re-evaluations, and shape what gets prioritized in the next cycle.

### Persona Trait System (Sims-Style)

Every persona has traits distributed on bell curves to match real population statistics:

| Trait | Range | Distribution | What It Affects |
|-------|-------|-------------|----------------|
| **IQ** | 70-145 | Normal (μ=100, σ=15) | Analytical depth, complexity of reasoning |
| **EQ** | 1-10 | Normal (μ=5, σ=1.5) | Empathy in reviews, user-perspective awareness |
| **Patience** | 1-10 | Normal (μ=5, σ=2) | Thoroughness vs speed, tolerance for ambiguity |
| **Creativity** | 1-10 | Normal (μ=5, σ=2) | Novel solutions, lateral thinking, unconventional picks |
| **Risk Tolerance** | 1-10 | Normal (μ=5, σ=2) | Willingness to recommend unproven tools |
| **Detail Orientation** | 1-10 | Normal (μ=5, σ=2) | Catches errors vs sees big picture |
| **Skepticism** | 1-10 | Normal (μ=5, σ=2) | Default trust vs default doubt |
| **Domain Depth** | 1-10 | Bimodal (specialists + generalists) | Deep expertise vs cross-domain breadth |
| **Communication** | 1-10 | Normal (μ=6, σ=1.5) | Clarity of reasoning, persuasiveness in debate |
| **Bias Awareness** | 1-10 | Right-skewed (μ=4, σ=2) | Self-awareness about own blind spots |

**Population rules:**
- Generate N personas → traits sampled from distributions
- Verify population % matches expected bell curves
- No two personas have identical trait profiles
- Extremes (top/bottom 5%) are rare but present — they're the contrarians and outliers that prevent groupthink

### What Public Voting Is Useful For

| Audience Action | What It Feeds |
|----------------|--------------|
| **Upvote a resource** | Increases significance score, validates Quorum's evaluation |
| **Downvote a resource** | Triggers re-evaluation cycle, may lower confidence flag |
| **Comment with new info** | Gets ingested as evidence for next Delphi round |
| **Flag as outdated/broken** | Triggers immediate dead-link check + freshness decay |
| **Suggest new resource** | Enters Stage 1 pipeline for next cycle |
| **Kevin's UI likes** | Weighted higher (founder signal), directly adjusts significance |
| **Kevin's UI comments** | Treated as directive — triggers targeted research |

### Naming

| Component | Name |
|-----------|------|
| The full system | **Quorum Congress** |
| Stage 1 | MECE Partition |
| Stage 2 | Deep Research |
| Stage 3 | Delphi Review (cross-functional lateral review) |
| Stage 4 | Scribe Protocol |
| Stage 5 | Governing Council |
| Stage 6 | Public Vote |
| Stage 7 | Feedback Loop |
| The skill invocation | `/quorum --congress` |
| Persona trait system | **Census Genome** (Sims-style trait DNA for each persona) |

---
name: reddit-trend-hunter
description: >
  Finds the newest relevant Reddit posts matching a tracked topic database, then surfaces the best threads for Arrays.org (financial data API for AI agents) to engage with. Use this skill whenever the user asks to find new Reddit posts, update Reddit trends, scan for engagement opportunities, or refresh the Notion Reddit tracking database. Also trigger when the user says things like "find new threads", "what's trending on Reddit", "update our Reddit list", "scout Reddit for ICP posts", or "find posts to reply to". This skill reads a Notion page with past Reddit posts to learn the topic fingerprint, then hunts for fresh threads across r/fintech, r/algotrading, r/SaaS, r/LLMDevs, r/MachineLearning, and adjacent subreddits.
---

# Reddit Trend Hunter — Arrays.org Edition

## What This Skill Does

This skill automates the Reddit research workflow for Arrays.org: it reads the existing Notion tracking database to understand what topics are resonating, then uses Apify to directly scrape Reddit for fresh threads with full post body + comments. It outputs a formatted table of new posts ready to add to Notion.

---

## Configuration

```
APIFY_TOKEN = YOUR_APIFY_TOKEN
APIFY_ACTOR = YOUR_APIFY_ACTOR_ID
APIFY_BASE_URL = https://api.apify.com/v2/acts/YOUR_APIFY_ACTOR_ID
```

**Run mode:** Use the synchronous endpoint so results come back in the same call — no polling needed for typical scrapes (<50 posts):
```
POST {APIFY_BASE_URL}/run-sync-get-dataset-items?token={APIFY_TOKEN}&timeout=120
```

---

## Arrays.org Context

Arrays.org is a **financial data API built for AI agents**. It lets developers give AI agents safe, bounded access to financial data — no raw SQL, pre-defined endpoints only, predictable outputs. This limits blast radius from misbehaving agents and makes financial data AI-safe by design.

**Target audience (ICP):**
- Developers/founders building AI-powered fintech products or features
- SaaS founders struggling with AI inference cost and unit economics
- Quant/algo traders building Python models who need affordable market data
- Fintech teams deploying GenAI who need governance + compliance guardrails
- Anyone giving AI agents access to sensitive financial data

**Core pain points Arrays.org addresses:**
1. Unsafe AI agent access to financial databases (compliance risk, data exposure)
2. No unified cost tracking across AI/data APIs (fragmented billing)
3. Flat-rate SaaS pricing broken by variable AI per-query costs
4. Affordable, production-grade financial data for developers (not just institutions)
5. GenAI governance gaps in fintech (auditability, explainability, FINRA compliance)
6. AI commoditizing the build layer → proprietary data becomes the moat

**What Arrays does NOT provide (as of March 2026):**
- Social sentiment data
- Prediction market data (Kalshi, Polymarket) — under consideration, not live
Do not claim these in replies. Acknowledge interest + note it's on the roadmap.

---

## Step-by-Step Workflow

### Step 1 — Read the Notion Database

Fetch the Notion page provided by the user (or use the default):
`https://www.notion.so/YOUR_WORKSPACE/YOUR_NOTION_PAGE_ID`

Extract from the page:
- **Subreddits** being tracked
- **Pain point themes** (the "Pain Point" column)
- **Customer insights** (the "Customer Insight" column)
- **Reply patterns** that worked (tone, length, disclosure style)

This is your "topic fingerprint" — what's resonating with Arrays.org's ICP.

---

### Step 2 — Build Apify Scrape Jobs

Generate scrape jobs across four buckets. Run all four every week in parallel using `Bash`.

#### Bucket A — Primary subreddits (new posts feed)

Scrape the `/new/` feed for each primary subreddit to catch everything posted in the last 30 days:

```bash
# Run for each subreddit: algotrading, fintech, SaaS, LLMDevs, mcp, ClaudeAI, ClaudeCode, quantfinance, options, ai_trading
curl -s -X POST \
  "https://api.apify.com/v2/acts/YOUR_APIFY_ACTOR_ID/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN&timeout=120" \
  -H "Content-Type: application/json" \
  -d '{
    "startUrls": [{"url": "https://www.reddit.com/r/algotrading/new/"}],
    "maxItems": 30,
    "skipComments": false,
    "maxComments": 5,
    "proxy": {"useApifyProxy": true}
  }'
```

#### Bucket B — MCP / Model Context Protocol keyword search

```bash
curl -s -X POST \
  "https://api.apify.com/v2/acts/YOUR_APIFY_ACTOR_ID/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN&timeout=120" \
  -H "Content-Type: application/json" \
  -d '{
    "startUrls": [
      {"url": "https://www.reddit.com/search/?q=financial+data+MCP+server&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=market+data+MCP&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=Model+Context+Protocol+financial&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=SEC+MCP+earnings+MCP&sort=new&t=month"}
    ],
    "maxItems": 20,
    "skipComments": false,
    "maxComments": 5,
    "proxy": {"useApifyProxy": true}
  }'
```

#### Bucket C — Vendor replacement / incumbent pain

```bash
curl -s -X POST \
  "https://api.apify.com/v2/acts/YOUR_APIFY_ACTOR_ID/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN&timeout=120" \
  -H "Content-Type: application/json" \
  -d '{
    "startUrls": [
      {"url": "https://www.reddit.com/search/?q=Polygon.io+alternative&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=Alpha+Vantage+alternative&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=Quandl+replacement&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=Bloomberg+API+alternative+developer&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=yfinance+alternative+production&sort=new&t=month"}
    ],
    "maxItems": 20,
    "skipComments": false,
    "maxComments": 5,
    "proxy": {"useApifyProxy": true}
  }'
```

#### Bucket D — Claude skills / AI agent financial tools

```bash
curl -s -X POST \
  "https://api.apify.com/v2/acts/YOUR_APIFY_ACTOR_ID/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN&timeout=120" \
  -H "Content-Type: application/json" \
  -d '{
    "startUrls": [
      {"url": "https://www.reddit.com/search/?q=Claude+financial+data+tool+MCP&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=AI+agent+financial+API+production&sort=new&t=month"},
      {"url": "https://www.reddit.com/search/?q=vibecoding+trading+bot+data&sort=new&t=month"}
    ],
    "maxItems": 20,
    "skipComments": false,
    "maxComments": 5,
    "proxy": {"useApifyProxy": true}
  }'
```

> **Fallback:** If Apify returns an error or zero results, fall back to `WebSearch` using the query buckets from the Keyword Reference section below. WebSearch gives snippets only — no post body or comments.

---

### Step 3 — Parse and Filter Apify Results

Each Apify response is a JSON array. For each post object, extract:

| Field | Apify key | Notes |
|-------|-----------|-------|
| Title | `title` | |
| URL | `url` | Must match `/r/.../comments/.../` format |
| Post body | `body` or `selftext` | Full text — use for Customer Insight |
| Upvotes | `score` | Keep posts with score ≥ 2 |
| Comments | `comments[].body` | Top 5 — reveals real pain, not just title |
| Posted date | `createdAt` or `created_utc` | Convert epoch to date if needed |
| Subreddit | `subredditName` | |

**Apply these filters:**

**URL filter** — Keep ONLY `reddit.com/r/<sub>/comments/<id>/<title>/` format. Discard search result pages.

**Date filter — STRICT 30-day window** — Use `createdAt` from Apify (exact timestamp, no guessing). Discard anything older than 30 days. No exceptions.

**Quality filter** — Discard posts with `score < 2` AND `numComments < 2` — too low-signal.

Aim for 5–10 confirmed posts. Quality over quantity.

---

### Step 4 — Score for Relevance

For each post, read the **full body + top comments** (now available via Apify) and score 1–3:

**3 — High value**: Body or comments describe a pain point Arrays.org directly solves — financial API fragmentation, broker API pain, data normalization cost, AI agent data access, MCP financial tools. Ideal for a value-add reply.

**2 — Medium value**: Adjacent topic — financial data in general, AI in fintech, algo trading tools. Worth engaging.

**1 — Low value**: Tangentially related. Drop unless nothing better.

Keep 3s and 2s. Discard 1s.

---

### Step 5 — Generate the Report

Output the report in this exact format:

---

**Daily Reddit Intelligence — [Day, Month Date]**

Assign each post to one of these categories. Use only categories that have qualifying posts — skip empty ones.

**Categories:**
- **Competitor Signal** — posts discussing competitor tools, pricing pain, or switching intent (Polygon.io, Alpha Vantage, yfinance, Bloomberg API, etc.)
- **Category Narrative** — broader market/narrative shifts relevant to financial data APIs, AI agents, or MCP tooling
- **ICP Pain** — posts where users describe pain points Arrays.org directly solves (API fragmentation, cost, agent data access, governance)
- **Partner Opportunity** — posts signaling a potential integration, ecosystem, or co-marketing angle
- **Engagement Opportunity** — high-quality threads where a value-add reply would build credibility with the ICP

For each post under its category:

```
• "[Most signal-rich quote from the post body or a top comment, verbatim]" — u/[username]
Why it matters: [2–3 sentences on the strategic implication for Arrays.org. Frame it around product positioning, narrative, or GTM — not just what the post says.]
Link: [full reddit.com/r/.../comments/... URL]
```

Close the report with:

**GTM Takeaways**
• [Synthesized bullet — a pattern or action Arrays.org should act on based on this week's posts]
• [Repeat for 2–4 total bullets. Focus on what to do or say differently, not just observations.]

---

**Ready-to-Go Replies** (separate section after the intelligence report)

For each Engagement Opportunity post, write a reply:
- Opens by engaging with the specific point raised in the post body or a top comment
- Adds genuine value (answer, pattern, validation)
- Does NOT lead with a pitch
- Ends with soft disclosure: *"Disclosure: I'm building Arrays at arrays.org. [One sentence on relevance]."*
- Tone: technical, peer-to-peer, no hype. 200–400 words.
- **Do not claim** Arrays provides social sentiment or prediction market data. If relevant, say it's on the roadmap.

---

### Step 6 — Update Notion (Optional)

If the user confirms, add confirmed entries to the Notion database:

1. Fetch collection `collection://YOUR_COLLECTION_ID` to confirm schema
2. Use `notion-create-pages` with: Title, Link, Content (post body summary), Category, Customer insight, Reply, Submission Date
3. Confirm each post was added

---

## Output Format

**1. Quick summary** — how many posts found, date range, top themes, Apify vs fallback

**2. Daily Reddit Intelligence report** — categorized entries (Competitor Signal / Category Narrative / ICP Pain / Partner Opportunity / Engagement Opportunity) with verbatim quote, u/handle, Why it matters, and link — followed by GTM Takeaways bullets

**3. Ready-to-Go Replies** — one reply per Engagement Opportunity post

---

## Subreddit Target List

Primary (scrape new feed every run):
- r/fintech
- r/algotrading
- r/SaaS
- r/LLMDevs
- r/mcp (MCP protocol + financial data — fast-growing ICP, promoted to primary)
- r/ClaudeAI / r/ClaudeCode (Claude tool builders — MCP-native audience)

Secondary (add to keyword search when primary is thin):
- r/MachineLearning
- r/LocalLLaMA
- r/investing
- r/quantfinance
- r/options
- r/devops
- r/ExperiencedDevs
- r/vibecoding (vibe coders building trading tools)
- r/ai_trading
- r/personalfinance

---

## Keyword Reference

Use these when building ad-hoc search URLs or extending Bucket queries.

**MCP / Model Context Protocol:**
`MCP server`, `MCP client`, `Model Context Protocol`, `financial MCP`, `market data MCP`, `SEC MCP`, `earnings MCP`, `options MCP`, `MCP tool financial`, `MCP agent trading`

**Claude skills/tools:**
`Claude skill financial`, `Claude tool use market data`, `Claude MCP financial agent`, `Claude Code financial API`, `Claude tool financial data`

**Incumbent vendors (replacement/pain signals):**
| Vendor | Watch for |
|--------|-----------|
| Polygon.io | `alternative`, `rate limit`, `too expensive` |
| Alpha Vantage | `alternative`, `unreliable`, `rate limit` |
| Quandl | `replacement`, `discontinued`, `expensive` |
| Bloomberg | `API alternative`, `too expensive`, `developer` |
| Refinitiv/LSEG | `alternative`, `data API developer` |
| yfinance | `alternative`, `unreliable`, `production` |

**Core pain point phrases (quote for precision):**
`"financial data API"`, `"market data API"`, `"normalized financial data"`, `"point-in-time data"`, `"AI agent financial"`, `"MCP financial"`, `"unified financial API"`, `"data provider alternative"`

---

## WebSearch Fallback Queries

Use only if Apify fails. These return snippets only — no post body or comments.

```
reddit r/fintech "AI agents" financial data security 2026
reddit r/SaaS AI feature cost per query unit economics
reddit r/algotrading financial API affordable developer
reddit r/LLMDevs database access AI agent production
reddit r/mcp "financial data" MCP server 2026
reddit r/mcp market data MCP server client
reddit "financial MCP" site:reddit.com
reddit "market data MCP" site:reddit.com
reddit "Polygon.io" alternative OR expensive site:reddit.com
reddit "Alpha Vantage" alternative OR unreliable site:reddit.com
reddit "Quandl" replacement site:reddit.com
reddit "Bloomberg API" alternative developer site:reddit.com
reddit r/ClaudeAI Claude MCP financial data trading
reddit r/ClaudeCode Claude financial API MCP tool
```

---

## Tips

- Run Bucket A (subreddit new feeds) and Buckets B–D (keyword searches) in parallel via multiple `Bash` calls
- If Apify times out, reduce `maxItems` to 15 and retry
- Post body and top comments are now the primary source for Customer Insight — use them, don't just read the title
- Post ID recency guide (WebSearch fallback only): IDs `1r`–`1t` = early 2026; `1h`–`1i` = late 2024

---

## Example Run

**User**: "Find new Reddit posts for Arrays.org"

**Claude should**:
1. Fetch the Notion page for topic fingerprint
2. Fire 4 Apify scrape jobs in parallel (one per bucket)
3. Parse JSON results — read full body + comments for each post
4. Apply: URL filter + 30-day date filter (exact from Apify) + quality filter (score ≥ 2)
5. Score top 5–8 posts by relevance
6. Write report: Daily Reddit Intelligence (categorized entries with quotes, Why it matters, links) + GTM Takeaways + Ready-to-Go Replies
7. Ask: "Want me to add these to Notion?"

**What NOT to do**:
- Never put a search URL in the Link column
- Never include posts older than 30 days
- Never claim Arrays provides prediction market or social sentiment data
- Never add signal-based entries to Notion without a confirmed direct URL

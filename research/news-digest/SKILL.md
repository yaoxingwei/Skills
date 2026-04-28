---
name: news-digest
description: "Automated news intelligence digest: collect Trump, Musk, politics, economy, AI models, and semiconductor news via Google News RSS, maintain a markdown wiki knowledge base, and deliver to Telegram."
version: 1.0.0
author: yaoxw
license: MIT
metadata:
  hermes:
    tags: [news, digest, trump, musk, ai, semiconductor, wiki, telegram, cron]
    category: research
---

# News Digest — 新闻情报速递

Automated bilingual (CN/EN) news collection and delivery system. Pulls latest news from Google News RSS feeds, maintains a structured wiki knowledge base at `~/wiki/`, and delivers daily digests to Telegram.

## Coverage (6 Topics)

| Topic | Keywords | Emoji |
|-------|----------|-------|
| Donald Trump | president, policy, election, Iran, assassination | 🔴 |
| Elon Musk | Tesla, SpaceX, xAI, OpenAI trial, DOGE | 🔵 |
| US-China Politics | tariffs, trade war, diplomacy, geopolitics | 🟡 |
| Global Economy | Fed, stock market, crypto, tariffs impact | 🟡 |
| AI / LLMs | GPT, Claude, Gemini, DeepSeek, open-source AI | 🟢 |
| Semiconductors | NVIDIA, TSMC, ASML, export controls, GPU | 🟢 |

## Architecture

```
~/wiki/                          # Knowledge base (Karpathy-style LLM Wiki)
├── SCHEMA.md                    # Conventions and tag taxonomy
├── index.md                     # Content catalog
├── log.md                       # Chronological action log
├── raw/articles/                # Immutable source feeds
├── entities/                    # Entity pages (Trump, Musk, NVIDIA, etc.)
├── concepts/                    # Topic/concept pages
├── digests/                     # Daily digest pages (YYYY-MM-DD-digest.md)
├── comparisons/                 # Side-by-side analyses
└── queries/                     # Saved query results

~/.hermes/skills/research/news-digest/   # This skill
```

## Cron Setup

Schedule: `0 9,19 * * *` (daily at 09:00 and 19:00 Beijing time)

```bash
hermes cron create "0 9,19 * * *" \
  --name "News Digest — Trump, Musk, AI, Chips" \
  --skill llm-wiki \
  --skill news-digest \
  --deliver telegram:chong
```

## Collection Workflow

### 1. Fetch Raw Feeds

Use Google News RSS with curl:

```bash
curl -s "https://news.google.com/rss/search?q=Trump+president&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=Elon+Musk&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=Nvidia+AI+chips+semiconductor&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=China+trade+tariffs+economy&hl=en-US&gl=US&ceid=US:en"
```

Parse with Python xml.etree.ElementTree to extract: title, pubDate, source, link.

### 2. Save Raw Sources

Save to `~/wiki/raw/articles/<topic>-YYYY-MM-DD.md` with frontmatter:
```yaml
---
source_url: <rss_url>
ingested: YYYY-MM-DD
---
```

### 3. Update Entity Pages

For each entity appearing in 2+ stories, create or update its page:
- `~/wiki/entities/donald-trump.md`
- `~/wiki/entities/elon-musk.md`
- `~/wiki/entities/nvidia.md`
- etc.

Each entity page has: overview, key facts, recent events (with dates), relationships to other entities via `[[wikilinks]]`.

### 4. Create Daily Digest

Write `~/wiki/digests/YYYY-MM-DD-digest.md` with:
- Top 5-8 stories per category
- Headline, source, date for each
- Key themes / cross-cutting storylines

### 5. Update Navigation

- Add/update entries in `~/wiki/index.md`
- Append to `~/wiki/log.md`: `## [YYYY-MM-DD] ingest | topic batch`

### 6. Deliver to Telegram

Send a concise summary via `send_message` to `telegram:chong`:

```
📰 新闻情报速递 | YYYY-MM-DD

🔴 TRUMP
- key point 1
- key point 2

🔵 MUSK
- key point 1
- key point 2

🟡 政治/经济
- key point 1

🟢 AI/芯片
- key point 1
- key point 2

Wiki: ~/wiki/
```

## Tag Taxonomy

- **人物:** trump, musk, xi-jinping, biden, powell, huang-renxun, sam-altman
- **组织:** tesla, spacex, openai, google, meta, nvidia, tsmc, asml, xai, anthropic, microsoft
- **主题:** tariff, trade-war, election, diplomacy, fed, crypto, iran-war, assassination
- **技术:** llm, agi, gpu, semiconductor, export-control, open-source-ai, deepseek, reasoning, tpu
- **元数据:** comparison, timeline, prediction, controversy

## Pitfalls

- Google News RSS `after:` filter is unreliable — fetch full feed and filter by pubDate in Python
- Telegram delivery may time out on unstable networks — retry once after gateway restart
- WSL environment needs `systemd=true` in `/etc/wsl.conf` for gateway service to survive terminal close
- Always follow SCHEMA.md conventions when creating/updating wiki pages
- Frontmatter `contested: true` for articles with conflicting claims
- Don't create entity pages for single-mention topics (threshold: 2+ sources)

## Dependencies

- curl (system)
- Python 3 with xml.etree.ElementTree (stdlib)
- Hermes cronjob tool
- Hermes send_message tool (Telegram platform configured)
- `~/wiki/SCHEMA.md` must exist (created during wiki initialization)
---
name: news-wiki
description: "Set up and run automated news intelligence wikis with cron delivery to Telegram, including portfolio/stock tracking"
version: 1.1.0
author: yaoxw
metadata:
  hermes:
    tags: [news, wiki, rss, telegram, cron, digest, portfolio]
    category: research
    related_skills: [llm-wiki, news-digest, hermes-agent]
---

# News Intelligence Wiki

Set up a persistent news aggregation wiki that collects articles from RSS feeds (Google News), organizes them by topic and portfolio holdings, and delivers periodic digests to Telegram.

## Architecture

```
wiki/
├── SCHEMA.md           # Domain conventions + tag taxonomy
├── index.md            # Content catalog
├── log.md              # Operation log
├── watchlist.md        # Stock watchlist (portfolio holdings)
├── raw/articles/       # Raw RSS feeds (immutable)
├── entities/           # People/orgs + portfolio stock pages
├── concepts/           # Topics
├── digests/            # Daily summaries (YYYY-MM-DD-digest.md)
├── comparisons/        # Side-by-side analyses
└── queries/            # Filed answers
```

**Core principle:** Raw sources are immutable. Wiki pages are agent-maintained. Digests are the deliverable.

## Step 1: Collect News Sources

### Google News RSS (primary — no auth needed)

```bash
curl -s "https://news.google.com/rss/search?q=QUERY&hl=en-US&gl=US&ceid=US:en"
```

**Critical: use regex for parsing, NOT XML parser.** Google News RSS contains `&amp;` etc. that break `ET.fromstring()`.

```python
import re, html, datetime

blocks = re.split(r'<item[^>]*>', xml_str, flags=re.IGNORECASE)
for block in blocks[1:]:
    title_m = re.search(r'<title[^>]*>(.*?)</title>', block, re.DOTALL | re.IGNORECASE)
    # ... extract link, pubDate, source similarly
    
    # Date parse: strip timezone suffix
    clean = re.sub(r' [A-Z]{2,4}$', '', pubdate_raw)
    parsed = datetime.datetime.strptime(clean, '%a, %d %b %Y %H:%M:%S')
```

**Key pitfalls:**
- `after:` query param unreliable → filter dates in Python
- **Chinese queries: use en-US locale** — zh-CN feeds return 0 results
- Topic classification: use source feed as primary signal, not keyword matching

## Step 2: Portfolio / Stock Tracking

For portfolio/stock holdings, create `~/wiki/watchlist.md`:

```markdown
# 自选股 Watchlist
> 最后更新: YYYY-MM-DD

| 股票代码 | 名称 | 市场 | 添加日期 |
|----------|------|------|----------|
| 000063.SZ | 中兴通讯 | A股 | 2026-04-29 |
```

### Adding a stock:
1. Append row to watchlist.md table
2. Create `entities/<slug>-holding.md` with frontmatter + Key Facts + risks + wikilinks
3. Update SCHEMA.md Portfolio domain + tags
4. Update index.md Portfolio Holdings section
5. Log to log.md

### Removing a stock:
1. Delete row from watchlist.md
2. Archive/delete corresponding entity page
3. Update SCHEMA.md + index.md + log.md

### Stock news collection:
- Read watchlist.md → generate per-stock RSS queries (en-US)
- Each stock gets its own raw feed file: `raw/articles/<slug>-YYYY-MM-DD.md`
- Append to digest: `## 💰 持仓分析与买卖建议` with 3-dimension table + forecast + advice
- Telegram summary includes brief holding analysis

## Step 3: Send to Telegram

Concise Chinese-language digest, ~1000 chars. Format:

```
📰 新闻速递 | YYYY年MM月DD日
🔴 特朗普 • 摘要...
🔵 马斯克 • 摘要...
💼 持仓快报 • 各股建议...
完整分析: ~/wiki/digests/...
```

If Telegram times out in WSL/China: restart gateway, wait 10s, retry.

## Step 4: Cron Setup

```
schedule: 0 8,20 * * * (twice daily, Beijing time)
deliver: telegram:chong
skills: llm-wiki, news-digest
```

## Pitfalls Summary

- Parse RSS with regex, never XML parser
- en-US locale for all queries (especially Chinese stock names)
- Filter dates client-side (not in query params)
- Classify by source feed, not keyword matching
- Always update index + log after any wiki change
- Raw sources are immutable — edit wiki pages only
---
name: news-digest
description: "中文新闻情报速递：每日自动收集特朗普、马斯克、政治、经济、AI大模型、芯片半导体新闻+自选股分析，存入wiki知识库并推送到Telegram。"
version: 2.2.0
author: yaoxw
license: MIT
metadata:
  hermes:
    tags: [news, digest, trump, musk, ai, semiconductor, wiki, telegram, cron, chinese, portfolio]
    category: research
---

# 新闻情报速递 (News Digest)

中英双语新闻自动收集与推送系统。通过 Google News RSS 抓取最新新闻，存入结构化 Wiki 知识库，并通过 Telegram 定时推送。

## 主题覆盖

### 六大宏观主题

| 主题 | 关键词 | 标识 |
|------|--------|------|
| 特朗普 | 发言、政策、选举、伊朗战争、法律案件 | 🔴 |
| 马斯克 | Tesla、SpaceX、xAI、X、OpenAI庭审 | 🔵 |
| 政治/地缘 | 中美关系、关税、贸易战、外交、国际政要 | 🔶 |
| 经济/市场 | 美联储、美股、加密货币、关税影响 | 🔶 |
| AI大模型 | GPT、Claude、Gemini、DeepSeek、开源模型、AGI | 🟢 |
| 芯片半导体 | 英伟达、台积电、ASML、出口管制、GPU | 🟢 |

### 自选股管理（第七主题）

自选股列表维护在 `~/wiki/watchlist.md`，格式如下：

```markdown
# 自选股 Watchlist

> 最后更新: YYYY-MM-DD

| 股票代码 | 名称 | 市场 | 添加日期 |
|----------|------|------|----------|
| 000063.SZ | 中兴通讯 | A股 | 2026-04-29 |
| 600702.SH | 舍得酒业 | A股 | 2026-04-29 |
| 09988.HK | 阿里巴巴 | 港股 | 2026-04-29 |
| 03690.HK | 美团 | 港股 | 2026-04-29 |
| — | 壁仞科技 | 港股 | 2026-04-29 |
```

**添加自选股（用户说"加仓"或"加自选"）：**
1. 在 `~/wiki/watchlist.md` 表格中追加一行
2. 更新 `~/wiki/SCHEMA.md` 的 Portfolio domain 描述
3. 为该股票创建 `~/wiki/entities/<slug>-holding.md`
4. 更新 `~/wiki/index.md` 的 Portfolio Holdings 段
5. 追加 `~/wiki/log.md` 日志

**删除自选股（用户说"删除"或"移除"某只持仓）：**
1. 从 `~/wiki/watchlist.md` 表格中删除对应行
2. 从 `~/wiki/SCHEMA.md` 的 Portfolio domain 描述中移除
3. 将对应 entity 页移至 `~/wiki/_archive/` 或直接删除
4. 从 `~/wiki/index.md` 的 Portfolio Holdings 段移除
5. 追加 `~/wiki/log.md` 日志

**每次新闻收集时：**
1. 从 `~/wiki/watchlist.md` 读取当前自选股列表
2. 每只股票用英文关键字建立独立 Google News RSS feed
3. 同时抓取板块背景 feed
4. 更新各股票 Entity 页面 + Digest 持仓分析板块

**持仓分析板块结构（追加到每日 Digest 末尾）：**
- 板块开头必须加声明：`> **声明：** 基于公开新闻和行业逻辑推演，不构成投资建议`
- 每只股票用 **三维度表格**（短期催化剂/中期逻辑/主要风险）
- 后跟 📊 **走势预判** 和 💡 **买卖建议**
- 风险评级视觉标识：📈正面 / ⚠️谨慎 / ⏸️观望 / 🔴高风险

**股票 RSS 抓取关键坑：**
- **必须用 en-US locale**，中文 zh-CN feed 几乎总是返回 0 结果
- 使用具体英文关键词，如 `"Alibaba BABA 09988 HK stock earnings"`
- 每只股票单独一个 feed，不要合并查询

**股票 Entity 页面结构：**
```markdown
---
title: 公司名 (Ticker) — 持仓
tags: [company-tag, exchange-tag, sector-tag]
sources: [raw/articles/...]
---
## Key Facts（代码/行业/市值/核心业务/催化剂事件）
## 近期动态（最新新闻+解读，带日期和来源）
## 风险因素（列出3-5条）
## 相关概念（[[wikilinks]] 链接到宏观页面）
```

## X/Twitter 消息源（需 xurl CLI）

安装 xurl 后可抓取以下账号的推文：

### 政治与政策
- @realDonaldTrump / @POTUS — 特朗普
- @elonmusk — 马斯克
- @WhiteHouse — 白宫
- @StateDept — 美国国务院
- @SecDef — 美国国防部

### 经济与金融
- @federalreserve — 美联储
- @ecb — 欧洲央行
- @BlackRock — 贝莱德
- @goldmansachs — 高盛
- @SquawkCNBC — CNBC快讯
- @WSJ — 华尔街日报
- @FT — 金融时报
- @business — 彭博社

### AI/科技领袖
- @sama — Sam Altman (OpenAI)
- @gdb — Greg Brockman (OpenAI)
- @kaboroevich — Ilya Sutskever (SSI)
- @ylecun — Yann LeCun (Meta AI)
- @AndrewYNg — 吴恩达
- @karpathy — Andrej Karpathy
- @DrJimFan — Jim Fan (NVIDIA)
- @JeffDean — Jeff Dean (Google AI)
- @demishassabis — Demis Hassabis (Google DeepMind)
- @DarioAmodei — Dario Amodei (Anthropic)

### 芯片/半导体
- @nvidia — NVIDIA
- @AMD — AMD
- @intel — Intel
- @TSMC — 台积电

### 中文科技圈
- @tualatrix — 图拉鼎
- @haoel — 左耳朵耗子
- @dotey — 宝玉
- @geekbb — 小众软件
- @yihong0618 — yihong

## Wiki 结构

```
~/wiki/
├── SCHEMA.md
├── index.md
├── log.md
├── watchlist.md        # 自选股列表
├── raw/articles/       # 原始新闻源（不可变）
├── entities/           # 人物/组织页面 + 持仓分析页面
├── digests/            # 每日摘要 YYYY-MM-DD-digest.md
├── concepts/           # 主题/概念
└── comparisons/        # 对比分析
```

## Cron 设置

当前 cron job：`b25f3f7206c2`

```
schedule: 0 8,20 * * * (北京时间每天8:00和20:00)
deliver: telegram:chong
skills: llm-wiki, news-digest

Cron 提示：每次运行时：(1) 收集六大宏观主题新闻；(2) 读取 ~/wiki/watchlist.md 获取自选股列表；
(3) 逐只抓取自选股新闻 → 更新 Entity 页面 → 追加 Digest 持仓分析板块；
(4) Telegram 推送中加入持仓买卖建议摘要。
```

## 工作流程

### 1. 抓取原始新闻

**优先使用英文 feed（更稳定、内容更多），中文 feed 为辅助：**

```bash
# 特朗普（英文主 + 中文辅）
curl -s "https://news.google.com/rss/search?q=Trump&hl=en-US&gl=US&ceid=US:en"

# 马斯克
curl -s "https://news.google.com/rss/search?q=Elon+Musk+Tesla+SpaceX&hl=en-US&gl=US&ceid=US:en"

# 政经（关税、贸易、股市）
curl -s "https://news.google.com/rss/search?q=tariffs&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=China+trade&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=stock+market+today&hl=en-US&gl=US&ceid=US:en"

# AI
curl -s "https://news.google.com/rss/search?q=artificial+intelligence&hl=en-US&gl=US&ceid=US:en"

# 芯片
curl -s "https://news.google.com/rss/search?q=NVIDIA&hl=en-US&gl=US&ceid=US:en"

# 自选股（从 watchlist.md 读取后动态生成，示例）：
curl -s "https://news.google.com/rss/search?q=Alibaba+BABA+09988+HK+stock+earnings&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=Meituan+03690+HK+stock+earnings&hl=en-US&gl=US&ceid=US:en"
```

**RSS 解析必须使用正则表达式（不能用 XML 解析器）：**
Google News RSS 含有 `&amp;` 等 HTML 实体，`ET.fromstring()` 会报 `mismatched tag` 错误。
正确做法：

```python
import re, html

def parse_rss_regex(xml_str, category):
    items = []
    blocks = re.split(r'<item[^>]*>', xml_str)
    for block in blocks[1:]:
        end = block.find('</item>')
        if end == -1: continue
        block = block[:end]
        
        title_m = re.search(r'<title[^>]*>(.*?)</title>', block, re.DOTALL)
        link_m = re.search(r'<link[^>]*>(.*?)</link>', block, re.DOTALL)
        pubdate_m = re.search(r'<pubDate[^>]*>(.*?)</pubDate>', block, re.DOTALL)
        source_m = re.search(r'<source[^>]*>(.*?)</source>', block, re.DOTALL)
        
        if not title_m or not link_m: continue
        
        title = html.unescape(title_m.group(1).strip())
        title = re.sub(r'<!\[CDATA\[|\]\]>', '', title)
        
        # Date parse — strip timezone suffix
        clean_date = re.sub(r' [A-Z]{2,4}$', '', pub_date_str)
        dt = datetime.strptime(clean_date, '%a, %d %b %Y %H:%M:%S')
```

**话题分类策略（避免全部被误判为 Trump）：**
使用来源 feed 作为主信号 + 关键词作为覆盖：
- 来自 `trump_broad` feed → 默认 Trump 类
- 来自 `nvidia_chip` feed → 默认芯片类
- 但若标题含 "Elon Musk"/"Tesla" 等，无论来源都归入 Musk 类

### 2. 存入 Wiki

- 原始数据 → `~/wiki/raw/articles/<topic>-YYYY-MM-DD.md`
- 实体页 → `~/wiki/entities/`（出现2次以上的实体才建页）
- 持仓页 → `~/wiki/entities/`（每个自选股一个）
- 每日摘要 → `~/wiki/digests/YYYY-MM-DD-digest.md`

### 3. 推送到 Telegram

**必须使用中文**，格式如下：

```
📰 新闻速递 | 2026年4月30日

━━━━━━━━━━━━━━━━━━

🔴 特朗普
• 摘要一（来源，日期）

🔵 马斯克
• 摘要一（来源，日期）

🔶 政经动态
• 摘要一（来源，日期）

🟢 AI / 芯片
• 摘要一（来源，日期）

━━━━━━━━━━━━━━━━━━

💼 持仓快报
📈 中兴: 持有/逢低加仓
⚠️ 舍得: 谨慎持有（观察五一数据）
📈 阿里: 持有等待（5/13财报）
⏸️ 美团: 持有观望
🔴 壁仞: 高波动 ≤5%

完整分析: ~/wiki/digests/YYYY-MM-DD-digest.md
```

## 语言规则（CRITICAL）

- **Telegram 推送必须全中文**，包括标题、正文、来源翻译
- Wiki 页面：中文为主，关键术语保留英文原名
- 日志、索引使用中文
- 持仓分析表格和标题使用中文

## 注意事项

- **Google News RSS 解析必须用正则，不能用 XML parser** —— `ET.fromstring()` 会因 `&amp;` 报错
- Google News RSS 的 `after:` 参数不可靠，拉取完整 feed 后在 Python 中按 pubDate 过滤
- **中文 `zh-CN` feed 经常返回空结果** —— 优先用英文 `en-US` feed，中文为辅（尤其是股票查询！）
- **话题分类用来源 feed 做主信号** —— 纯关键词分类会把所有 news 误判为 Trump
- **查询新鲜度：** 通用查询如 `"global economy"` 返回数月前的常青文章。用具体术语如 `"tariffs"`、`"stock market today"`、公司名 `"NVIDIA"` 更有效
- Telegram 推送在网络不稳定时可能超时，可重试一次
- WSL 环境确保 `systemd=true` 在 `/etc/wsl.conf` 中
- 人物/组织出现2次以上才创建实体页
- 矛盾信息标记 `contested: true`
- **自选股操作先更新 watchlist.md，再更新其他文件**

## 依赖

- curl（系统自带）
- Python 3（标准库：xml.etree, re, html, datetime）
- Hermes cronjob 工具
- Hermes send_message（Telegram 平台已配置）
- 可选：xurl CLI（抓取 X/Twitter 推文）
- `~/wiki/watchlist.md`（自选股列表，手动或自动维护）
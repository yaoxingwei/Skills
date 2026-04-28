---
name: news-digest
description: "中文新闻情报速递：每日自动收集特朗普、马斯克、政治、经济、AI大模型、芯片半导体新闻，存入wiki知识库并推送到Telegram。"
version: 2.0.0
author: yaoxw
license: MIT
metadata:
  hermes:
    tags: [news, digest, trump, musk, ai, semiconductor, wiki, telegram, cron, chinese]
    category: research
---

# 新闻情报速递 (News Digest)

中英双语新闻自动收集与推送系统。通过 Google News RSS 抓取最新新闻，存入结构化 Wiki 知识库，并通过 Telegram 定时推送。

## 六大主题

| 主题 | 关键词 | 标识 |
|------|--------|------|
| 特朗普 | 发言、政策、选举、伊朗战争、法律案件 | 🔴 |
| 马斯克 | Tesla、SpaceX、xAI、X、OpenAI庭审 | 🔵 |
| 政治/地缘 | 中美关系、关税、贸易战、外交、国际政要 | 🔶 |
| 经济/市场 | 美联储、美股、加密货币、关税影响 | 🔶 |
| AI大模型 | GPT、Claude、Gemini、DeepSeek、开源模型、AGI | 🟢 |
| 芯片半导体 | 英伟达、台积电、ASML、出口管制、GPU | 🟢 |

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
├── raw/articles/       # 原始新闻源（不可变）
├── entities/           # 人物/组织页面
├── digests/            # 每日摘要 YYYY-MM-DD-digest.md
├── concepts/           # 主题/概念
└── comparisons/        # 对比分析
```

## Cron 设置

```
schedule: 0 9,19 * * * (北京时间每天9:00和19:00)
deliver: telegram:chong
skills: llm-wiki, news-digest
```

## 工作流程

### 1. 抓取原始新闻

```bash
# Google News RSS（中文+英文）
curl -s "https://news.google.com/rss/search?q=特朗普+总统&hl=zh-CN&gl=CN&ceid=CN:zh"
curl -s "https://news.google.com/rss/search?q=Elon+Musk+马斯克&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=NVIDIA+AI+芯片+半导体+英伟达&hl=zh-CN&gl=CN&ceid=CN:zh"
curl -s "https://news.google.com/rss/search?q=AI+大模型+DeepSeek+GPT+Claude&hl=zh-CN&gl=CN&ceid=CN:zh"
curl -s "https://news.google.com/rss/search?q=中美+关税+贸易战+经济&hl=zh-CN&gl=CN&ceid=CN:zh"
```

### 2. 存入 Wiki

- 原始数据 → `~/wiki/raw/articles/<topic>-YYYY-MM-DD.md`
- 实体页 → `~/wiki/entities/`（出现2次以上的实体才建页）
- 每日摘要 → `~/wiki/digests/YYYY-MM-DD-digest.md`

### 3. 推送到 Telegram

**必须使用中文**，格式如下：

```
📰 新闻速递 | 2026年4月28日

━━━━━━━━━━━━━━━━━━

🔴 特朗普
• 摘要一（来源，日期）
• 摘要二（来源，日期）

🔵 马斯克
• 摘要一（来源，日期）
• 摘要二（来源，日期）

🔶 政经动态
• 摘要一（来源，日期）
• 摘要二（来源，日期）

🟢 AI / 芯片
• 摘要一（来源，日期）
• 摘要二（来源，日期）

━━━━━━━━━━━━━━━━━━
Wiki: ~/wiki/
```

## 语言规则（CRITICAL）

- **Telegram 推送必须全中文**，包括标题、正文、来源翻译
- Wiki 页面：中文为主，关键术语保留英文原名
- 日志、索引使用中文

## 注意事项

- Google News RSS 的 `after:` 参数不可靠，拉取完整 feed 后在 Python 中按 pubDate 过滤
- Telegram 推送在网络不稳定时可能超时，可重试一次
- WSL 环境确保 `systemd=true` 在 `/etc/wsl.conf` 中
- 人物/组织出现2次以上才创建实体页
- 矛盾信息标记 `contested: true`

## 依赖

- curl（系统自带）
- Python 3 xml.etree（标准库）
- Hermes cronjob 工具
- Hermes send_message（Telegram 平台已配置）
- 可选：xurl CLI（抓取 X/Twitter 推文）
---
name: news-digest
description: "中文新闻情报速递：每日自动收集特朗普、马斯克、政治、经济、AI大模型、芯片半导体新闻+GitHub趋势榜，存入wiki知识库并推送到Telegram。"
version: 2.5.0
author: yaoxw
license: MIT
metadata:
  hermes:
    tags: [news, digest, trump, musk, ai, semiconductor, wiki, telegram, cron, chinese]
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

### 通用抓取规则

以下规则适用于所有主题的新闻抓取：

- **execute_code 沙箱限制：** Google News RSS 在 execute_code 中可能超时/返回空。始终用 terminal 工具执行 curl 命令
- **安全扫描器阻止 pipe：** `curl | python3` 管道会被拦截。解决：curl 先存文件（-o /tmp/file），再用 execute_code 读文件解析
- **并行抓取方法（CRITICAL）：** 在 execute_code 中用 Python `concurrent.futures.ThreadPoolExecutor` 并行发起所有 curl 请求
- **去重策略：** 解析后按 `link` URL 去重
- **空 feed 优雅降级：** 标注"本期无新增"并引用上期要点
- **零字节 feed 重试策略：** 用更宽松的查询重试，最多1次
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
- @sama — Sam Altman (OpenAI CEO)
- @gdb — Greg Brockman (OpenAI)
- @kaboroevich — Ilya Sutskever (SSI)
- @miramurati — Mira Murati (Thinking Machines, ex-OpenAI CTO)
- @ylecun — Yann LeCun (Meta AI)
- @AndrewYNg — 吴恩达 (DeepLearning.AI)
- @karpathy — Andrej Karpathy (Eureka Labs)
- @DrJimFan — Jim Fan (NVIDIA)
- @JeffDean — Jeff Dean (Google AI)
- @demishassabis — Demis Hassabis (Google DeepMind)
- @DarioAmodei — Dario Amodei (Anthropic CEO)
- @alexandr_wang — Alexandr Wang (Scale AI CEO)
- @amasad — Amjad Masad (Replit CEO)
- @aidangomez — Aidan Gomez (Cohere CEO)
- @guillaumelample — Guillaume Lample (Mistral)
- @arthurmensch — Arthur Mensch (Mistral CEO)
- @OpenAI — OpenAI 官方
- @AnthropicAI — Anthropic 官方
- @GoogleDeepMind — Google DeepMind 官方
- @AIatMeta — Meta AI 官方
- @MistralAI — Mistral AI 官方
- @huggingface — Hugging Face 🤗
- @DeepSeek — DeepSeek 官方（如存在）
- @OpenRouterAI — OpenRouter
- @GroqInc — Groq
- @perplexity_ai — Perplexity AI

### 芯片/半导体
- @nvidia — NVIDIA 官方
- @JensenHuang — 黄仁勋 (NVIDIA CEO)
- @AMD — AMD 官方
- @LisaSu — 苏姿丰 (AMD CEO)
- @intel — Intel 官方
- @PGelsinger — Pat Gelsinger (Intel CEO)
- @TSMC — 台积电
- @Broadcom — Broadcom
- @Arm — Arm
- @SamsungDS — Samsung 半导体
- @SKhynix — SK 海力士
- @dylan522p — Dylan Patel (SemiAnalysis)

### 科技巨头 CEO
- @sundarpichai — Sundar Pichai (Google CEO)
- @satyanadella — Satya Nadella (Microsoft CEO)
- @tim_cook — Tim Cook (Apple CEO)
- @finkd — Mark Zuckerberg (Meta CEO)
- @ajassy — Andy Jassy (Amazon CEO)

### 风投/意见领袖
- @pmarca — Marc Andreessen (a16z)
- @eladgil — Elad Gil
- @balajis — Balaji Srinivasan
- @bchesky — Brian Chesky (Airbnb CEO)

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
schedule: 0 8,20 * * * (北京时间每天8:00和20:00)
deliver: telegram:chong
skills: llm-wiki, news-digest

**完整 prompt 模板：**
收集以下主题的最新新闻：
1. 特朗普（Trump）— 政策、伊朗战争、法律案件、外交
2. 马斯克（Musk）— Tesla/SpaceX/xAI/X、DOGE、OpenAI
3. 中美政经 — 关税、贸易战、地缘政治、外交
4. 全球经济 — 美联储、美股、加密货币、OPEC/油价
5. AI大模型 — GPT、Claude、Gemini、DeepSeek、开源模型；关注科技公司CEO（Jensen Huang、Sam Altman、Dario Amodei等）的发言和动向
6. 芯片半导体 — 英伟达、台积电、ASML、出口管制、GPU；关注芯片巨头CEO（Jensen Huang、Lisa Su、Pat Gelsinger等）动态
7. GitHub Trending — 每日/每周 Top 10 开源项目（必须带详细中文介绍）

使用 Google News RSS（curl + Python regex解析）获取新闻。英文en-US feed为主。优先使用事件级具体查询词获取新鲜新闻。

保存原始数据到 ~/wiki/raw/articles/<topic>-YYYY-MM-DD.md。

GitHub Trending 抓取与展示（重要：每个项目必须有详细中文介绍）：
- 使用 GitHub Search API（不能用 HTML 抓取——trending 页面是 JS 渲染）：
  - 日榜: curl -s -H "Accept: application/vnd.github+json" "https://api.github.com/search/repositories?q=created:>YYYY-MM-DD+stars:>50&sort=stars&order=desc&per_page=10"
  - 周榜: curl -s -H "Accept: application/vnd.github+json" "https://api.github.com/search/repositories?q=created:>YYYY-MM-DD+stars:>200&sort=stars&order=desc&per_page=10"
  - 日榜取近3天创建、stars>50；周榜取近14天创建、stars>200
- 获取 Top 10 后，**对每个项目调用 GitHub API 获取详细信息**：
  curl -s -H "Accept: application/vnd.github+json" "https://api.github.com/repos/{owner}/{repo}"
  读取 description、topics、language、stargazers_count、forks_count
- 对 Top 5 项目获取 README 摘要（取前500字符）：
  curl -s -H "Accept: application/vnd.github+json" "https://api.github.com/repos/{owner}/{repo}/readme"
  Base64解码后取开头段落
- 保存原始数据到 ~/wiki/raw/trending/trending-daily-YYYY-MM-DD.md 和 trending-weekly-YYYY-MM-DD.md
- **在 digest 中每个项目必须有 2-3 句中文总结**，包含：
  1. 项目是什么（功能/用途）
  2. 技术亮点/创新点
  3. 目标用户/适用场景
- 表格后追加 "🔥 本周趋势洞察"，归纳 3-5 个跨项目技术趋势
- 推送格式使用详细表格，每个项目一行，简介列写完整的中文介绍

每日摘要（digest）必须包含：
1. 六大宏观主题（含科技CEO动态） 2. 🟣 GitHub Trending 日榜+周榜（带详细项目介绍）

推送到 Telegram:chong。所有推送和Wiki内容必须使用中文。
```

## 工作流程

### 1. 抓取原始新闻

**优先使用英文 feed（更稳定、内容更多），中文 feed 为辅助：**

```bash
# 特朗普（英文主 + 中文辅）
curl -s "https://news.google.com/rss/search?q=Trump&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=特朗普&hl=zh-CN&gl=CN&ceid=CN:zh"

# 马斯克
curl -s "https://news.google.com/rss/search?q=Elon+Musk+Tesla+SpaceX&hl=en-US&gl=US&ceid=US:en"

# 政经（关税、贸易、股市）
curl -s "https://news.google.com/rss/search?q=tariffs&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=China+trade&hl=en-US&gl=US&ceid=US:en"
curl -s "https://news.google.com/rss/search?q=stock+market+today&hl=en-US&gl=US&ceid=US:en"

# AI（用具体公司名比"AI news"更有效）
curl -s "https://news.google.com/rss/search?q=artificial+intelligence&hl=en-US&gl=US&ceid=US:en"

# 芯片
curl -s "https://news.google.com/rss/search?q=NVIDIA&hl=en-US&gl=US&ceid=US:en"
```

**注意：** 中文 `zh-CN` feed 经常返回空结果或旧文章。优先使用英文 `en-US` feed。
如中文 feed 返回 0 条，不影响整体质量——英文 feed 已覆盖关键消息源（BBC、CNN、路透社等）。

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
        # ... extract other fields
        
        # Date parse
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
- 每日摘要 → `~/wiki/digests/YYYY-MM-DD-digest.md`

### 晚间运行（Evening Run）注意事项
- 晚间运行（20:00 CST）应创建独立晚间摘要：`digests/YYYY-MM-DD-evening-digest.md`
- GitHub Trending 数据每次运行都重新抓取（星数全天变化），覆盖已有 `trending-daily-YYYY-MM-DD.md`
- 重点关注白天到晚间的新增新闻（xAI/SpaceX合并、峰会新分析等），避免重复早间已有内容
- 晚间摘要标题加"晚间更新"标识，index.md 和 log.md 中与早间摘要并列

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

- **Google News RSS 解析必须用正则，不能用 XML parser** —— `ET.fromstring()` 会因 `&amp;` 报错
- Google News RSS 的 `after:` 参数不可靠，拉取完整 feed 后在 Python 中按 pubDate 过滤
- **中文 `zh-CN` feed 经常返回空结果** —— 优先用英文 `en-US` feed，中文为辅
- **话题分类用来源 feed 做主信号** —— 纯关键词分类会把所有 news 误判为 Trump
- **查询新鲜度（CRITICAL）：** 通用查询如 `"Trump president policy tariff Iran"` 返回数月前的常青文章（实测：73条中0条为最近2天）。**必须使用事件级具体查询**才能获取新鲜新闻。正确做法：
  - ❌ `"Trump president policy tariff Iran"` → 2025年7月-2026年1月的旧文
  - ✅ `"Trump Iran blockade Comey Powell Fed trial"` → 4月29-30日新闻
  - ✅ `"Musk Altman OpenAI trial court testimony"` → 当天庭审报道
  - ✅ `"UAE OPEC oil quit Iran war"` → 当天OPEC新闻
  - **原则：** 用最近发生的具体事件名词（人物+动作+对象）替代泛泛的主题词。每次运行前根据上期digest中识别的热点事件更新查询词。
- **多轮抓取策略：** 第一轮用通用查询覆盖广度并保存raw文件；第二轮用事件级查询补充深度。仅事件级查询的结果用于digest——通用查询的结果虽然存raw但大部分是过期内容
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
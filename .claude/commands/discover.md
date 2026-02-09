---
name: discover
description: 自动从多个来源广泛收集热门/有趣网站，批量写入 inbox.md 并创建 sites/ 档案。按 agent 逻辑运行，自主完成搜索、筛选、去重、建档、入库。
---

# /urldiscover - 网站发现 Agent

## Purpose

广撒网收集有趣的网站和工具。不限主题，目标是持续扩大 inbox 网站池，为后续 `/curate` 提供素材。

## 运行模式

这是一个 agent 而非简单 skill。触发后自主完成所有步骤，不需要用户逐步确认。全程用中文与用户沟通。

## Input

用户可以可选指定：

- **数量**：希望收集多少个新网站（默认 10-15）
- **来源偏好**：侧重哪些来源（默认全部扫一遍）

```
/urldiscover
/urldiscover 20
/urldiscover hackernews
```

## Instructions

### Step 1: 读取现有 inbox

读取 `picks/inbox.md`（或 `/Users/moonyaan/Documents/Projects/gridtabs/picks/inbox.md`），提取所有已有 URL，用于后续去重。去重时忽略 `www.`、末尾 `/`、`http/https` 差异。

### Step 2: 多源并行搜索

使用 **Task tool 启动多个并行 subagent**，每个负责一个来源。这是关键——并行搜索大幅提升效率。

#### 来源清单与搜索策略

**Source 1: Hacker News**
- WebSearch: `site:news.ycombinator.com Show HN {current month} {current year}`
- WebSearch: `Hacker News trending tools {current month} {current year}`
- 目标：找 Show HN 帖子中分享的产品/工具 URL

**Source 2: Product Hunt**
- WebSearch: `site:producthunt.com new products {current month} {current year}`
- WebSearch: `Product Hunt top launches this week {current year}`
- 目标：找近期上线的产品 URL

**Source 3: Reddit**
- WebSearch: `site:reddit.com r/InternetIsBeautiful {current month} {current year}`
- WebSearch: `site:reddit.com r/webdev cool tools {current year}`
- WebSearch: `site:reddit.com r/SaaS new launch {current year}`
- 目标：找社区推荐的网站和工具 URL

**Source 4: Twitter/X & Indie Hacker 圈**
- WebSearch: `indie hacker launch new tool {current month} {current year}`
- WebSearch: `best new web tools {current month} {current year}`
- WebSearch: `new developer tools trending {current year}`
- 目标：找独立开发者圈子热议的产品

**Source 5: 综合发现**
- WebSearch: `best new websites {current month} {current year}`
- WebSearch: `cool web apps launched recently {current year}`
- WebSearch: `underrated tools {current year}`
- 目标：兜底，捞前面来源没覆盖到的

每个 subagent 返回格式：
```
- Site Name | URL | source名称 | 简短说明
```

### Step 3: 汇总与筛选

收集所有 subagent 结果后：

1. **去重**：
   - 与 inbox.md 已有 URL 去重
   - 搜索结果之间去重（不同来源可能发现同一网站）

2. **过滤**：排除以下类型
   - 纯新闻/文章页面（如 techcrunch.com/article/xxx）——要的是产品本身的 URL，不是报道它的文章
   - 已经很大众的网站（如 Google、GitHub、YouTube 这类）
   - 已经失效的网站（如果 WebFetch 返回错误）
   - 聚合/目录类网站本身（如 alternativeto.net）——除非它真的值得收藏

3. **提取产品 URL**：如果搜索结果是一篇文章提到了某个产品，提取产品自身的 URL，不是文章 URL

### Step 4: 更新域名白名单

将所有筛选通过的新网站域名写入 `picks/.claude/settings.local.json` 的 `permissions.allow` 数组中，格式为 `WebFetch(domain:xxx.com)`。

操作方式：
1. 读取当前 `picks/.claude/settings.local.json`
2. 从新 URL 中提取域名（保留 `www.` 等子域名）
3. 检查是否已在白名单中，避免重复
4. 将新条目追加到 `permissions.allow` 数组
5. 写回文件

这一步必须在 Step 5 的 WebFetch 之前完成，否则抓取时会触发手动确认。

### Step 5: 抓取网站信息并建档

对筛选通过的每个 URL，用 WebFetch 获取完整网站信息，**同时**创建 `sites/*.md` 档案文件。

**逐个串行处理**（避免并行 WebFetch 导致网络错误），每个网站：

1. WebFetch 抓取网站，提取：品牌名、功能描述、目标用户、视觉风格、配色、氛围
2. 立即写入 `picks/sites/{domain}.md`，格式：

```markdown
# {Site Name}

- **URL**: {url}
- **一句话**: {中文一句话描述}
- **详细说明**: {中文 2-3 句，功能特色和独特卖点}
- **目标用户**: {目标用户群体}
- **视觉风格**: {设计风格描述}
- **主色调**: {主要配色}
- **氛围关键词**: {3-5 个关键词}
- **可归类方向**:
  - 常规: {功能性分类方向}
  - 创意: {创意/美学分类方向}
```

**文件命名规则**：URL 去掉协议和 `www.`，`.` 和 `/` 替换为 `-`，去掉末尾 `-`，加 `.md`。
例：`https://www.raycast.com` → `raycast-com.md`，`https://lumalabs.ai/genie` → `lumalabs-ai-genie.md`

如果 WebFetch 失败（403/超时），用 inbox.md 中的 Note 信息和域名推断内容，仍然创建档案文件。

### Step 6: 写入 inbox.md

读取当前 `picks/inbox.md`，在表格末尾追加新行。Note 可直接复用 Step 5 建档时写的「一句话」：

```
| {Site} | {URL} | {Source} | {Note} | {Today's Date} | - |
```

- Source 填实际发现来源：hackernews / producthunt / reddit / twitter / web
- Note 填一句话说明（中文）
- In Picks 固定 `-`

### Step 7: 输出报告

```
## 发现报告

搜索了 N 个来源，发现 M 个新网站，已写入 inbox.md。

### 新增

| # | Site | URL | Source | Note |
|---|------|-----|--------|------|
| 1 | ... | ... | ... | ... |

### 跳过（已存在）

- site (url) — 已在 inbox 中

### 跳过（过滤）

- url — 原因
```

## 质量标准

收集的网站应该是：
- **可用的产品或工具**：有实际功能，不是纯资讯
- **有一定品质**：UI 不至于太粗糙，有人在认真做
- **值得被推荐**：能对某类用户有实际帮助
- **有独特性**：不是某个大产品的简单复制品

不需要每个都完美，inbox 是粗筛，质量把控在 curate 阶段。

## 重要规则

- **不要给网站打 tag**——这是 curate 的事
- **不要修改已有行**——只追加
- **并行搜索**——用 Task tool 并行跑多个来源，不要串行
- **结果要实际 URL**——不是文章链接，是产品本身的网址
- **Note 用中文**——简短一句话

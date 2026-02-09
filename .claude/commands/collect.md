---
name: collect
description: 收集网站到 inbox。给 URL 自动抓取网站信息，追加到 inbox.md 并创建 sites/ 档案。
---

# /collect - 网站收集

## Purpose

将网站快速收集到 picks/inbox.md 并创建 sites/ 档案，降低收集摩擦。人只需要提供 URL，自动完成信息抓取、建档、入库。

## Input

用户提供以下信息：

- **URL**（必填）：网站地址
- **Source**（可选）：来源，如 twitter、reddit、hackernews、自己用。默认 `-`
- **Note**（可选）：简短备注，为什么值得收藏。默认 `-`

支持单个或批量收集（多个 URL 用换行分隔）。

## Example Usage

```
/collect https://linear.app
```

```
/collect https://linear.app twitter 项目管理工具，设计很好
```

```
/collect
https://linear.app
https://arc.net
https://raycast.com
```

## Instructions

对于每个 URL，执行以下步骤：

### Step 1: 检查重复

读取 `picks/inbox.md`，检查 URL 是否已存在（匹配域名，忽略 www 和末尾斜杠差异）。

- 如果已存在，告知用户并跳过
- 如果不存在，继续

### Step 2: 更新域名白名单

将新 URL 的域名写入 `picks/.claude/settings.local.json` 的 `permissions.allow` 数组，格式为 `WebFetch(domain:xxx.com)`。检查避免重复。这一步在 WebFetch 之前，确保抓取时不触发手动确认。

### Step 3: 抓取网站信息并建档

使用 WebFetch 访问 URL，提取完整网站信息，并创建 `picks/sites/{domain}.md` 档案文件。

**文件命名规则**：URL 去掉协议和 `www.`，`.` 和 `/` 替换为 `-`，去掉末尾 `-`，加 `.md`。
例：`https://www.raycast.com` → `raycast-com.md`

**档案格式**：

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

如果 WebFetch 失败，用域名推断品牌名、用用户提供的 Note 填充内容，仍然创建档案。

### Step 4: 追加到 inbox.md

在 `picks/inbox.md` 的表格末尾追加一行。Note 可复用 Step 3 档案中的「一句话」：

```
| {Site} | {URL} | {Source} | {Note} | {Today's Date} | - |
```

- Date 格式：`YYYY-MM-DD`
- In Picks 固定为 `-`（新收集的网站未分配到任何 pick）

### Step 5: 确认

输出收集结果摘要：

```
已收集 N 个网站到 inbox + sites/：
- Site Name (url) → sites/{filename}

跳过 M 个（已存在）：
- Site Name (url)
```

## 重要规则

- **不要添加 tag**：收集阶段不做分类
- **不要修改已有行**：只追加新行
- **Note 保持简短**：一句话，不要写长描述
- **inbox.md 路径**：始终使用当前项目下的 `picks/inbox.md`，如果不在 picks 目录，使用 `/Users/moonyaan/Documents/Projects/gridtabs/picks/inbox.md`

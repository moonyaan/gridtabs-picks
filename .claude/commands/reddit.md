---
name: reddit
description: 根据已发布或 draft pick 生成 Reddit 帖子，输出到 content/{slug}/reddit.md。
---

# /reddit-post - 生成 Reddit 帖子

## Input

```
/reddit-post                         # 用最新 draft/pick
/reddit-post ai-coding-tools         # 指定 slug
```

## Instructions

1. 读取 `picks/drafts/{slug}.md` 或从 `picks/picks.json` 中找到对应 pick
2. 生成帖子，写入 `picks/content/{slug}/reddit.md`

## 输出格式

```markdown
# Reddit Post: {slug}

## 推荐 Subreddits

- r/xxx — 原因
- r/xxx — 原因

## Title

{标题}

## Body

{正文}
```

## 帖子规范

- **标题**：直接、有信息量，Reddit 用户讨厌标题党
- **正文**：提供真实价值，先说有用的内容，再提 gridtabs
- **语气**：社区成员分享，不是品牌推广。像 "I compiled a list of..." 不像 "Check out our..."
- **自荐标注**：如果提到 gridtabs 产品，按 Reddit 规矩标注 `[OC]` 或说明是自己做的
- **Subreddit 推荐**：根据 pick 主题推荐 2-3 个合适的 subreddit

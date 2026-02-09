---
name: xhs
description: 根据已发布或 draft pick 生成小红书帖子，输出到 content/{slug}/xiaohongshu.md。
---

# /xiaohongshu-post - 生成小红书帖子

## Input

```
/xiaohongshu-post                    # 用最新 draft/pick
/xiaohongshu-post ai-coding-tools    # 指定 slug
```

## Instructions

1. 读取 `picks/drafts/{slug}.md` 或从 `picks/picks.json` 中找到对应 pick
2. 生成帖子，写入 `picks/content/{slug}/xiaohongshu.md`

## 输出格式

```markdown
# 小红书: {slug}

## 标题

{标题，带 emoji}

## 正文

{正文内容}

## 标签

{hashtag 列表}

## 封面图建议

{封面应该怎么做}
```

## 帖子规范

- **语言**：中文
- **标题**：20 字以内，可以用 emoji，要有点击欲（但不浮夸）
- **正文**：分段清晰，适当用 emoji 分隔段落，列清单式最受欢迎
- **标签**：5-10 个相关标签
- **风格**：像在和朋友安利好东西，真诚不做作
- **不要**：过度使用 "绝绝子""yyds" 等过时网络用语

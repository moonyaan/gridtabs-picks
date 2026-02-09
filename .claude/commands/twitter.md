---
name: twitter
description: 根据已发布或 draft pick 生成 Twitter/X 推文，输出到 content/{slug}/twitter.md。
---

# /twitter-post - 生成推文

## Input

```
/twitter-post                        # 用最新 draft/pick
/twitter-post ai-coding-tools        # 指定 slug
```

## Instructions

1. 读取 `picks/drafts/{slug}.md` 或从 `picks/picks.json` 中找到对应 pick
2. 读取 `picks/sites/` 中相关网站档案获取更多上下文
3. 生成推文，写入 `picks/content/{slug}/twitter.md`

## 输出格式

生成 **3 个版本**供用户选择，写入 `picks/content/{slug}/twitter.md`：

```markdown
# Twitter Post: {slug}

## Version A — 清单体

{推文内容}

## Version B — 故事体

{推文内容}

## Version C — 观点体

{推文内容}
```

## 推文规范

- **长度**：单条不超过 280 字符，或 thread（2-3 条）
- **语言**：英文（gridtabs 主要受众是英文用户）
- **结构**：hook 开头 → 内容 → CTA
- **必须包含**：gridtabs picks 页面的引导（不硬塞，自然带出）
- **风格**：像独立开发者在分享自己的发现，不像品牌营销号
- **禁止**：leverage、empower、seamless、delve、streamline、🚀（除非用户要求 emoji）

## 三种体裁

**清单体**：直接列网站，简洁有力
```
10 design tools I keep coming back to:

1. Mobbin — real UI patterns from real apps
2. ...

Full list → [link]
```

**故事体**：从个人经历切入
```
I spent 2 hours looking for the right tool and found something better — a whole collection of them.

...
```

**观点体**：抛出观点引发讨论
```
Most people only know Dribbble for design inspiration.

Here are 12 sites that are actually better:
...
```

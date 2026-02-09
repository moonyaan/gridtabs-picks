---
name: tiktok
description: 根据已发布或 draft pick 生成 TikTok 短视频脚本，输出到 content/{slug}/tiktok.md。
---

# /tiktok-script - 生成短视频脚本

## Input

```
/tiktok-script                       # 用最新 draft/pick
/tiktok-script ai-coding-tools       # 指定 slug
```

## Instructions

1. 读取 `picks/drafts/{slug}.md` 或从 `picks/picks.json` 中找到对应 pick
2. 生成脚本，写入 `picks/content/{slug}/tiktok.md`

## 输出格式

```markdown
# TikTok Script: {slug}

## Hook (0-3s)

{开头文字/画面}

## Body (3-45s)

| 时间 | 画面 | 旁白/文字 |
|------|------|----------|
| 3-8s | {描述} | {文字} |
| 8-15s | ... | ... |
| ... | ... | ... |

## CTA (45-60s)

{结尾引导}

## 文案覆盖文字

{屏幕上显示的文字，逐条列出}

## 推荐 BGM 风格

{节奏/氛围建议}
```

## 脚本规范

- **时长**：30-60 秒
- **语言**：英文（可备注中文版本差异）
- **节奏**：快切，每个网站 3-5 秒展示
- **Hook**：前 3 秒必须抓注意力，用反问或惊人事实
- **不要**：念说明书式的旁白，要有节奏感

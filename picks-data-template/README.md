# gridtabs-picks

gridtabs Picks 数据仓库 - 用于存放精选网站合集数据

## 目录结构

```
gridtabs-picks/
├── picks.json          # 主数据文件
├── images/             # 封面图片
│   ├── coverImage1.jpg
│   └── coverImage2.jpg
└── README.md
```

## 数据格式

`picks.json` 是一个数组，每个元素是一个主题：

```json
{
  "slug": "url-friendly-name",
  "title": {
    "en": "English Title",
    "zh-CN": "中文标题"
  },
  "description": {
    "en": "English description",
    "zh-CN": "中文描述"
  },
  "date": "2026-02-03",
  "coverImage": "coverImage1.jpg",
  "sites": [
    {
      "name": "Site Name",
      "url": "https://example.com",
      "description": {
        "en": "English description",
        "zh-CN": "中文描述"
      }
    }
  ]
}
```

## 字段说明

| 字段 | 说明 |
|------|------|
| `slug` | URL 路径，只能用小写字母、数字、连字符 |
| `title` | 标题，需要中英文 |
| `description` | 简介，需要中英文 |
| `date` | 发布日期，格式 YYYY-MM-DD |
| `coverImage` | 封面图片文件名（放在 images 文件夹） |
| `sites` | 网站列表数组 |

## 如何添加新主题

1. 在 `picks.json` 数组末尾添加新对象
2. 把封面图片放到 `images/` 文件夹
3. Commit 并 Push
4. 网站会在 1 小时内自动更新

---

# AI 生成内容指南

如果你使用 AI 来帮助生成内容，可以给 AI 以下提示：

## Prompt 模板

```
我需要你帮我生成一个 picks.json 的新条目。

主题：[你的主题，比如"AI 写作工具"]
网站列表：
1. [网站名] - [URL]
2. [网站名] - [URL]
...

请按照以下格式生成 JSON：
- slug: 用小写字母和连字符
- title: 英文标题要吸引人，中文翻译要自然
- description: 英文和中文各一句话，有态度，不要太官方
- date: 使用今天的日期
- sites: 每个网站写一句简短的介绍，中英文都要

参考风格：
- 标题例子："Underrated Platforms That Actually Convert" / "被低估的高转化推广平台"
- 描述例子："Where top designers find their next homepage idea." / "顶尖设计师的灵感来源。"
```

## 只有图片时的 Prompt

```
我有一张 gridtabs 截图，里面有这些网站：[列出网站名]

请帮我：
1. 查找每个网站的 URL
2. 写一句简短的中英文介绍
3. 想一个吸引人的主题标题和描述
4. 生成完整的 JSON 条目
```

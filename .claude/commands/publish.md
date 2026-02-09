---
name: publish
description: 将审核通过的 draft 发布到 picks.json。解析 markdown draft，转为 JSON，追加到已发布数据中。
---

# /publish-pick - 发布 Pick

## Purpose

将审核通过的 draft pick 正式发布：markdown draft → JSON → 写入 picks.json。这是管道的最后一步，操作 picks.json 这个官网直接引用的文件，必须谨慎。

## Input

```
/publish-pick                        # 发布最新的 draft
/publish-pick ai-coding-tools        # 发布指定 slug
```

## Instructions

### Step 1: 定位 Draft

- 如果用户指定了 slug，读取 `picks/drafts/{slug}.md`
- 如果没指定，读取 `picks/drafts/` 目录，找最近修改的 `.md` 文件
- 如果 drafts 目录为空，提示用户先用 `/curate` 生成 draft

### Step 2: 检查发布条件

读取 draft 的 frontmatter：

- `status` 必须是 `draft` 或 `review`（不能是已发布的 `published`）
- 如果 status 是其他值，提示用户先完成审核流程

读取 `picks/picks.json`，检查：
- slug 不能和已发布的 pick 重复
- 如果重复，**停止并告知用户**，不要覆盖

### Step 3: 解析 Markdown → JSON

从 draft markdown 中解析出 picks.json 需要的结构：

**解析规则：**

1. **slug**: 从 frontmatter 的 `slug` 字段读取
2. **title**:
   - `en`: 第一个 `# ` 标题（英文）
   - `zh-CN`: 第二个 `# ` 标题（中文）
3. **description**:
   - `en`: 英文标题下面的段落
   - `zh-CN`: 中文标题下面的段落
4. **date**: 使用今天的日期，格式 `YYYY-MM-DD`
5. **coverImage**: 从 frontmatter 读取，如果为空则设为 `""`
6. **sites**: 从 `## Sites` 下的每个 `### ` 解析：
   - `name`: `###` 后的文本
   - `url`: `**URL**:` 后的链接
   - `description.en`: `**EN**:` 后的文本
   - `description["zh-CN"]`: `**ZH**:` 后的文本

**输出 JSON 结构：**

```json
{
  "slug": "...",
  "title": { "en": "...", "zh-CN": "..." },
  "description": { "en": "...", "zh-CN": "..." },
  "date": "2026-02-06",
  "coverImage": "...",
  "sites": [
    {
      "name": "...",
      "url": "...",
      "description": { "en": "...", "zh-CN": "..." }
    }
  ]
}
```

### Step 4: 预览确认

在写入前，向用户展示即将写入的完整 JSON，并明确告知：

```
## 即将发布

**Slug**: {slug}
**标题**: {en title} / {zh title}
**网站数**: {N}
**日期**: {today}

将追加到 picks.json（当前已有 {M} 个 picks）。

确认发布？
```

**必须等用户确认后才写入 picks.json。** 使用 AskUserQuestion tool 询问。

### Step 5: 写入 picks.json

1. 读取当前 `picks/picks.json`
2. 将新 pick 的 JSON 追加到数组末尾
3. 写入文件，保持格式化（2 空格缩进）

### Step 6: 更新 Draft 状态

将 `picks/drafts/{slug}.md` 的 frontmatter `status` 改为 `published`。

### Step 7: 输出确认

```
## 发布完成

✓ {slug} 已写入 picks.json
✓ draft 状态已更新为 published
✓ picks.json 现有 {N} 个 picks

下一步：
- 用 /twitter-post {slug} 生成推文
- 用 /tiktok-script {slug} 生成短视频脚本
```

## 重要规则

- **必须预览确认**——picks.json 是官网直引文件，写入前必须让用户确认
- **不覆盖**——slug 重复时停止，不要覆盖已发布内容
- **JSON 格式严格**——2 空格缩进，和现有 picks.json 风格一致
- **date 用当天**——发布日期就是今天
- **不要修改已有 picks**——只追加新条目到数组末尾

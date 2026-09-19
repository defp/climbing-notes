---
name: article-translate
description: 翻译攀岩/技术文章并归档的工作流。当用户给出一个文章 URL 要求"下载并翻译"、"翻译成中文"、"存档翻译文章"时使用。流程：下载原始 HTML → 提取英文正文 → 翻译为中文 → 下载图片 → （可选）用 herdr 开 codex agent 审校 → 写校订记录。
---

# 文章翻译归档流程

把一篇在线文章翻译为中文并按项目约定归档。全程在 git 仓库根目录（climbing 项目）下操作。

## 目录与命名约定（必须遵守）

每篇文章一个目录，目录名是英文 slug（从 URL 或标题推导，小写连字符）：

```
<slug>/
├── <slug>.html              # 原始 HTML 存档（必须下载）
├── <slug>.en.md             # 提取的英文正文（Markdown，图指向本地 images/）
├── <slug>.zh.md             # 中文译文（结构与 en.md 一致，图指向本地 images/）
├── images/                  # 正文引用的图片，按出现顺序 01.jpg、02.png …
└── translation-review.zh.md # 审校记录（有审校时生成）
```

硬性规则：

- 文件名只用 `en` / `zh` 语言后缀，**禁止**用"中文翻译"之类中文命名文件。
- 必须下载原始 HTML（`<slug>.html`），不要只保存 Markdown。
- 图片下载到 `images/`，两位数字顺序命名，保留原扩展名；en.md 和 zh.md 都引用本地相对路径 `images/NN.ext`。
- en.md / zh.md 开头带元信息头（对照已有文章如 `garda-hitch/garda-hitch.en.md`）：

```markdown
# 标题

- Author: <作者>
- Published: <发布日期>
- Source: [<站点名>](<原文 URL>)
```

## 步骤

### 1. 下载原始 HTML

```bash
mkdir -p <slug>/images
curl -sL -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0 Safari/537.36" \
  "<原文 URL>" -o <slug>/<slug>.html
wc -c <slug>/<slug>.html   # 确认不是 0 字节或错误页
```

### 2. 提取英文正文 → `<slug>.en.md`

用 Python 从 HTML 中定位正文容器（Squarespace 是 `<article>`，其他站点找对应正文标签），按顺序提取标题/段落/列表/图片 URL，输出为 Markdown。正文之外的导航、页脚、推荐文章一律丢弃。保留所有正文图片 URL。

### 3. 下载图片

按正文出现顺序下载到 `images/`，两位数字命名（`01.jpeg`、`02.jpeg`…），en.md 中的远程 URL 替换为本地相对路径。

### 4. 翻译 → `<slug>.zh.md`

- 逐段对照翻译，段落数、图片数与 en.md 一致，不漏译。
- 标题、元信息头同样翻译（Author→作者，Published→发布日期，Source→原文）。
- 术语原则：
  - 有通行中文译名的用中文，首处保留英文对照，如"主连接点（masterpoint）"。
  - 器材/结名保留英文原名：Quad、Magic X、BHK（Big Honkin' Knot）、cordellette（辅绳环）、cam（机械塞）、nut（岩塞）。
  - carabiner 泛指时译"锁扣"，不要一律译"主锁"（主锁易被理解为锁门主锁）。
  - 单位保留原值并在首处给换算，如 4 英尺（约 1.2 米）、7mm。
- 人名、品牌、URL 不译。

### 5. 审校（可选，需要 Herdr 环境）

当 `HERDR_ENV=1`（当前在 herdr pane 里）且用户要求 review 时，开 codex agent 审校：

```bash
herdr pane layout --pane "$HERDR_PANE_ID"                          # 宽 pane 向右分，窄/高向下分
herdr pane split --current --direction right --cwd "$PWD" --no-focus   # 记录返回的 pane_id
herdr agent start reviewer --kind codex --pane <pane_id>
# 若返回 agent_not_ready，先 read 看是否卡在启动确认/更新提示：
herdr agent read reviewer --source recent-unwrapped --lines 40
herdr agent send-keys reviewer <key>     # 如 2 / enter / esc
herdr agent wait reviewer --until idle --timeout 30000
herdr agent prompt reviewer "请审校中文翻译质量。对照英文原文 <slug>/<slug>.en.md 与中文译文 <slug>/<slug>.zh.md。检查：1) 漏译/错译；2) 术语准确性；3) 中文表达。直接修改 zh.md 修正问题，并给出修改摘要。" --wait --timeout 600000
# 若 timeout 但状态是 working，继续 wait：
herdr agent wait reviewer --until idle --timeout 600000
herdr agent read reviewer --source recent-unwrapped --lines 120
```

注意：`agent start` 不会自己分屏，必须先有 idle shell pane；误触发的输入用 `esc` 打断后再 prompt。

### 6. 写审校记录 `translation-review.zh.md`

有审校时生成，格式参照 `garda-hitch/translation-review.zh.md`：校订日期、术语对照表（原文术语／中文处理）、主要修改点摘要。

## 完成检查清单

- [ ] `<slug>.html` 存在且非空
- [ ] en.md / zh.md 段落数一致，图片数量一致且都指向 `images/` 本地文件
- [ ] 文件名无中文
- [ ] 无残留临时文件（如 *-text.txt）

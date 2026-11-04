---
name: literature-table
description: |-
  Whenever the user mentions collecting, organizing, or formatting literature, papers, references, or citations into a table, use this skill to parse the provided data and generate a structured table with full metadata. Trigger on phrases like "整理文献", "文献表格", "汇总参考文献", "把论文整理成表格", "literature table", "organize my references", "citation table", or any request to extract and tabulate publication information — even if the user does not explicitly say "table." Also trigger when the user pastes BibTeX, citation lists, or semi-structured text describing multiple papers and needs them organized. This skill handles both free-form text (e.g. copy-paste from websites, PDFs, emails) and structured input (BibTeX, JSON) and outputs both a Markdown table and a downloadable CSV file.
---

# Literature Table

将用户提供的文献数据解析并整理为标准化的文献表格，同时输出 **Markdown 表格** 和 **CSV 文件**。

## 工作流程

### 1. 解析输入

根据用户提供的数据格式自动识别并解析：

**自由文本**（从网页、PDF、邮件等粘贴的非结构化文本）：
- 识别每条文献记录的分隔边界（空行、序号、换行等）
- 从每条记录中提取：作者、年份、标题、期刊/来源、DOI/链接、关键词、摘要/备注
- 如果某字段缺失，留空即可，不要编造
- 作者名保持原始格式（"LastName, FirstName" 或 "FirstName LastName" 均可），多人作者用分号或逗号分隔

**BibTeX**：
- 解析标准 BibTeX 字段：`author`、`title`、`journal`/`booktitle`、`year`、`doi`、`url`、`keywords`、`abstract`/`note`
- 将 `author` 字段中的 `and` 分隔符转换为可读格式

**JSON**：
- 接受包含文献对象数组的 JSON，字段名不区分大小写
- 常见键名映射：`authors`/`author`、`year`/`date`、`title`、`journal`/`source`/`venue`、`doi`、`url`/`link`、`keywords`/`tags`、`abstract`/`notes`/`summary`

### 2. 构建表格

按以下 7 列整理所有文献：

| # | 列名 | 说明 |
|---|------|------|
| 1 | **作者** | 全部作者列出，多人用分号分隔 |
| 2 | **年份** | 发表年份，未知则留空 |
| 3 | **标题** | 论文/文章完整标题 |
| 4 | **期刊/来源** | 期刊名、会议名、出版社或网址 |
| 5 | **DOI/链接** | DOI 号或可访问的 URL 链接 |
| 6 | **关键词** | 逗号分隔的关键词，有助于分类检索 |
| 7 | **备注/摘要** | 简短摘要或用户添加的备注（截取前150字） |

### 3. 输出格式

同时生成两种输出：

#### Markdown 表格

在对话中直接展示：

```markdown
| # | 作者 | 年份 | 标题 | 期刊/来源 | DOI/链接 | 关键词 | 备注/摘要 |
|---|------|------|------|-----------|----------|--------|-----------|
| 1 | Zhang et al. | 2023 | Deep Learning for NLP | ACL | https://... | NLP, DL | Proposed a novel... |
| 2 | Li & Wang | 2022 | ... | ... | ... | ... | ... |
```

要求：
- 表格对齐清晰，标题加粗
- 文献按年份降序排列（最新在前）
- 表格下方附一条统计信息："共 N 篇文献"

#### CSV 文件

生成 UTF-8 BOM 编码的 CSV 文件（确保在 Excel 中正确显示中文），保存到用户当前工作目录下的 `literature_table.csv`：

```csv
序号,作者,年份,标题,期刊来源,DOI链接,关键词,备注摘要
1,"Zhang et al.",2023,"Deep Learning for NLP","ACL","https://...","NLP, DL","Proposed a novel..."
```

要求：
- 使用逗号分隔，包含标题行的字段用双引号包裹
- UTF-8 BOM 编码（`﻿` 开头）
- 告知用户文件的完整保存路径

### 4. 特殊情况处理

- **文献数量超过 20 条**：先展示前 20 条在 Markdown 中，说明还有 N 条未显示，CSV 文件包含全部
- **作者名格式不一致**：尽量统一为 "LastName, FirstName" 或中文全名，不要混用
- **同一作者多篇同年**：在年份后加字母区分（2023a, 2023b），CSV 中保留此标记
- **用户提供的数据有歧义**：标注不确定的部分，询问用户确认

## 示例

**输入（自由文本）：**
```
Zhang et al. (2023). Deep Learning Approaches for Natural Language Processing. ACL 2023. https://doi.org/10.xxx

Li, X., & Wang, Y. (2022). A Survey on Transformer Models. Journal of Machine Learning Research, 23(1), 1-48. Keywords: transformer, attention, survey
```

**输出：** 在对话中展示 Markdown 表格，同时生成 `literature_table.csv` 文件到当前工作目录。

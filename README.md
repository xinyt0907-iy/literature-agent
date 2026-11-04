# 学术文献收集与整理智能体 · Literature Agent

> 从科研用户文献整理的效率痛点出发，基于 **Claude Code + MCP** 打造的「检索 → 去重 → 元数据抽取 → 标准化 → 导出」全流程自动化智能体。

> 📽️ 演示录屏：录制一段「粘贴文献 → 自动生成表格」的操作，保存为 `docs/demo.gif`，在下方以图片形式插入。

## 解决了什么问题

写文献综述时，通常要在多个数据库反复检索、手动复制粘贴、再手工排版成表格，重复劳动多、格式不统一、容易出错。本智能体把「人找信息、人做表格」变成「一句话需求 → 结构化结果」。

## 核心功能

- **多源学术检索**：接入 arXiv、PubMed、bioRxiv/medRxiv、Semantic Scholar、Crossref、OpenAlex、Google Scholar、IACR 等 10+ 数据源，跨库检索与自动去重。
- **多格式解析**：自动识别自由文本 / BibTeX / JSON 三种输入格式。
- **标准化整理**：抽取作者、年份、标题、期刊、DOI、关键词、摘要 7 类元数据，按年份降序排列。
- **一键导出**：生成 Markdown 表格 + UTF-8 BOM CSV（Excel 打开无乱码）。
- **复合条件筛选**：支持「引用特定经典文献 + 指定研究方法 + 期刊收录类型」等组合条件。

## 产品架构

```
用户需求（一句话）
      │
      ▼
┌──────────────────────────────┐
│  检索层  paper-search-mcp     │  ← arXiv / PubMed / Semantic Scholar /
├──────────────────────────────┤    Crossref / OpenAlex / Google Scholar…
│  整理层  literature-table      │  ← 解析(自由文本/BibTeX/JSON)
├──────────────────────────────┤    → 7列元数据 → Markdown + CSV
│  自动化层  playwright          │  ← 真实浏览器渲染场景兜底
└──────────────────────────────┘
      │
      ▼
标准化交付物（Markdown 表格 + CSV）
```

## 使用效果

**输入**（一段杂乱文献文本）→ **输出**（标准化表格 + CSV），完整样例见 [`demo/`](demo/) 目录。

| 场景 | 传统方式 | 本智能体 |
|------|---------|---------|
| 多库检索 | 逐个网站手动搜 | 一次跨库聚合去重 |
| 文献制表 | 手工复制粘贴排版 | 自动抽取 7 类字段 |
| 导出 Excel | 易乱码、格式乱 | UTF-8 BOM CSV 直接打开 |

## 技术栈

- Claude Code（智能体框架）
- Model Context Protocol（MCP）多服务接入
- Python（paper-search-mcp 数据源）
- Playwright（浏览器自动化）

## 目录结构

```
├── skills/literature-table/   # 文献整理技能（解析 → 制表 → 导出）
├── mcp.json                   # MCP 数据源配置
├── demo/                      # 演示：输入样例 → 输出结果
└── examples/                  # BibTeX 输入样例
```

## 快速开始

1. 将 `skills/literature-table/` 放入 Claude Code 的 skills 目录；
2. 将 `mcp.json` 合并到 Claude Code 的 MCP 配置；
3. 在对话中粘贴文献（自由文本 / BibTeX / JSON），即可自动生成表格与 CSV。

---
name: docs-naming
description: Naming and formatting conventions for documents in /Users/a1/work/work_space. Use whenever creating or recording (1) 解析文档 code-repo structure analysis, (2) 开发文档 development-task docs, (3) 问题记录文档 issue/problem records, or (4) 论文/技术报告总结文档 paper & tech-report summaries. Triggers include parsing a code repo's structure, starting a dev task, logging a bug, or summarizing a paper/arXiv preprint/technical report. Default creator is Li Cheng.
---

# 工作区文档命名规范 Skill

在 `/Users/a1/work/work_space` 下创建/记录文档时，按本规范统一命名与格式。共四类文档，完整规范见：

- 解析文档：`/Users/a1/work/work_space/docs-naming-convention/解析repo文档命名规范.md`
- 开发文档：`/Users/a1/work/work_space/docs-naming-convention/开发文档命名规范.md`
- 问题记录：`/Users/a1/work/work_space/docs-naming-convention/问题记录文档命名规范.md`
- 论文总结：`/Users/a1/work/work_space/docs-naming-convention/论文技术报告总结文档命名规范.md`
- 示例：`/Users/a1/work/work_space/docs-naming-convention/examples/`

需要细节或边界情况时，Read 对应规范文件；常规创建按下面速查即可。

## 通用元信息头（所有文档，文件首行起）

```markdown
<!--
name: <主题，与文件名一致>
creator: Li Cheng
created: YYYY-MM-DD
modified: YYYY-MM-DD
-->
```

- `creator` 默认 **Li Cheng**（他人创建改实际姓名）。
- `created` 创建后不变；每次实质修改更新 `modified` 为当日。
- 今日日期以会话上下文中的 currentDate 为准。

## 1. 解析文档（记录代码仓结构）

- 目录：`<代码仓名>-docs/`（如 `Megatron-LM-docs/`）。
- 文件：`<两位序号>[.子序号]-<中文主题>.md`，如 `02-并行化子系统.md`、`02.0-…`。
- 必备：`00-README.md`（导航）、`01-框架透视图解.md`（架构总图）。
- 无状态字段。

## 2. 开发文档（一项开发任务）

- 文件：`<YYYYMMDD>-<当日序号NN>-<语义清晰的任务主题>.md`，如 `20260701-01-登录接口重构.md`。
  - 以日期开头；同一天用 `-01` `-02` 递增。
  - **禁止** `开发文档1` 这类无语义命名。
- 元信息头追加 `related_issues:`（关联问题文档，可空）。
- 正文首行标状态（三阶段，只进不退）：`**状态**：未启动 | 开发中 | 已完成`。
- 推荐小节（空节可省）：目标与背景 / 影响范围 / 实现方案 / 进展记录（按日期追加）/ 关联问题 / 验证与结果。原则：能一屏看懂。

## 3. 问题记录文档（一个问题）

- 文件：`<YYYYMMDD>-<当日序号NN>-<问题简述>.md`，如 `20260701-02-登录超时问题.md`（命名同开发文档）。
- 元信息头追加 `related_dev:`（关联开发文档，可空）。
- 正文首行标状态（两阶段）：`**状态**：未修复 | 已修复`。
- 推荐小节：背景与环境（时间/仓/分支/commit/环境）/ 问题现象 / 复现步骤 / 原因分析 / 解决方案+修复时间 / 关联开发文档。必写清「时间、背景、现象」。

## 4. 论文 / 技术报告总结（一篇外部论文或报告）

- 目录：`<主题>-papers/`（如 `attention-papers/`），少于 5 篇可先统一放 `papers/`；必备 `00-README.md` 索引表。
- 文件：`<发表年份YYYY>-<来源标识>-<中文主题>.md`，如 `2022-FlashAttention-IO感知的显存高效注意力.md`。
  - 用**论文发表年份**开头（非阅读日期），目录排序即技术演进线。
  - 来源标识优先用方法/模型简称，无简称用机构或一作姓氏。
- 元信息头追加论文出处字段：`title`（英文原名照抄）、`authors`、`venue`、`year`、`url`、`code`、`tags`、`related_repo`、`related_dev`。其中 **`title` 与 `url` 必填**。
- 正文首行标状态（三阶段，只进不退）：`**状态**：速览 | 精读中 | 已消化`。允许长期停在「速览」。
- 推荐小节（★ 必写）：一句话总结 ★ / 背景与问题 / 核心思想 ★ / 实现细节 / 实验与结果 / 结论与局限 / **个人评述与启发 ★** / 关联资料 / 术语表。
- 原则：**总结 ≠ 翻译**，提炼 + 判断才有价值；摘录原文用引用块并标注章节（`> (§3.2) …`）与自己的话区分。
- 边界：只写「别人做了什么、我怎么看」。我们自己的实现进展进开发文档，落地踩的坑进问题记录文档。

## 文档间的对应关系

双向关联，互相可跳转：

- 开发文档：`related_issues` 元字段 + 正文「关联问题」列出问题文档名。
- 问题文档：`related_dev` 元字段 + 正文「关联开发文档」指回开发文档名。
- 独立于开发任务的问题，`related_dev` 留空。
- 论文总结：`related_repo` 指向已解析的 `<仓名>-docs/`，`related_dev` 指向因其发起的开发任务；两者均可留空。

## 创建流程

1. 判断文档类型（解析 / 开发 / 问题 / 论文总结）→ 选对应命名格式。
2. 写元信息头（creator 默认 Li Cheng，日期填当日；论文类补 `title` / `url`）。
3. 开发 / 问题 / 论文文档正文首行加 `**状态**`。
4. 涉及关联时，双向填 `related_dev` / `related_issues` / `related_repo`。
5. 拿不准格式就 Read 对应规范文件或 examples/ 下示例对照。

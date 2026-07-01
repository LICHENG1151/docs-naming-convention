---
name: docs-naming
description: Naming and formatting conventions for documents in /Users/a1/work/work_space. Use whenever creating or recording (1) 解析文档 code-repo structure analysis, (2) 开发文档 development-task docs, or (3) 问题记录文档 issue/problem records. Triggers include parsing a code repo's structure, starting a dev task, or logging a bug. Default creator is Li Cheng.
---

# 工作区文档命名规范 Skill

在 `/Users/a1/work/work_space` 下创建/记录文档时，按本规范统一命名与格式。共三类文档，完整规范见：

- 解析文档：`/Users/a1/work/work_space/docs-naming-convention/解析repo文档命名规范.md`
- 开发文档：`/Users/a1/work/work_space/docs-naming-convention/开发文档命名规范.md`
- 问题记录：`/Users/a1/work/work_space/docs-naming-convention/问题记录文档命名规范.md`
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

## 开发文档 ↔ 问题记录 的对应

双向关联，互相可跳转：

- 开发文档：`related_issues` 元字段 + 正文「关联问题」列出问题文档名。
- 问题文档：`related_dev` 元字段 + 正文「关联开发文档」指回开发文档名。
- 独立于开发任务的问题，`related_dev` 留空。

## 创建流程

1. 判断文档类型 → 选对应命名格式。
2. 写元信息头（creator 默认 Li Cheng，日期填当日）。
3. 开发/问题文档正文首行加 `**状态**`。
4. 涉及关联时，双向填 `related_dev` / `related_issues`。
5. 拿不准格式就 Read 对应规范文件或 examples/ 下示例对照。

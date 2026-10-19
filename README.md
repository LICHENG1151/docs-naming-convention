<!--
name: docs-naming-convention 索引
creator: Li Cheng
created: 2026-08-25
modified: 2026-10-19
-->

# 文档命名规范 · docs-naming-convention

> 本工作区（`work_space`）的文档规范库。**本页是 L1 索引**：由总导航 [`0-docs/README.md`](../README.md) §5 以「文件夹级」一条链接下钻至此，再由此指向各具体规范。
> 起草任何文档前，先按下表套对应规范。

---

## 1. 七类规范

| 规范 | 管什么 | 何时读 |
|---|---|---|
| [解析 repo 文档命名规范](./解析repo文档命名规范.md) | 记录代码仓结构的文档（`<仓名>-docs/`） | 解析一个代码仓 |
| [开发文档命名规范](./开发文档命名规范.md) | 一项开发任务的文档（含分支↔文档追踪） | 起一个开发任务 |
| [问题记录文档命名规范](./问题记录文档命名规范.md) | 一个问题的记录（含 UT 先行修改范式） | 登记一个问题 |
| [论文技术报告总结文档命名规范](./论文技术报告总结文档命名规范.md) | 一篇外部论文 / 技术报告总结 | 总结论文或报告 |
| [实验记录文档命名规范](./实验记录文档命名规范.md) | 一次实验 / 演练 / 基准测试（含证据规范、三原则） | 跑一次实验/演练下结论 |
| [索引与互链分级规范](./索引与互链分级规范.md) | 多篇之间怎么连、总导航怎么不臃肿（L0/L1/L2 三级下钻） | 建/改任何 README 或跨文档互链 |
| [**语言规范**](./语言规范.md) | **字怎么写**：说人话、禁自创名词、通用英文术语保持英文 | **写任何文档、回复、commit message 之前** |

> 前五类管**单篇怎么命名**，末一类管**多篇之间怎么连**，正交互补。

---

## 2. 配套

| 入口 | 说明 |
|---|---|
| [worktree 并行开发约定](./worktree并行开发约定.md) | 同一仓并行开发多个互不干扰特性时用 git worktree；一份计划、一条分支、一个目录，计划与实现分两阶段合入 main |
| [并行开发 skill 模板](./skill/repo-parallel-development/SKILL.md) | 设备、repo 与 worktree 路径使用占位符；可复制定制，包含同分支两阶段合入、main 历史、跨设备同步及清理规则 |
| [skill/](./skill/) | 已封装的 `docs-naming` skill（`SKILL.md` 为速查，含四类速查表）+ `structured-writing`、开发分支追踪约定 |
| [examples/](./examples/) | 各类规范的最小示例（解析 / 开发 / 问题 / 论文子文件夹 / worktree 并行开发） |

---

## 3. 通用约定（所有规范共用）

- **元信息头**：每篇 `.md` 首行起用 HTML 注释块记 `name` / `creator` / `created` / `modified`（各规范另有追加字段）。
- **默认创建人**：`Li Cheng`。
- **日期**：以会话上下文 currentDate 为准；`created` 创建后不变，每次实质修改刷新 `modified`。

## 4. 定制并行开发 skill

复制 `skill/repo-parallel-development/` 整个目录至自己的 skill 目录（如 `~/.codex/skills/`），替换 SKILL.md 中的 `{{...}}` 配置，或在使用时临时提供值。多项目可使用不同目录名，并同步修改 frontmatter 的 `name`，例如 `myrepo-parallel-development`。

只用同设备时，主仓和 worktree 根目录配合默认值即可；跨设备还要填开发机连接别名、开发机独立 clone 路径与共同 remote。PLAN_DIR 是仓内相对路径。模板不包含个人机器路径，也不会自动安装、连接远端或修改真实仓库。

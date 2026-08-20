---
name: structured-writing
description: Readability rules for any prose deliverable in work_space (dev docs, problem records, plans, design writeups, summaries) and any long explanation. Invoke whenever writing or editing a document, plan, or multi-sentence explanation. Core rule — break content into ordered numbered steps or tables with an explicit logical sequence (temporal or causal); never write long paragraphs / walls of text ("一坨"). Default creator Li Cheng.
---

# 有序分步写作 Skill（拒绝长段 · 必有逻辑序）

长段文字极度缺乏可读性。work_space 下**任何**文档 / 计划 / 说明，写或改时套用本规范。
本 SKILL.md 自身就是范例：全是短行、编号、表格，没有一坨。

## 铁律（6 条）
1. **拒绝长段**：连续解释超过 ~3 行，必拆成有序列表 / 表格 / 子项。
2. **必有逻辑序**：编号顺序只能是两种之一——**时间先后**（实现/执行步骤）或**因果先后**（因→果→果）。不许随意罗列。
3. **一条一义**：每个编号项只讲一件事；一句话能说清就别堆成段。
4. **先结论后展开**：每节 / 每类第一行给结论或要点，再列步骤。
5. **结构承载逻辑**：按内容形态选结构（见下表），让读者扫骨架就懂顺序。
6. **加粗骨架词**：关键名词 / 动作加粗，扫读即命中主干。

## 结构选型（按内容形态选一种）
| 内容形态 | 用什么结构 |
|---|---|
| 步骤 / 流程 / 时间线 | 有序列表 `1. 2. 3.` |
| 多维对照 / 分界 | 表格 |
| 因果推演 | `A → B → C` 或「因 X → 故 Y」 |
| 分类枚举 | 每类一个加粗小标题 + 其下短子项 |
| 单一结论 | 一句话加粗，不展开 |

## 写法：一类事拆成「1→N」
1. 每个场景 / 主题起一个**加粗小标题**。
2. 其下用 `1. 2. 3.` 列出**按发生顺序**（时间或因果）的步骤。
3. 步骤内因果用 `→` 串，不写「因为……所以……而且……」的长句。
4. 例外 / 升级条件单列一条（如「例外→…」），不塞进正常步骤里。

## 反例 → 正例
**反例（一坨）**：
> worker 崩了之后因为容器主进程还是 agent 所以 pod 不会退出，然后 ft_launcher 会重启 worker group 并且 reload ckpt 再 rendezvous，除非 max-restarts 耗尽才会退出升级换设备……

**正例（有序）**：
> 1. worker 子进程崩。
> 2. agent（容器 PID1）没退 → **pod 不释放**。
> 3. ft_launcher 重启整组 worker。
> 4. reload 上一个 ckpt → re-rendezvous → 续跑。
> 5. 例外：`max-restarts` 耗尽 → 退出升级换设备。

## 收尾自检（4 问，改完文档过一遍）
1. 有没有超过 3 行的段落？→ 拆。
2. 每个列表项是否单一职责？→ 拆。
3. 编号顺序有没有明确的时间 / 因果依据？→ 说不出就重排。
4. 每节第一行是不是结论？关键骨架词加粗了吗？

## 与 docs-naming 的关系
- `docs-naming` 管**命名 + 元信息头 + 四类文档骨架**（写在哪、叫什么、有哪些节）。
- 本 skill 管**每一节内部怎么写**（有序、分步、不写长段）。
- 两者叠加使用：先按 docs-naming 定结构，再按本 skill 填内容。

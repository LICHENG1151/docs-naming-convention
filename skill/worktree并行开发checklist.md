<!--
name: worktree并行开发checklist
creator: Li Cheng
created: 2026-08-25
modified: 2026-08-25
-->

# git worktree 多特性并行开发 checklist（skill 留档）

> `docs-naming` skill 的补充参考。权威规范见 [`../worktree并行开发约定.md`](../worktree并行开发约定.md)；本文件是**可操作 checklist**。
> 一句话：**一份开发计划 = 一条分支 = 一个 worktree 目录；主检出常驻主线，特性都在 worktree 里做，合入后移除目录。**

---

## 1. 何时用（一句决策）

**要不要「同时存在」两个工作现场？** 要 → worktree；不要 → 主检出里直接 `git switch -c dev/...`。

典型要用：并行推进 ≥2 个特性 / 中途插紧急 hotfix 不想打断现场 / 并排对比两个分支的运行。

## 2. 三层对应（目录 ↔ 分支 ↔ 文档）

```
开发文档   20260701-01-登录接口重构.md
   └─ 分支    dev/20260701-01-登录接口重构           # 元信息头 branch: 填这个
        └─ 目录   <仓名>.worktrees/20260701-01-登录接口重构/   # 分支去 dev/ 前缀
```

- 主检出 `<仓名>/` **常驻主线**（`main`/`master`），只做同步、开分支、合入。
- worktree 根：主检出**同级** `<仓名>.worktrees/`（仓外，不嵌套）。

## 3. 生命周期 checklist（在分支 checklist 上叠加目录动作）

- [ ] **立项**：建开发文档，`**状态**：未启动`，`branch:` 留空。
- [ ] **动工**：主检出内建 worktree（见 §4）→ 回填 `branch:` → 状态改 `开发中` → README「分支」列登记。
- [ ] **开发**：**在 worktree 目录内**改动、提交、装依赖、跑测试；进展记录按日期追加。
- [ ] **验证**：在该 worktree 跑通测试 / e2e；进展记录写验证结论。
- [ ] **合入**：切回主检出，从 `dev/...` 合入 `main`（PR/merge）。
- [ ] **收尾**：`git worktree remove` 移除目录 →（可选）`git branch -d dev/...` → 文档改 `已完成`，进展记录写合入 commit/PR，README 分支标「已合入」。

> 未通过验证不合入、不移除；目录保留继续迭代。

## 4. 命令速查（以 `ltp_ha` 仓为例，在主检出内执行 add）

| 动作 | 命令 |
|---|---|
| 新特性（建分支+目录） | `git worktree add ../ltp_ha.worktrees/20260701-01-登录接口重构 -b dev/20260701-01-登录接口重构` |
| 检出已有分支到新目录 | `git worktree add ../ltp_ha.worktrees/20260701-01-登录接口重构 dev/20260701-01-登录接口重构` |
| 列出所有 worktree | `git worktree list` |
| 合入后移除目录 | `git worktree remove ../ltp_ha.worktrees/20260701-01-登录接口重构` |
| 手删目录后清理登记 | `git worktree prune` |

## 5. 坑（git 硬规则 + 实践）

- 一个分支只能被一个 worktree 检出（git 硬限）——**正好契合**一份计划一条分支一个目录。
- `node_modules/` `venv/` `build/` 每个目录**各自安装/生成**（并行互不干扰的来源）；可配共享缓存省空间。
- 所有 worktree 共享同一 `.git` 配置 / hooks；submodule 需各目录各自 `git submodule update --init`。
- 手删目录务必 `git worktree prune`，否则 `list` 留幽灵项。
- 主检出别停在特性分支上——特性都在 worktree 做。

## 6. 与 README / 文档字段的衔接

- **不新增 README 列**：worktree 路径由约定推导（`<仓名>.worktrees/<分支后缀>/`），仍只维护「分支」列。
- **`branch:` 填分支名**（不是目录路径）——分支是长期锚点，目录是临时现场。
- 权威链仍是 `开发文档 ↔ 分支`（开发文档命名规范 §6）；本约定只补「分支 → 并行工作目录」一层。

<!--
name: worktree-并行开发示例
creator: Li Cheng
created: 2026-08-25
modified: 2026-10-19
-->

# 示例 · 同一开发分支先合计划，再合实现

> 返回 [规范库索引](../README.md)。以下为同设备示例，路径按实际配置替换。跨设备须在开发机独立 clone 管理 worktree，双方通过 remote push/fetch 同步。

## 1. 创建分支和现场

确认主检出常驻 main、工作区干净且 main 已更新；命令中的基线 main 必须是已核验版本。

```bash
git -C /path/to/repo worktree add -b dev/20261019-01-feature-a /path/to/repo.worktrees/20261019-01-feature-a main
```

另一个任务使用自己的 `dev/20261019-02-feature-b` 和同名后缀目录。每条线先登记计划，再进入实现。

## 2. 首个 commit 提交计划

在 feature-a 的 worktree 内编写 `docs/plans/20261019-01-feature-a.md`（示例路径，遵循项目实际目录），记录目标、影响模块、接口、依赖、负责人、验收标准，状态 `未启动`，`branch: dev/20261019-01-feature-a`。

```bash
git -C /path/to/repo.worktrees/20261019-01-feature-a add docs/plans/20261019-01-feature-a.md
git -C /path/to/repo.worktrees/20261019-01-feature-a commit -m "docs: 登记 feature-a 开发计划"
```

提交前确认暂存区没有其他任务或实现文件；必要计划索引可一同提交。

## 3. 第一次合入：审核计划

审视 main 中其他计划，确认范围和接口无未解决冲突；检查整个待合入差异只包含计划文档。以下本地命令须在审核完成、main 和分支 tip 未变化后执行：

```bash
git -C /path/to/repo merge --no-ff dev/20261019-01-feature-a -m "merge: 审核通过 feature-a 开发计划"
```

多人通过计划 PR 完成同样的审核与 merge commit。**保留 dev 分支及 worktree，关闭自动删分支，任务仍未启动。** 记录本次计划 merge hash。

## 4. 在原现场继续实现

```bash
git -C /path/to/repo.worktrees/20261019-01-feature-a merge main
```

跨设备应先 fetch，再同步权威 remote/main，不能使用落后的本地 main。开始实现后状态转 `开发中`，正常提交代码与验证记录；不创建第二条任务分支。各目录的端口、可写数据、构建产物及共享资源分别配置或协调。

## 5. 第二次合入：验收实现

合入前更新 main，在 dev 同步它、解决冲突，验证最新组合。以下仅在串行集成且 main 与已验收开发 tip 未变化时执行：

```bash
git -C /path/to/repo merge --no-ff dev/20261019-01-feature-a -m "merge: 验收通过 feature-a 实现"
```

多人从原 dev 分支新建实现 PR；第一次已关闭的计划 PR 不复用。第二次 merge 保留共享历史，不重复引入计划。核验权威 main 包含验收实现后，再以纯文档 commit 或文档 PR 回填 `已完成`、计划/实现两次 merge hash 和验收证据。

索引应区分：

| 计划 | 文档状态 | 合入情况 | 分支 |
|---|---|---|---|
| feature-a | 已完成 | 计划、实现均已合入 | dev/20261019-01-feature-a |
| feature-b | 未启动 | 计划已合入，实现未开始 | dev/20261019-02-feature-b |

## 6. 独立清理

实现验收合入后仍默认保留目录。核验完整开发 tip 已合入，检查 tracked/untracked/ignored 文件、产物与进程，获得开发人员对具体目录的明确同意后，才执行：

```bash
git -C /path/to/repo worktree remove /path/to/repo.worktrees/20261019-01-feature-a
```

仅计划合入时即使祖先检查通过也不能清理。删除本地/远端分支另需授权；remove 失败先调查，不加 force。feature-b 的现场继续保留。

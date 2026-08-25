<!--
name: worktree-并行开发示例
creator: Li Cheng
created: 2026-08-25
modified: 2026-08-25
-->

# 示例 · git worktree 并行开发两个特性

> [`../worktree并行开发约定.md`](../worktree并行开发约定.md) 的最小示例。
> 场景：`ltp_ha` 仓要**同时**推进「登录接口重构」和「推理缓存接入」两个互不干扰的特性。

---

## 1. 目录布局（主检出常驻主线，两特性各一目录）

```
0-code/training/inhouse/
├── ltp_ha/                                   # 主检出，常驻 main（只同步/开分支/合入）
└── ltp_ha.worktrees/                         # 该仓所有 worktree 的根（仓外同级）
    ├── 20260701-01-登录接口重构/              # ↔ dev/20260701-01-登录接口重构
    └── 20260702-01-推理缓存接入/              # ↔ dev/20260702-01-推理缓存接入
```

## 2. 建两个 worktree（在主检出 `ltp_ha/` 内执行）

```bash
# 特性 A：登录接口重构
git worktree add ../ltp_ha.worktrees/20260701-01-登录接口重构 -b dev/20260701-01-登录接口重构

# 特性 B：推理缓存接入
git worktree add ../ltp_ha.worktrees/20260702-01-推理缓存接入 -b dev/20260702-01-推理缓存接入

git worktree list
# .../ltp_ha                                     1b61e59 [main]
# .../ltp_ha.worktrees/20260701-01-登录接口重构   xxxxxxx [dev/20260701-01-登录接口重构]
# .../ltp_ha.worktrees/20260702-01-推理缓存接入   yyyyyyy [dev/20260702-01-推理缓存接入]
```

此后进入任一目录即普通仓：各自装依赖、`git commit`、并行跑测试，互不打扰。

## 3. 对应的开发文档元信息头

```markdown
<!-- 20260701-01-登录接口重构.md -->
branch: dev/20260701-01-登录接口重构        # 填分支名，不填目录路径

<!-- 20260702-01-推理缓存接入.md -->
branch: dev/20260702-01-推理缓存接入
```

## 4. README「开发文档一览」表（仍只维护「分支」列，worktree 路径不入表）

```
| 文档 | 状态 | 分支 | 一句话 |
|------|------|------|--------|
| 20260701-01-登录接口重构 | 开发中 | dev/20260701-01-登录接口重构 | 拆鉴权中间件 |
| 20260702-01-推理缓存接入 | 开发中 | dev/20260702-01-推理缓存接入 | KV cache 接入推理链路 |
```

## 5. 特性 A 验证通过 → 合入 → 移除目录

```bash
# 切回主检出合入主线
cd ../../ltp_ha
git switch main && git merge --no-ff dev/20260701-01-登录接口重构

# 移除该特性的 worktree 目录，并（可选）删分支
git worktree remove ../ltp_ha.worktrees/20260701-01-登录接口重构
git branch -d dev/20260701-01-登录接口重构
```

收尾：文档 A 状态改 `已完成`、进展记录写合入 commit/PR，README 分支标「已合入」；特性 B 的 worktree 不受影响，继续开发。

> 若曾手动 `rm -rf` 目录，记得 `git worktree prune` 清理登记，否则 `git worktree list` 会留幽灵项。

<!--
name: worktree并行开发约定
creator: Li Cheng
created: 2026-08-25
modified: 2026-10-19
-->

# 单 repo 多线并行开发约定

> 返回 [规范库索引](./README.md)。这是开发文档规范的并行扩展；仅对明确采用本模式的项目生效，不自动迁移其他项目。

一份计划对应一条 `dev/<计划名>` 分支和一个独立 worktree。main 检出负责计划登记与集成，开发在各自目录进行，合入串行执行，清理须经开发人员明确同意。

## 1. 方案判断与边界

推荐采用本模式。相比单目录反复切分支，它能保留多条开发线的现场；相比每项任务完整 clone，同设备的 worktree 共享对象库、减少重复存储。跨设备采用“每台开发机一个独立 clone + 多个 worktree”，通过 Git remote 同步。

worktree 隔离工作文件、index 和 HEAD，不保证语义无冲突，也不隔离端口、数据库、GPU、容器名称、外部服务及共享缓存。顺利合入依赖任务拆分、独立资源配置和最新 main 上的联合验证。

## 2. main 历史约定

- main 保留完整代码。约束的是启用本模式后的 **first-parent 历史**：只允许开发计划及其状态/索引的文档 commit，以及验收后开发分支的 merge commit。已有历史无需重写。
- 代码、配置、依赖、其他产品文档改动都通过开发分支进入 main。主检出不直接编写或修复这些内容。
- 合入使用 `--no-ff` 或托管平台的 merge commit 模式，不使用 squash / rebase merge / fast-forward 替代计划的 merge commit。
- tag 是指向 commit 的引用，不是一类 commit。版本 tag 指向已通过发布验收的 main commit，推荐 annotated tag；不移动已发布 tag。
- `git log --first-parent main` 用于查看计划和集成脉络；完整历史仍包含开发分支的普通 commit。
- 计划正文、验收记录和该计划索引回填属于允许的计划文档变更。登记时明确路径白名单，不能以“文档 commit”为名夹带代码。
- 合入 commit 的 hash 在合入后才能获得，通过随后一次计划文档 commit 回填；不要 amend merge commit 来写入它自身的 hash。

## 3. 路径与跨设备拓扑

| 参数 | 含义 |
|---|---|
| `MAIN_HOST` / `MAIN_REPO_DIR` | 主检出所在设备及绝对路径，常驻 main |
| `DEV_HOST` / `WORKTREE_ROOT` | 开发设备及 worktree 父目录的绝对路径 |
| `DEV_REPO_DIR` | worktree 的管理仓；同设备可等于 MAIN_REPO_DIR，跨设备为开发机上的独立 clone |
| `REMOTE_URL` / `REMOTE_NAME` | 两端使用的共同 Git remote，名称通常为 origin |

两个路径同设备时足够；跨设备还必须明确开发机连接方式和远端管理仓，不能只把本机的 worktree 路径写成远端路径。路径必须在其所属设备上解析。

```text
便携端 MAIN_HOST                  开发机 DEV_HOST
MAIN_REPO_DIR (main)              DEV_REPO_DIR (独立 clone)
        ↕ push / fetch                   ↕ 管理本机 worktree
             Git remote          WORKTREE_ROOT/
                                   20261019-01-feature-a/ → dev/20261019-01-feature-a
                                   20261019-02-feature-b/ → dev/20261019-02-feature-b
```

linked worktree 依赖管理仓的 Git 元数据，不能把 `.git` 指针跨设备拷贝后当独立仓使用，也不推荐用网络挂载共享活跃 `.git`。远端 clone 可常驻 main，但只有指定主检出/集成负责人负责推进权威 main。

目录名 = 分支去掉 `dev/` 的后缀 = 开发计划文件名去掉 `.md`。采用 `<YYYYMMDD>-<NN>-<主题>`，计划名不能包含路径分隔符；创建前验证分支名并检查目录与分支是否已存在。已有计划的缩写命名保留明确映射，不强行重命名。

WORKTREE_ROOT 推荐为 `<仓名>.worktrees`，可按用户配置位于其他磁盘或设备，必须在代码检出目录之外，不能嵌套。普通 clone 的 worktree 共享对象库、分支引用和多数配置；默认不允许同一分支在同一管理仓中重复检出。不同 clone 没有这项互斥保护，因此每条分支应只有一个约定写入者。

## 4. 开发到合入

1. **立项**：在 main 记录计划、影响范围、依赖、验收标准和预计分支名；按开发文档规范记录状态。先提交计划，再记录这个基线 commit，并同步到共同 remote。未创建分支时 `branch:` 可留空。
2. **创建现场**：确认仓库身份、设备、main 基线、工作区状态及现有 worktree。同设备从已记录基线创建；跨设备先 fetch，并确认该计划 commit 已存在，再创建分支。禁止覆盖现有目录或重置已有分支。
3. **独立开发**：只在所属 worktree 改代码、提交和回填计划；依赖环境、构建输出、端口、容器名称和可写数据各自分配。共享 GPU/服务需预约或串行使用。避免各分支同时修改总索引，集中在集成时回填。
4. **持续验证**：按任务需要执行测试、review、集成实验，记录执行设备、命令、结果和 commit。需要行为测试的代码遵循先 Red 后 Green；纯文档不制造无意义测试。基线失败与本次失败区分记录。
5. **跟进 main**：合入前 fetch 最新 main，默认在开发分支 merge main，冲突在开发现场解决并重新验证。未合入任务间若有依赖，明确依赖顺序；不要偷偷 merge 另一条未验收开发分支。
6. **串行集成**：同一时间仅一个集成者推进 main。冻结并记录待合入分支 tip 和当前 main hash，验证它们组合后的候选结果。需要远端运行时把候选 commit 送到开发机/CI，在独立验证现场运行，不切换正在工作的开发目录。
7. **合入与核验**：确认 main 仍是已验证基线、分支仍是已验证 tip，再以 merge commit 合入。main 或 tip 有变化，重新集成并补足受影响验证。若采用临时候选分支，可将验证过的候选 merge commit 以 fast-forward 提升到仍处于其第一父节点的 main；这里提升已有 merge commit 不等于直接 fast-forward 开发分支。发布到 remote 使用普通 push；被拒绝时重新 fetch 和集成，不 force push。
8. **回填**：验证合入可从权威 main 到达后，计划状态改为 `已完成`，记录 merge hash、分支 tip、验收证据与合入时间，索引标“已合入”。发布版本时另行确认发布范围和 tag 名，不能仅因完成开发就自动发布。

不能只凭两个分支各自测试通过就宣称组合通过。若使用托管平台合入，以最新目标分支的候选合并结果完成 CI/验收，并启用相应分支保护或 merge queue；本地流程也遵守同一约束。

建议记录：计划 ID、分支、负责人、设备与目录、基线 hash、验收 tip、验证命令和结果、merge hash、是否已同步 remote、清理状态。目录是临时现场，分支和 commit 才是长期追踪信息。

## 5. 清理是独立动作

合入后默认保留 worktree，直到适当时机开发人员明确同意删除该具体目录。批准合入不等于批准清理；批准清理目录不等于批准删除本地/远端分支。

清理前逐项确认：

- 重新 fetch 权威 main，以 `git merge-base --is-ancestor <开发分支-tip> <权威-main-ref>` 确认整个分支 tip 已合入；合入后新增 commit 不能被遗漏。
- 检查 tracked、untracked 和 ignored 文件。普通 `git status` 不能覆盖 ignored 的日志、模型或实验产物；须另行盘点并保留有用数据。
- 确认没有运行中的测试、训练、编辑器任务或其他使用者仍依赖该目录；必要日志与证据已归档。
- 给开发人员具体设备、绝对路径、分支、合入证据和待处理产物，并获得明确同意。
- 在管理仓所属设备执行 `git worktree remove <目录>`，不用 `--force` 或 `rm -rf`。拒绝删除时检查原因，不自动加 force。
- 目录清理后核对 `git worktree list` 并记录时间。分支删除另行获得授权；远端开发 clone 的本地 main 可能落后，不能因 `branch -d` 拒绝就改用 `-D`。

`git worktree prune` 仅清理失效管理记录，不代替目录清理；远端/可移动磁盘暂时不可达时不能据此判定现场已失效。

## 6. 与 superpowers 及现有规范的关系

沿用 superpowers 的设计讨论 → 开发计划 → 隔离工作目录 → 分步实现与测试 → review → 验收收尾。它是一套开发节奏，Git 拓扑和集成规则仍需本文明确。是否启用多 agent 由实际任务和授权决定，不是采用 worktree 的前提。

开发文档的状态仍为 `未启动 / 开发中 / 已完成`；分支验收通过但尚未合入时保持 `开发中`，在进展中注明已验收待合入。清理状态单独记为保留/待同意/已清理，不影响已完成状态。

可移植 skill 入口见本目录索引的 `repo-parallel-development` 模板。模板中的设备、路径均由使用者填写；这份规范不授权修改任何真实仓库，也不自动提交、push、发布 tag 或清理目录。

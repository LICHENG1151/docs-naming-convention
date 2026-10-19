---
name: repo-parallel-development
description: 在明确采用计划分支与 worktree 模式的单 repo 中组织多线并行开发、跨设备同步、验收合入 main 及经开发人员同意后的现场清理。
---

# 单 repo 多线并行开发

## 实例配置

以下是有意保留的模板占位符。用户可在本次会话提供值，或复制本 skill 后替换；未解析的配置不得作为命令参数执行。只询问当前步骤必需且无法从上下文确认的值。

```yaml
MAIN_HOST: "{{MAIN_HOST}}"
MAIN_REPO_DIR: "{{MAIN_REPO_DIR}}"
DEV_HOST: "{{DEV_HOST}}"
WORKTREE_ROOT: "{{WORKTREE_ROOT}}"
DEV_REPO_DIR: "{{DEV_REPO_DIR}}"
REMOTE_URL: "{{REMOTE_URL}}"
REMOTE_NAME: "{{REMOTE_NAME}}"
PLAN_DIR: "{{PLAN_DIR}}" # 仓内相对路径；遵循项目已有文档规范
```

同设备 DEV_REPO_DIR 可等于 MAIN_REPO_DIR；跨设备必须使用开发机独立 clone 管理 worktree，并通过共同 remote 同步。设备可写 local 或已配置 SSH 别名，路径必须是所属设备上的绝对路径。不要复制 linked worktree 的 .git 指针跨设备使用。不要在网络挂载上共享活跃 Git 元数据。

## 开始前

读取项目 AGENTS.md 与已有开发计划。用只读 Git 命令核对仓库 remote、当前分支、status、worktree list、git-common-dir。确认路径与设备匹配、main 检出常驻 main、现有现场属于哪个计划。遇到已有分支或目录，先核对并复用合适现场，不覆盖或重置。

应用当前会话已有授权；本 skill 本身不额外授权 push、发布、删除或跨项目操作。

## 不变量

- 一份计划 `<YYYYMMDD>-<NN>-<主题>.md` → `dev/<计划名>` → `WORKTREE_ROOT/<计划名>`，目录去掉 dev/ 前缀。不得含路径分隔符；创建前用 git check-ref-format --branch 检查。已有命名明确映射即可。
- 本模式启用后，main 的 first-parent 历史只新增计划及状态/索引的文档 commit 和验收后的 merge commit；main 包含完整代码，完整历史包含开发 commit。tag 是引用，指向发布验收通过的 main commit。
- 主检出不直接做代码开发；开发与冲突处理在各自 worktree。代码、配置、依赖和其他产品文件经开发分支合入。
- worktree 仅隔离文件现场。端口、构建产物、可写缓存、数据库、容器名称等独立配置；GPU/外部资源按项目规则分配。
- 一条分支一个写入者；同一管理仓不强制重复检出。不同 clone 也不能同时无协调写同一分支。

## 执行流程

1. main 提交开发计划与验收标准；记录计划基线 hash。在授权范围同步 remote，跨设备先 fetch 并核验计划 commit。
2. 从明确基线创建 dev 分支和 worktree。WORKTREE_ROOT 在代码检出目录之外。同设备和跨设备分别在对应管理仓执行 Git 操作。
3. 在所属 worktree 按计划实现、测试和回填证据。遵循项目测试规则；需要行为测试时先 Red 后 Green。review 和集成验证按实际风险进行，不因本 skill 自动派生多 agent。
4. 合入前 fetch 最新 main，在开发分支 merge main、解决冲突并重测。依赖另一项任务时明确合入顺序，不能夹带其未验收分支。
5. 集成串行执行。固定 main hash 和分支 tip，验证候选合并结果。若 main 或 tip 变化，重新集成并验证受影响行为；跨设备记录测试设备、命令、结果及候选 commit。
6. 使用 --no-ff 或平台 merge commit；不 squash、不 rebase merge。已测试的候选 merge commit 可在 main 仍为其第一父节点时 fast-forward 提升到 main。远端 push 被拒绝则重新 fetch/集成，不 force push。
7. 核验权威 main 包含开发 tip，计划改为已完成，并在随后计划文档 commit 记录 merge hash 和验收证据。不能把自身 hash 写入自身 commit。未合入保持开发中；清理状态另记。
8. 需要版本发布时按已有发布授权创建 annotated tag；开发验收完成不自动等于发布授权。

## 清理必须独立征得同意

默认保留已合入 worktree。清理前重新核验全部开发 tip 是最新权威 main 的祖先，检查 tracked/untracked/ignored 文件、实验产物和运行进程。给开发人员具体设备、绝对路径、分支与合入证据，获得删除该目录的明确同意后，才在管理仓所属设备执行 git worktree remove。

不使用 --force 或 rm -rf；失败时调查原因。删除本地分支、远端分支需另有明确授权，不能从“删除目录”推断。prune 只处理已确认失效的管理记录，不能对暂时离线现场盲目执行。记录清理结果并复核 worktree list。

## 结束回报

报告计划、分支、设备/目录、验证结果、main 合入情况与 commit，以及现场保留或清理状态。区分分支测试通过、候选合并测试通过、已合入、已同步 remote；未实际执行的步骤不得标完成。

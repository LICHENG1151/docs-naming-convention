---
name: repo-parallel-development
description: 在明确采用同一开发分支先合计划、再合实现的 worktree 模式的单 repo 中组织多线并行开发、跨设备同步、验收合入 main 及经开发人员同意后的现场清理。
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
- 本模式启用后，main 的 first-parent 历史包含计划审核 merge commit、实现验收 merge commit 及必要的计划变更/状态/索引文档 commit；初始计划从 dev 合入，不直接在 main 起草；main 包含完整代码，完整历史包含开发 commit。tag 是引用，指向发布验收通过的 main commit。
- 主检出不直接做代码开发；开发与冲突处理在各自 worktree。代码、配置、依赖和其他产品文件经开发分支合入。
- worktree 仅隔离文件现场。端口、构建产物、可写缓存、数据库、容器名称等独立配置；GPU/外部资源按项目规则分配。
- 一条分支一个写入者；同一管理仓不强制重复检出。不同 clone 也不能同时无协调写同一分支。

## 执行流程

1. 核验并记录最新 main 基线，在对应设备管理仓创建唯一 dev 分支和 worktree；跨设备先 fetch。WORKTREE_ROOT 在代码检出目录之外，不额外创建 plan/ 分支。
2. 第一个 commit 只提交计划及必要索引，写清范围、影响模块、接口、依赖、负责人、验收标准和 branch，状态未启动。计划审核合入前不提交实现。
3. 第一次合入只审核计划：结合 main 其他计划识别冲突；核验相对最新 main 的全部变更只含计划文档、被审核 tip 未变化。单人本地审核后 --no-ff 合入，多人计划 PR review 后 merge commit 合入。保留同一分支和 worktree，关闭平台合入后自动删分支；不能标记已完成。
4. 同一 dev 分支同步包含计划合入结果的 main，然后转开发中、开始实现。跨设备开发端 push 分支，集成端 fetch/merge/push main，再由开发端 fetch/sync。共享历史正常，不 squash/rebase merge、不重建任务分支。
5. 在所属 worktree 实现、测试、记录证据，按项目规则 review；需要行为测试时先 Red 后 Green。不因本 skill 自动派生多 agent。日常进展留在 dev；main 展示已审核立项计划而非实时进度。
6. 范围、接口、依赖、验收标准重要变化先审核并同步 main。dev 已有实现时，禁止 merge 整支提前带入代码；提取审核过且不依赖未合入代码的纯文档 patch，单人按授权应用到干净 main，多人遵守保护规则用临时文档 PR，再让 dev 同步 main。不能只检查 tip 文件而忽略其实现祖先；这不要求每个任务固定第二分支。
7. 第二次合入前 fetch 最新 main，在 dev merge main、处理冲突并验证候选组合。依赖任务明确合入顺序。串行集成，固定 main hash 与开发 tip；变化后重新集成并补足受影响验证，纯文档变化不机械要求全量代码测试。
8. 实现验收后 --no-ff 或平台 merge commit 合入；多人从原 dev 分支新建实现 PR，不复用已关闭计划 PR。已测试候选 merge commit 可在 main 仍为其第一父节点时 fast-forward 提升。push 被拒绝重新 fetch/集成，不 force push。
9. 核验权威 main 包含验收实现 tip 后才标已完成。随后纯文档 commit 或项目要求的文档 PR 回填计划 merge hash、实现 merge hash 与证据；不把自身 hash 写进自身 commit。索引区分计划已合入和实现已合入。暂停/取消单独说明，不冒充完成。
10. 发布依已有授权创建 annotated tag；实现验收不自动授权发布。两个必经阶段不限制重要计划更新产生额外文档提交或合入。

## 清理必须独立征得同意

默认保留 worktree。仅计划已合入时，即使开发 tip 是 main 的祖先也不能清理。先确认实现验收和第二阶段合入已完成；清理前重新核验全部开发 tip 是最新权威 main 的祖先，检查 tracked/untracked/ignored 文件、实验产物和运行进程。给开发人员具体设备、绝对路径、分支与合入证据，获得删除该目录的明确同意后，才在管理仓所属设备执行 git worktree remove。

不使用 --force 或 rm -rf；失败时调查原因。删除本地分支、远端分支需另有明确授权，不能从“删除目录”推断。prune 只处理已确认失效的管理记录，不能对暂时离线现场盲目执行。记录清理结果并复核 worktree list。

## 结束回报

报告计划、分支、设备/目录、验证结果、main 合入情况与 commit，以及现场保留或清理状态。区分计划已合入、实现开发中、候选合并测试通过、实现已合入、已同步 remote；未实际执行的步骤不得标完成。

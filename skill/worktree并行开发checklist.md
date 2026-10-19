<!--
name: worktree并行开发checklist
creator: Li Cheng
created: 2026-08-25
modified: 2026-10-19
-->

# worktree 并行开发 checklist

> 返回 [规范库索引](../README.md)。完整规则以规范库中的 worktree 并行开发约定为准。

- [ ] 确认项目启用本模式；配置主检出、开发设备、管理仓、worktree 根目录和共同 remote。
- [ ] main 提交计划，记录基线；跨设备 fetch 到开发机独立 clone。
- [ ] 检查已有现场；一计划、一 dev 分支、一目录，目录名去 dev/ 前缀。
- [ ] 所属目录内开发、测试、记录证据，隔离端口/数据/构建产物并协调共享资源。
- [ ] 跟进最新 main，在开发现场处理冲突并验证最终组合。
- [ ] 串行合入，固定已验证 main 与分支 tip，使用 merge commit；基线变化重新验证。
- [ ] 合入后回填计划状态、merge hash 与索引；tag 依发布授权另行处理。
- [ ] 默认保留现场；核验完整 tip 已合入、文件/ignored 产物/进程安全后，征得开发人员对具体目录的明确同意才 remove。
- [ ] 删除分支另行授权；不 force remove、不盲目 prune。

可复制的独立 skill 模板在 `repo-parallel-development/SKILL.md`，不依赖本机绝对路径。

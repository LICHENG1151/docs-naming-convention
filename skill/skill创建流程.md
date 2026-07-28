<!--
name: skill创建流程
creator: Li Cheng
created: 2026-07-01
modified: 2026-07-28
-->

# docs-naming Skill 创建流程与安装说明

本文件记录把「工作区文档命名规范」封装成 Claude Code skill 的完整过程，便于复现、维护与推送到 GitHub。

---

## 1. 背景

`docs-naming-convention/` 下已沉淀三份规范（解析 / 开发 / 问题记录）与示例。为让 Claude Code 在读代码仓、起开发任务、记录问题时**自动套用**这些规范，将其封装为一个 skill。

## 2. Skill 是什么

- Claude Code 的 skill 就是一个带 YAML frontmatter 的 `SKILL.md` 文件。
- frontmatter 必需字段：
  - `name`：skill 名，供 `/name` 调用。
  - `description`：**决定何时被触发**，需写清适用场景与关键词。
- 正文是给模型看的操作指引，采用「渐进式披露」：常用要点内联，细节指向外部规范文件（用时再 Read）。

## 3. 存放位置

Claude Code 从两处发现 skill：

| 位置 | 作用范围 |
|------|----------|
| `<项目根>/.claude/skills/<name>/SKILL.md` | 仅该项目 |
| `~/.claude/skills/<name>/SKILL.md` | 所有项目通用 |

本 skill 安装在项目级：`/Users/a1/work/.claude/skills/docs-naming/SKILL.md`（本会话工作根目录为 `/Users/a1/work`）。

## 4. 创建步骤（实际执行）

1. **梳理来源**：确认三份规范文件与 `examples/` 已就绪。
2. **设计 description**：覆盖三类触发动作——解析代码仓结构、开始开发任务、记录问题，并写明默认创建人 Li Cheng。
3. **编写 SKILL.md 正文**：内联「元信息头 / 三类命名格式 / 状态机 / 双向关联 / 创建流程」速查；细节指向 `docs-naming-convention/` 下规范与示例。
4. **落盘安装**：写入 `/Users/a1/work/.claude/skills/docs-naming/SKILL.md`。
5. **加指引**：在 `work_space/CLAUDE.md` 增加一行，让协作者与模型更易想到该 skill。
6. **归档备份**：将最终 `SKILL.md` 与本流程文档复制到 `docs-naming-convention/skill/`，纳入版本控制推送到 GitHub。

## 5. 安装到新机器 / 新环境

```bash
# 在目标项目根目录下（示例：/Users/a1/work）
mkdir -p .claude/skills/docs-naming
cp /path/to/docs-naming-convention/skill/SKILL.md .claude/skills/docs-naming/SKILL.md
# 或安装为全局 skill：
# mkdir -p ~/.claude/skills/docs-naming && cp .../SKILL.md ~/.claude/skills/docs-naming/
```

> 注意：`SKILL.md` 内引用的规范文件路径为绝对路径 `/Users/a1/work/work_space/docs-naming-convention/...`。若在别的机器/路径使用，需同步调整这些引用路径。

## 6. 验证

- 新开会话后输入 `/docs-naming`，能看到并调用即安装成功。
- 或在需要创建三类文档之一时，观察 skill 是否被自动触发。
- 当前会话的 skill 列表在启动时加载，新增 skill 通常需重启会话才在本会话可见。

## 7. 维护

- 规范内容变更时，**先改** `docs-naming-convention/` 下的规范文件，再同步更新两处 `SKILL.md`（安装位置 + 本备份），并更新各文件 `modified`。
- 保持备份 `SKILL.md` 与已安装 `SKILL.md` 内容一致。
- 新增文档类别时，需同步四处：① 新建规范文件 ② `SKILL.md` 的 `description`（决定触发）与正文速查 ③ `work_space/CLAUDE.md` 的类别数与说明 ④ 本文件变更记录。

---

## 8. 变更记录

### 2026-07-28 —— 新增第四类：论文 / 技术报告总结文档

- 新增规范 `docs-naming-convention/论文技术报告总结文档命名规范.md`。
- `SKILL.md`：`description` 增加第 4 类触发场景（总结论文 / arXiv 预印本 / 技术报告）；正文新增「4. 论文 / 技术报告总结」速查；「开发文档 ↔ 问题记录 的对应」一节改名为「文档间的对应关系」，补充 `related_repo`；创建流程改为四类判断。
- `work_space/CLAUDE.md`：三类 → 四类。
- **顺带修复**：已安装的 `/Users/a1/work/.claude/skills/docs-naming/SKILL.md` 中规范文件路径仍为旧值
  （`00-docs-naming-convention/文档命名规范.md`），目录与文件早已更名，链接失效已久；本次同步一并改正为
  `docs-naming-convention/解析repo文档命名规范.md`。两处 `SKILL.md` 现已完全一致。

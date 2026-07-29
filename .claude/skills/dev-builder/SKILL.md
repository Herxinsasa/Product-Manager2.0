---
name: dev-builder
description: 根据开发计划编写代码。按任务调度 implementer sub-agent，在 <project-name>/ 目录下创建项目。
---

# Dev Builder — 项目开发

## 职责

根据 `docs/plans/dev-plan.md` 中的任务清单，按任务调度独立的 `implementer` sub-agent 完成编码实现。在 `<project-name>/` 目录下创建完整项目。

## 触发场景

- docs/plans/dev-plan.md 已就绪，需要开始编码
- 用户说"开始开发"、"写代码"、"实现"

## 核心原则

1. **每个任务独立 sub-agent** — 使用 Agent 工具为每个任务创建新的 implementer 实例
2. **不继承上下文** — 每个 implementer 只接收当前任务的上下文
3. **两阶段审查** — 每个任务完成后先审查规格对齐，再审查代码质量
4. **编码完成必须独立审查** — 0-1 初期无 git hook 时，dev-builder 自驱动调度独立 code-reviewer（见 Step 4）
5. **简单优先** — 遵循 Karpathy 准则：最小代码解决问题

## 工作流程

### Step 1: 初始化项目

**1.1 确定项目目录与名称**

项目目录即当前工作目录（用户应在交付物项目目录启动会话）。项目名从 `docs/requirements/product-spec.md` 的产品概述提取；若 product-spec 未定项目名，停下问用户，**不得自行编造**。

**1.2 检查目录状态（不覆盖原则）**

- 若当前目录已有 `src/` 或 `projects/` 等代码结构 → **停下问用户**：「目录已有项目代码，继续往里加 / 新建别的目录 / 取消？」不得直接覆盖。
- 若是空目录或仅有 docs/ → 正常初始化。

**1.3 初始化**

- **确认 `.claude/` 和顶层 `CLAUDE.md` 已就位**：当前目录应有 `.claude/`（调度大脑）+ 根 `CLAUDE.md`（PM 入口版，从 PM 框架拷贝来的）。若缺失，提示用户从 PM 框架复制（结构 Y 即插即用），否则 skill/hook 无法工作、PM 身份无法激活。顶层 CLAUDE.md 不用 dev-builder 生成——它在拷贝时就是 PM 入口版，编码完成后由 release-builder 改写为项目自述版。
- 按用户指定或确认的技术栈，用对应脚手架创建项目骨架
- 项目代码放 `projects/<name>/`（遵循 harness-doc 目录规范）
- 初始化版本控制（`git init`，若用户需要）；git init 后按主控 Hooks 章节设置 `git config core.hooksPath .claude/hooks`

### Step 2: 逐任务实现
遍历 `docs/plans/dev-plan.md` 中的任务，对每个任务：

1. **创建 implementer sub-agent**：传入任务描述（含「设计参照」字段）、docs/architecture/tech-arch.md、docs/design/design-brief.md 路径
2. **等待实现完成**：implementer 返回 DONE / DONE_WITH_CONCERNS / NEEDS_CONTEXT / BLOCKED
3. **需求对齐审查**（强制，借鉴 grill-with-docs）：
   - 对照 `docs/plans/dev-plan.md` 中该任务的验收标准，逐条打勾确认 ✅/❌
   - 对照 `docs/requirements/product-spec.md` 领域术语表，检查实现的命名是否与术语一致
   - **如果任务有设计参照**：检查关键色值、间距、布局是否与 Design-Brief 规范一致
   - 对照 docs/architecture/tech-arch.md ADR，检查技术选型是否偏离架构决策
   - 发现有偏离 → 标记为 DONE_WITH_CONCERNS，附具体偏离说明（偏离了哪条验收标准/术语/ADR）
4. **更新任务状态**：在 docs/plans/dev-plan.md 中更新该任务状态（待执行→执行中→已完成），并在 Dev-Plan「执行记录」表追加一行（时间/任务ID/状态变化/Agent名称/Agent ID）。遇阻设为"阻塞"并记录原因。这是中断恢复的主状态源，务必及时更新。

### Step 3: 整体集成
所有任务完成后，确保各部分能正常集成运行。

### Step 4: 编码完成后自驱动审查（0-1 初期强约束）

0-1 阶段项目尚未 git init，pre-commit 门禁够不着，审查无 hook 兜底。dev-builder 完成编码（或每个里程碑）后，**必须主动调度 code-review**，不能裸奔：

1. 调度独立 `code-reviewer` sub-agent 审查本次代码变更（主 Agent 内部的"需求对齐审查"不算，必须是独立 sub-agent 签名）
2. 审查通过后，按 code-review SKILL.md「Step 5 写入审查状态」写入 `.claude/.review-status.json`——0-1 初期无 git 时哈希用文件内容 SHA256（`Get-FileHash -Algorithm SHA256`）
3. 审查未过 → 修复后重审，不进入下一步

> 为什么强制：0-1 初期是地基，最该审查；无 git hook 时靠 dev-builder 自驱动补位。git init 后，pre-commit 的 `review-check.ps1` 自动接管强约束，此步作为提交前的主动审查仍保留。

## 输出
- `<project-name>/` 目录下的完整项目代码
- `docs/plans/dev-plan.md` — 更新任务完成状态

## 参考资源
- [编码约束](../../references/harness-doc/coding-requirement/coding-req.md) — 通用编码约束（编码前必读，C++ 仅为示例，规则语言无关）
- [日志约束](../../references/harness-doc/log-requirement/log-req.md) — 日志规范约束
- [subagent-driven-development](../../references/superpowers/subagent-driven-development.md) — Sub-agent 驱动开发模式
- [karpathy-guidelines](../../references/andrej-karpathy-skills/karpathy-guidelines.md) — 编程准则
- `.claude/agents/implementer.md` — 实现者 sub-agent 定义
- `docs/plans/dev-plan.md` — 任务清单

## 注意
- 不要覆盖已有项目文件，和用户确认后再操作
- 如果遇到无法解决的问题，及时报告给用户

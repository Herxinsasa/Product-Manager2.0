# 产品经理（PM）— 0-1 产品开发工作流

> 基于 Claude Code 的 Skill 驱动开发框架。把脑子里的模糊想法，一路带到可运行、可发布的产品。

```
Skill 管流程  ·  Sub-agent 管执行  ·  Hook 管兜底
```

---

## 它是什么

一套**可复制的开发工作流框架**。你用自然语言描述产品想法，PM 自动路由到对应的 Skill，按 `需求 → 设计 → 架构 → 开发 → 审查 → 发布` 全流程推进，每一步都有文档沉淀、状态可恢复。

**框架与交付物分离**：PM 框架是工作台，每个产品是独立的交付物项目。把 `.claude/` 拷到目标项目即可启用。

---

## 特点与优点

| 优点 | 说明 |
|------|------|
| **全流程 0-1 覆盖** | 从需求澄清到发布上线，11 个 Skill 覆盖产品开发完整链路，不缺环 |
| **按需调度，无需记命令** | 说「我想做个日志分析工具」，自动路由到对应 Skill；不走固定流水线 |
| **中文原生 + 离线可用** | 全中文交互；参考文档与编码/目录规范（harness-doc）内置，断网也能用 |
| **Sub-agent 隔离执行** | 编码、审查由独立 sub-agent 在干净上下文完成，长会话质量不下降 |
| **中断可恢复** | 开发计划带任务状态机（待执行/执行中/已完成/阻塞/跳过），断了按任务状态精确续接 |
| **审查强约束** | pre-commit 哈希校验——暂存代码必须经独立 code-reviewer 审查且审查后未变更，否则阻止提交 |
| **设计到代码可追溯** | 设计稿路径回写设计规范 → 任务绑定设计参照 → implementer 读取 → 审查验证视觉一致性 |
| **规范内置** | harness-doc 提供通用编码约束、日志规范、目录规范，开发即遵循，无需另配 |
| **即插即用** | 纯 Markdown + PowerShell hook，无运行时依赖；拷一个 `.claude/` 目录就启用 |

---

## 快速开始

PM 框架与交付物项目**物理分离**。开发产品时，在**交付物项目目录**工作。

```bash
# 1. 新建交付物项目目录并进入
mkdir my-product && cd my-product

# 2. 从 PM 框架复制 .claude/（调度大脑）+ 根 CLAUDE.md（PM 入口版，流程启动靠它）
cp -r /path/to/Product-Manager-2.0/.claude .
cp /path/to/Product-Manager-2.0/CLAUDE.md .

# 3. 启动 Claude Code（必须重启，让 .claude/agents/ 的自定义 Agent 生效）
claude

# 4. 说出需求，系统自动路由
"我想做一个日志分析工具，可以拖入文件自动解析"
```

**两层 CLAUDE.md**：
- 根 `CLAUDE.md`（拷贝来的）= **PM 入口版**，含启动指令，激活 PM 身份。开发完成后由 `release-builder` 改写为**项目自述版**。
- `.claude/CLAUDE.md` = **调度大脑**，角色/规则/启动回档，框架真实启动入口，始终不变。

### ⚠️ 使用纪律

- **重开项目必须 cd 到交付物项目目录**——PM 身份靠项目里的 CLAUDE.md 链激活。在 PM 框架目录开会话无法续接项目进度。
- **框架在开发期冻结**——不在开发产品的同时改 PM 框架。框架优化是独立的事。
- **复制 `.claude/` 后必须重启 Claude Code**——否则自定义 Agent 不生效。

---

## 开发链路

```
模糊想法
  → (可选) structured-thinking    苏格拉底提问 + 第一性原理 + 奥卡姆剃刀
  → product-spec-builder           需求澄清 → docs/requirements/product-spec.md
  → design-brief-builder           设计规范 → docs/design/design-brief.md
  → design-maker                   设计稿   → docs/design/ui/
  → tech-architect                 技术架构 → docs/architecture/tech-arch.md
  → dev-planner                    开发计划 → docs/plans/dev-plan.md（任务状态机）
  → dev-builder                    编码（调度 implementer）→ projects/<name>/
  → code-review                    独立审查 → .review-status.json
  → bug-fixer                      失败修复（根因回退路由）
  → release-builder                构建/发布 + 文档移交
```

> 链路非强制顺序。主控根据当前输入和已有文档判断需要哪个 Skill，按需调度。

---

## Skill 一览

| 阶段 | Skill | 职责 |
|------|-------|------|
| 需求 | `product-spec-builder` | 多维度需求澄清（含背景、定位、功能、优先级、约束、设计参考） |
| 思考 | `structured-thinking` | 可选前置：苏格拉底提问 / 第一性原理 / 奥卡姆剃刀 |
| 设计 | `design-brief-builder` | 视觉规范 + 逐模块 UE 确认 |
| 设计 | `design-maker` | 产出原型/设计稿（Figma/Pencil/HTML/Canvas） |
| 架构 | `tech-architect` | 技术选型 + ADR + 复用清单 + 目录规范 |
| 开发 | `dev-planner` | 垂直切片任务拆分 + 任务状态机 |
| 开发 | `dev-builder` | 调度 implementer 编码 + 需求对齐审查 + 自驱动 code-review |
| 质量 | `bug-fixer` | 证据→复现→根因（含回退路由）→最小修复→复测 |
| 质量 | `code-review` | 调度 code-reviewer 审查 + 写审查状态 |
| 发布 | `release-builder` | 构建门禁 + 版本/发布 + 项目文档移交 |
| 元 | `skill-builder` | 创建新 Skill |

**Sub-agents**：`implementer`（编码，dev-builder 调度）、`code-reviewer`（审查，code-review 调度）。每次任务全新实例，不继承上下文。

---

## 架构

```
┌─ 项目经理（调度中枢）  .claude/CLAUDE.md — 角色、调度规则、启动回档
├─ 技能体系（11 Skill）  .claude/skills/   — 需求→设计→架构→开发→审查→发布
├─ Sub-agents（2）       .claude/agents/   — implementer / code-reviewer
└─ Hooks（3）            .claude/hooks/    — pre-commit 串联审查+编译门禁
```

**Hooks**：`pre-commit`（sh 壳）串联 `review-check.ps1`（审查状态哈希校验）+ `pre-commit-check.ps1`（编译检查，失败阻止提交并路由 bug-fixer）。0-1 初期项目未 git init 时，由 dev-builder 自驱动 code-review 补位。

---

## 文档结构（交付物项目）

```
<project-name>/
├── CLAUDE.md                # 开发期=PM入口版；完成后=项目自述版
├── .claude/                 # 从 PM 框架复制
├── docs/
│   ├── requirements/        # product-spec.md（PRD + 领域术语）
│   ├── design/              # design-brief.md + ui/（设计稿）
│   ├── architecture/        # tech-arch.md（架构 + ADR）
│   ├── plans/               # dev-plan.md（任务状态机）
│   ├── guide/  test/
│   └── changelog.md         # 变更记录（需求/架构/版本）
└── projects/<name>/         # 项目代码（遵循 harness-doc 目录规范）
```

---

## 设计原则

1. **按需调度** — 非固定流水线，按当前需要精准调起 Skill
2. **隔离执行** — Sub-agent 全新实例，不继承上下文
3. **先证据再修复** — Bug 修复前必须复现确认，禁止见错就改
4. **垂直切片** — 任务切穿全层，完成后可独立验证
5. **状态可恢复** — 任务状态机 + progress.json，中断不丢进度
6. **审查强约束** — 提交前哈希校验，未审查或审查后变更的代码提交不了
7. **规范内置** — 编码/日志/目录规范随框架走，开发即遵循

---

> 完整调度规则见 [.claude/CLAUDE.md](.claude/CLAUDE.md)。

---
name: code-review
description: 代码审查和质量检查。调度 code-reviewer sub-agent 审查代码，检查是否符合计划和编码标准。
---

# Code Review — 代码审查

## 职责

审查代码质量和风险，确保实现符合计划要求和编码标准。将审查任务分派给独立的 `code-reviewer` sub-agent。

## 触发场景

- Dev Builder 完成开发后自动触发
- 用户说"审查代码"、"代码审查"、"review"
- commit 前希望检查代码质量

## 工作流程

### Step 1: 确定审查范围
与用户确认审查范围：
1. 全量审查还是特定文件？
2. 审查重点是什么？（安全性/性能/架构/常规/视觉一致性？）
3. 是否参考了 docs/plans/dev-plan.md 中的技术方案？
4. 如果有设计交付物（docs/design/design-brief.md 交付物表），需要对照检查视觉一致性

### Step 2: 调度审查
创建 `code-reviewer` sub-agent 实例，传入：
- 审查范围（文件列表或变更范围）
- 审查重点
- 相关的计划文档（docs/plans/dev-plan.md）
- 设计规范（docs/design/design-brief.md，如有）
- 设计交付物路径（docs/design/design-brief.md「设计交付物」表格，如有）
- 项目编码规范

### Step 3: 处理审查结果
根据审查结论采取对应行动：

| 结论 | 处理方式 |
|------|----------|
| 通过 | 通知用户，可继续下一步 |
| 有条件通过 | 修复 Important 级别问题后继续 |
| 不通过 | 修复 Critical 问题后重新审查 |

### Step 4: 输出审查报告
将审查结果记录到审查报告中。

### Step 5: 写入审查状态（提交门禁）

审查通过或有条件通过后，写入 `.claude/.review-status.json`。先调用 `review-check.ps1 -Snapshot` 生成当前审查文件的内容哈希，再合并审查结论写入：

```text
1. 在项目目录执行：
   powershell -File .claude/hooks/review-check.ps1 -Snapshot -Files <审查涉及的代码文件>
   → 输出 { reviewed_files, reviewed_file_hashes, diff_fingerprint }
2. 合并快照与审查结论，写入 .claude/.review-status.json
```

`.review-status.json` 字段契约：

```json
{
  "review_agent_name": "code-reviewer",
  "review_agent_id": "<调度 code-reviewer sub-agent 时 Agent 工具返回的 ID>",
  "last_review": "<ISO 8601 timestamp>",
  "conclusion": "通过 | 有条件通过",
  "reviewed_files": ["src/main.cpp", "src/util.cpp"],
  "reviewed_file_hashes": { "src/main.cpp": "<git blob hash>", "src/util.cpp": "<git blob hash>" },
  "diff_fingerprint": "<review-check.ps1 -Snapshot 生成的 SHA256>"
}
```

> 此文件由 pre-commit 的 `review-check.ps1` 读取：提交前校验每个暂存代码文件的内容哈希都在 `reviewed_file_hashes` 中且一致（审查后改过代码会被拦下），并校验 `review_agent_name == "code-reviewer"` 且 `review_agent_id` 非空（主 Agent 内部自审不算，必须独立 code-reviewer 签名）。
>
> **0-1 初期无 git 时**：`review-check.ps1 -Snapshot` 用 `git hash-object` 取哈希会失败；此时改用文件内容 SHA256 填 `reviewed_file_hashes`（`Get-FileHash -Algorithm SHA256`）。git init 后改回 `git hash-object`，字段结构不变。

## 输出
- 代码审查报告（问题清单、严重级别、修改建议）
- 如果需要修复，标记待修复项

## 审查检查清单

code-reviewer 在审查时，如果任务涉及 UI 实现，**必须**检查以下视觉一致性项：

```
□ 色值与 docs/design/design-brief.md §1.3 配色方案一致
□ 圆角/阴影/间距与 docs/design/design-brief.md §1.6 组件视觉规范一致
□ 布局与对应模块 UE（§2.x）一致
□ 若有设计源文件（.pen / Figma MCP）→ 读取结构化数据，比对关键尺寸
□ 若仅有 PNG → 以 Design-Brief 文字规范为准，PNG 仅作辅助参照
□ CSS 变量 / Token 是否与设计规范的色值对应（禁止 hardcode 裸色值）
```

## 参考资源
- [编码约束](../../references/harness-doc/coding-requirement/coding-req.md) — 通用编码约束（审查时对照，规则语言无关）
- [日志约束](../../references/harness-doc/log-requirement/log-req.md) — 日志规范约束
- `.claude/agents/code-reviewer.md` — 审查者 sub-agent 定义
- [code-review-skill](../../references/code-review-skill/SKILL.md) — 多技术栈审查规则
- [requesting-code-review](../../references/superpowers/requesting-code-review.md) — 审查请求流程
- [code-reviewer](../../references/superpowers/code-reviewer.md) — 审查者角色参考

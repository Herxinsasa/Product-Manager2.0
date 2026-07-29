---
name: release-builder
description: 构建验证、版本管理与发布上线。多轮确认发布目标与推送路径后执行门禁与发布，写入状态与 release notes。用户提到发布、上线、deploy、打 tag、发版、merge 到 main、推远程、构建生产包、创建 PR 时，即使未说 release 也必须使用本 skill。
---

# Release Builder — 构建发布

**原则：** 先确认方案 → 再跑门禁 → 后执行。门禁任一项失败则报告并 **Stop**，不执行 push/deploy/tag。

**开场：** 「正在使用 release-builder 处理构建/发布。」

## 触发

`code-review` 通过后；或用户说发布/构建/上线/deploy/打 tag/推远程/合并 main；Dev-Plan 阶段 5 收尾后。

**前置：** `<project-name>/` 可构建；建议已完成 code-review（强制跳过须在记录中标注 `review_skipped`）。

---

## Step 1：读上下文 + 多轮确认方案

### 1.1 快速读上下文（为提问准备默认值）

并行扫描：`docs/architecture/tech-arch.md`（§1.3 部署/托管、构建工具、相关 ADR）、`docs/plans/dev-plan.md`（阶段 5、测试/构建命令）、`.claude/.review-status.json`、`docs/changelog.md` 最新条目、`projects/<name>/package.json` scripts、`docs/guide/build-and-deploy.md`、`git status` / `branch` / `remote -v`。

**Release notes 来源（按序，可合并）：** `docs/releaseNote.md` → `docs/releaseNotes/<version>.md` → `docs/changelog.md` → `git log`（自上一 tag）。

用户未指定的构建/部署/推送项，默认采用 Tech-Arch / build-and-deploy，标注「（来自 Tech-Arch，可改）」。

### 1.2 多轮确认（本步核心，确认完才进入 Step 2）

每轮复述选择并问「是否正确？」；全部完成后输出 **发布方案确认**，等用户回复 **「开始执行」** 前不得 commit/push/deploy/tag。

| 轮次 | 确认内容 |
|------|---------|
| **1 目标** | A 仅构建 / B 分支集成(PR·merge) / C 打 tag / D 部署 / E 组合；版本号；变更范围 |
| **2 构建** | 构建命令与产物路径；是否加 lint/类型检查 |
| **3 推送**（含 B/C/D/E） | 基准分支；集成方式(本地 merge / PR / 仅 push / 不碰 git)；**remote、目标分支、是否 --tags**；CI 触发分支/workflow |
| **4 部署**（含 D/E） | 平台与环境(preview/staging/prod)；部署命令；发布后验证方式 |

**确认摘要（示例）：**

```
目标 / 版本 / 构建命令→产物 / Git与推送(remote·分支·--tags) / 部署 / release notes 来源 / 审查结论
```

---

## Step 2：门禁验证（按方案执行，失败即 Stop）

仅跑方案中需要的项；**任一项失败 → 输出报告并 Stop，不进入 Step 3。**

| 门禁 | 规则 |
|------|------|
| **审查** | `.review-status.json`：`PASS` 继续；`CONDITIONAL_PASS` 须确认 Important 已修；`FAIL`/缺失 → Stop（除非用户声明跳过） |
| **测试** | 从 Dev-Plan / build-and-deploy / package scripts 取命令执行 |
| **构建** | 方案要求时执行；失败 Stop；warning 须用户接受 |
| **工作区** | 未提交变更仅提示；含 `.env`/密钥 → Stop |

---

## Step 3：执行

用户已说「开始执行」且门禁通过后，按确认方案操作。分支集成参考 [finishing-a-development-branch](../../references/superpowers/finishing-a-development-branch.md)。

| 目标 | 要点 |
|------|------|
| A 仅构建 | 输出构建报告即可 |
| B 集成 | merge 后在合并结果再测；PR 用 `gh pr create`；push 严格按确认的 remote/分支 |
| C 版本 | `git tag -a v<x>`；仅确认后才 `git push` tag；可选 `gh release create` |
| D 部署 | 按 build-and-deploy；Vercel 默认 preview，**生产须明确要求** |
| 收尾 | 按需删已合并分支；报告 `git status` |

---

## Step 4：发布记录与状态

### 4.1 发布记录

写入确认的 release note 路径（缺省 `docs/releaseNote.md` 或 `docs/releaseNotes/v<version>.md`），并更新 `docs/changelog.md`（轻量总览：版本 + 类型 + 一句话摘要，不重复审查/测试细节）。

```markdown
# Release v<version>
- 时间 / 类型(build|pr|merge|tag|deploy) / Git(branch@sha→remote/branch)
- 构建·测试·部署(URL) / 审查结论
## 变更摘要（来自 changelog/releaseNote/git log，禁止编造）
### 新增 / 修复 / 其他
## 验证清单 [ ]测试 [ ]构建 [ ]部署 [ ]远程符合预期
```

### 4.2 `.claude/.release-status.json`（每次结束必写）

```json
{
  "last_release": "<ISO8601>",
  "version": "v1.0.0",
  "release_type": "build-only|pr|merge|tag|deploy|combined",
  "status": "SUCCESS|FAILED|ABORTED",
  "git": { "branch": "", "remote": "", "target_branch": "", "commit": "", "tag": "", "pr_url": "" },
  "build": { "command": "", "success": true },
  "test": { "command": "", "success": true },
  "deploy": { "environment": null, "url": "", "success": true },
  "review": { "conclusion": "PASS|CONDITIONAL_PASS|skipped", "last_review": "" },
  "release_notes_path": [],
  "failure_reason": ""
}
```

### 4.3 `progress.json`（成功时）

`current_phase`: `released`；`milestones` 追加 `✓ v<version> 发布于 <date>`。

---

## Step 5：项目交付收尾（0-1 达成，文档移交）

编码完成、产品 0-1 达成后，交付物项目的顶层文档从「PM 入口版」移交为「项目自述版」，PM 功成身退。

### 5.1 改写顶层 `CLAUDE.md`（从 PM 入口 → 项目自述）

开发期顶层 CLAUDE.md 是 PM 入口版（启动指令 + 极简项目标识）。交付时改写为项目自述版：

```markdown
# <项目名>

## 项目是什么
<从 docs/requirements/product-spec.md 提炼：定位、背景、核心功能>

## 技术架构
<从 docs/architecture/tech-arch.md 提炼：技术栈、架构模式、目录结构>

## 编码与规范
<引用 docs/architecture/tech-arch.md §5 项目规范约束 + harness-doc 基线>

## 构建与运行
<从 docs/guide/build-and-deploy.md 提炼：构建命令、运行入口>
```

### 5.2 生成顶层 `README.md`（项目版）

从 product-spec / tech-arch / build-and-deploy 提炼，写人类入口：项目简介、快速启动、构建部署。

### 5.3 `.claude/` 去留（询问用户）

| 用户选择 | 处理 |
|---------|------|
| **保留**（交付后仍要迭代） | 保留 `.claude/`；顶层 CLAUDE.md 自述版末尾保留一行引线：「迭代时读 `.claude/CLAUDE.md` 激活 PM」 |
| **删除**（纯交付，独立维护） | 删除 `.claude/`；项目成为纯产品，无 PM 框架 |

> 必须询问用户，不得擅自删除 `.claude/`。删除前确认用户已备份或不再需要 PM 迭代能力。

---

## 红线

**禁止：** 门禁失败仍 push/deploy；未经同意 commit/push/--tags/force push/生产部署；未经确认删分支或丢弃提交；发布含密钥文件；改 git config；擅自 `--no-verify`。

**必须：** 先完成 Step 1 确认再跑门禁与执行；推送路径与 remote/分支须明确（缺省用 Tech-Arch 并标明）；merge 后再测；结束写 `.release-status.json`；变更摘要可追溯 changelog/releaseNote/git log。

## 参考

`docs/architecture/tech-arch.md` · `docs/guide/build-and-deploy.md` · `code-review`（`.review-status.json`）· [finishing-a-development-branch](../../references/superpowers/finishing-a-development-branch.md)

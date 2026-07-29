# 文档导航

> 本项目所有文档索引。AI 接手项目时先读这里。
> 生成时机：`tech-architect` 确定目录结构后；每次新增模块时更新模块索引。

## 快速入口

| 我想... | 去看 |
|---------|------|
| 了解产品定位和 MVP 功能 | [产品概述](requirements/overview.md) |
| 查业务术语含义 | [术语表](requirements/glossary.md) |
| 了解某个功能的交互流程 | [功能设计](design/functional/) |
| 看 UI 配色和组件风格 | [原型设计](design/visual/) |
| 了解系统架构和数据流 | [架构总览](architecture/overview.md) |
| 找到某个模块的代码位置 | [模块索引](modules/README.md) |
| 了解模块的实现细节 | [模块文档](modules/<模块名>/README.md) |
| 搭建开发环境 | [开发指南](guides/development.md) |
| 写测试/跑测试 | [测试指南](guides/testing.md) |
| 构建/部署 | [部署指南](guides/build-and-deploy.md) |
| 排查问题 | [问题排查](guides/troubleshooting.md) |
| 了解需求变更历史 | [../Product-Spec-CHANGELOG.md](../Product-Spec-CHANGELOG.md) |

## 权威源文档

> docs/ 是索引和摘要，不重复写详细内容。以下文件是权威数据源：

| 文档 | 由哪个 Skill 生成 | 归档到 |
|------|------------------|--------|
| Product-Spec.md | `product-spec-builder` | `requirements/overview.md`、`requirements/glossary.md` |
| Design-Brief.md | `design-brief-builder` + `design-maker` | `design/functional/`、`design/visual/` |
| Tech-Arch.md | `tech-architect` | `architecture/overview.md`、`modules/README.md`（模块清单） |
| Dev-Plan.md | `dev-planner` | `modules/README.md`（开发进度） |
| Product-Spec-CHANGELOG.md | 各 skill 追加 | 不归档，引用原文件 |

## 文档维护规范

- 所有文档使用 Markdown 格式
- 架构图使用 Mermaid 语法（AI 可读可改）
- 引用代码时标注具体路径，如 `src/business/user/handler.ts`
- 新增模块需同步更新 `modules/README.md` 索引
- 权威源文档更新后，及时同步到对应 docs/ 文件
- 生成新项目时，从 `.claude/templates/docs/` 复制模板后按需填充

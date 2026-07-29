# 参考来源

本目录包含产品经理系统在设计过程中参考的外部 Skill 文件副本与权威规范。这些文件**不是运行依赖**，仅供离线查阅设计灵感、方法论来源与编码/目录规范。

外部 Skill 副本来自开源项目（MIT 协议）；harness-doc 为团队内部权威规范。

## 外部 Skill 副本

| 目录 | 来源 | 借鉴内容 | 引用者 |
|------|------|---------|--------|
| `superpowers/` | [obra/superpowers](https://github.com/obra/superpowers) | Sub-agent 驱动开发、系统化调试、代码审查流程 | bug-fixer、code-review、dev-builder、release-builder |
| `andrej-karpathy-skills/` | [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) | 简单优先、精准修改、目标驱动编码准则 | dev-builder |
| `anthropics-skills/` | [anthropics/skills](https://github.com/anthropics/skills) | 前端设计、画布设计方法论文档 | design-maker |
| `code-review-skill/` | 本地收集 | 多技术栈审查规则 | code-review |
| `ui-ux-pro-max-skill/` | [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) | 设计系统、品牌规范 | design-maker |

## harness-doc（团队内部权威规范）

| 目录 | 内容 | 引用者 |
|------|------|--------|
| `harness-doc/coding-requirement/` | 通用编码约束（语言无关，分级必须/应当/建议） | dev-builder、code-review、tech-architect |
| `harness-doc/log-requirement/` | 日志规范约束（标准格式、级别、内容、可观测性） | dev-builder、code-review |
| `harness-doc/path-specification/` | 软件工程目录规范 + 文档模板 | tech-architect |

> harness-doc 作为外部权威引用，开发时指导编码、日志与目录结构，不内联进 skill。

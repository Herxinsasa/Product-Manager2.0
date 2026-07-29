---
name: skill-builder
description: 在 .claude/skills/ 下创建新的 SKILL.md。用于扩展技能体系。
---

# Skill Builder — 创建新 Skill

## 职责

在 `.claude/skills/` 目录下创建新的 SKILL.md 文件，扩展工作流的技能体系。

## 触发场景

- 用户说"创建新 skill"、"加一个技能"、"新建 skill"
- 发现在现有 skill 之外需要新的能力

## 工作流程

### Step 1: 需求澄清
与用户确认新 skill 的信息：
1. **名称与描述**：skill 叫什么？做什么用？
2. **触发场景**：什么时候应该调用这个 skill？
3. **工作流程**：执行步骤是什么？
4. **输入输出**：需要什么输入？产出什么？

### Step 2: 创建目录和文件
1. 在 `.claude/skills/<skill-name>/` 下创建目录
2. 创建 `SKILL.md`，使用以下模板：

```markdown
---
name: <skill-name>
description: <简短的一句话描述>
---

# <Skill 名称> — <中文标题>

## 职责

## 触发场景

## 工作流程

### Step 1
### Step 2
### Step 3

## 输出
```

### Step 3: 验证
确保新 skill 能够被识别和调用。

## 输出
- `.claude/skills/<skill-name>/SKILL.md` — 新创建的 skill 文件

---
name: design-maker
description: 设计执行。基于 docs/design/design-brief.md 产出原型、设计稿或设计系统。支持 Figma、Pencil、HTML/CSS 原型等多种输出方式。
---

# Design Maker — 设计执行

## 职责

基于 `docs/design/design-brief.md` 中的设计规范，产出可视化的设计交付物：原型页面、设计稿、设计系统等。

**此 skill 不负责需求收集。** 设计规范的澄清和确认由 `design-brief-builder` 负责。如果 `docs/design/design-brief.md` 不存在，引导用户先走设计规范流程。

## 前置条件

- `docs/design/design-brief.md` 必须已存在且内容完整
- 如果不存在，告知用户并建议先调用 `design-brief-builder`

## 触发场景

- docs/design/design-brief.md 就绪，用户说"出原型"、"做设计稿"、"生成设计"
- design-brief-builder 完成后用户选择进入设计执行

## 工作流程

### Step 0: 前置检查

1. 检查 `docs/design/design-brief.md` 是否存在
2. 如果不存在：
   > 还没有设计规范文档。需要先通过 design-brief-builder 收集设计需求、确认配色方案和模块 UE。
   > 要现在开始吗？
3. 如果存在，读取 `docs/design/design-brief.md` 和 `docs/requirements/product-spec.md`，了解：
   - 产品对标和品牌调性
   - 配色方案（色值、主题）
   - 组件风格（按钮、圆角、阴影等）
   - 模块 UE 设计（布局、交互、信息流）
   - 产品名称

---

### Step 1: 确定设计交付物类型

与用户确认需要产出什么：

> 基于设计规范，你想产出什么类型的交付物？
> - **A. 完整原型页面** — 所有 MVP 模块的可交互 HTML/CSS 页面
> - **B. 关键页面原型** — 只做最核心的 1-2 个页面
> - **C. 设计系统文件** — 配色 token、组件库、字体规范（可导入代码项目）
> - **D. 单个组件/模块** — 只看某个特定模块的设计效果

---

### Step 2: 选择设计工具

根据交付物类型，提供工具选项给用户选择：

> 你希望通过什么方式产出设计？
>
> **专业设计工具（需要 MCP 连接）：**
> - **A. Figma** — 通过 MCP 直接操作 Figma 文件，适合需要交付给设计师的场景
> - **B. Pencil** — 开源原型工具，通过 MCP 连接
>
> **代码原型（无需额外工具）：**
> - **C. HTML/CSS 前端原型** — 直接在 `<project-name>/` 下生成可交互的原型页面。使用 React/Vue 或纯 HTML，效果最佳
> - **D. Canvas 设计稿** — 生成静态视觉设计稿 PNG/SVG
>
> **自定义：**
> - **E. 其他工具** — 你指定工具或三方库，我来适配

---

### Step 3: 工具就绪检查与引导

根据用户选择的工具执行对应逻辑：

#### A. Figma

1. 检测 Figma MCP 服务是否已在 `.mcp.json` 或 Claude Code 设置中配置
2. 如果未配置：
   > 需要先配置 Figma MCP 连接。步骤如下：
   > 1. 生成 Figma Personal Access Token（前往 Figma → Settings → Account → Personal Access Tokens）
   > 2. 运行：`claude mcp add figma --env FIGMA_TOKEN=<your-token>`
   > 3. 或者在 `.mcp.json` 中添加 Figma 服务配置
   >
   > 配置完成后告诉我，我来继续。
   >
   > 或者你可以现在选其他工具（如 HTML/CSS 原型），不需要额外配置。
3. 如果已配置 → 通过 MCP 创建/更新 Figma 文件，基于设计规范生成：
   - 配色样式（Color Styles）
   - 文字样式（Text Styles）
   - 组件（按钮、输入框、卡片等）
   - 各模块页面 Frame
4. **产出存放**：
   - Figma 文件链接 → 记录到 `docs/design/ui/figma/README.md`（每行一个链接）
   - 导出关键页面 PNG → `docs/design/ui/figma/screenshots/` 作为降级参照
   - 完成后回写 docs/design/design-brief.md「设计交付物」表格

#### B. Pencil

1. 检测 Pencil MCP 服务是否已配置
2. 如果未配置：
   > Pencil 是一个开源原型工具（[https://pencil.evolus.vn/](https://pencil.evolus.vn/)）。
   > 需要配置 MCP 连接后才能使用。配置方法：...
   >
   > 或者选 HTML/CSS 原型方案，无需额外工具。
3. 如果已配置 → 通过 MCP 生成原型文件
4. **产出存放**：
   - `.pen` 源文件 → `docs/design/ui/pencil/`
   - 导出关键页面 PNG → `docs/design/ui/pencil/screenshots/` 作为降级参照
   - 完成后回写 docs/design/design-brief.md「设计交付物」表格

#### C. HTML/CSS 前端原型（默认方案）

不需要任何外部工具。直接参考 Anthropic 官方 frontend-design skill 的方式：

1. 在 `docs/design/ui/prototypes/` 目录下创建原型文件
2. 基于设计规范中的配色方案、组件风格、模块 UE 设计生成页面
3. 包含可交互的演示（页面跳转、状态切换）
4. 使用设计规范中定义的具体色值和参数
5. 完成后回写 docs/design/design-brief.md「设计交付物」表格

可用的设计资源（离线参考）：
- [frontend-design](../../references/anthropics-skills/frontend-design.md) — 前端设计实现参考
- [canvas-design](../../references/anthropics-skills/canvas-design.md) — 视觉设计输出参考
- [ui-ux-pro-max-skill](../../references/ui-ux-pro-max-skill/SKILL.md) — 设计系统、品牌规范参考

#### D. Canvas 设计稿

使用 canvas-design 方式生成静态视觉设计稿。
- 产出 PNG/SVG → `docs/design/ui/` 目录
- 完成后回写 docs/design/design-brief.md「设计交付物」表格
- 适合只需要看效果、不需要交互原型的场景

#### E. 自定义工具

如果用户指定了其他工具或三方库（如 React + Tailwind CSS、Framer、Sketch 等），根据实际情况适配。

---

### Step 4: 执行设计生成

基于选定的工具和设计规范，产出设计交付物。

**生成过程中必须严格依据 docs/design/design-brief.md：**
- 配色使用规范中定义的精确色值
- 组件风格（圆角、阴影、间距）严格匹配
- 模块布局按照 UE 设计中确认的骨架
- 信息流风格（气泡/纯文本流/紧凑列表）按模块执行

**原型应覆盖的交互状态（参考 Design-Brief 各模块的状态处理定义）：**
- 正常态
- 加载态（骨架屏 / 进度条 / 旋转动画）
- 空数据态（引导提示 / 示例数据）
- 错误态（错误提示 + 重试按钮）

---

### Step 5: 展示与迭代

1. 生成完成后，告知用户交付物位置
2. 如果生成的是 HTML/CSS 原型，启动本地预览服务器，提供预览 URL
3. 询问是否需要调整：
   > 原型已生成在 `<路径>`。需要调整什么吗？

---

### Step 5.5: 回写 docs/design/design-brief.md 交付物表（强制）

设计产出确认后，**必须**更新 `docs/design/design-brief.md` 的「设计交付物」章节：

| 模块 | 工具 | 源文件路径 | 降级参照 | 状态 |
|------|------|----------|---------|------|
| <模块名> | <工具名> | docs/design/ui/xxx/xxx.ext | docs/design/ui/xxx/screenshots/xxx.png | 已确认 |

- **源文件路径**：MCP 可读的源文件（.pen / Figma 链接 / .html）。用户手动出图无源文件则填 `—`
- **降级参照**：PNG 截图。所有工具都应导出一份 PNG 作为 MCP 不可用时的兜底
- **状态**：刚产出填「已确认」

### Step 6: 记录变更

追加到 `docs/changelog.md`：
- `v<当前版本>` → **新增** → `生成设计原型：工具 <工具名>，覆盖 <N> 个模块`

## 输出

- `docs/design/design-brief.md` — 更新设计交付物章节，记录原型路径
- `docs/design/ui/prototypes/` — 原型文件（HTML/CSS 方案）
- `docs/design/ui/pencil/` — Pencil 源文件（.pen）及截图
- `docs/design/ui/figma/` — Figma 链接记录及截图
- `docs/changelog.md` — 追加设计变更记录
- Figma/Pencil 文件（专业工具方案）
- `docs/changelog.md` — 追加设计变更记录

## 注意事项

- **严格依据设计规范** — 不要自由发挥配色或布局，所有参数以 docs/design/design-brief.md 为准
- **工具选择不给压力** — Figma/Pencil 需要额外配置，明确告知用户。HTML/CSS 方案开箱即用，作为默认推荐
- **可交互优先** — 对于原型，能点、能切换状态的页面比纯静态图片更有价值
- **状态覆盖** — 做好每个模块的加载态、空态、错误态，这些往往是设计规范中最容易遗漏的地方
- 如果设计规范中的某个参数缺失（如某个色值未定义），基于整体配色方案推理一个合理值并告知用户

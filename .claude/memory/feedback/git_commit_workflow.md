---
name: git commit workflow
description: Before committing and pushing to git, show a brief changelog and wait for user approval
type: feedback
---

**规则：** 所有 git 操作前，必须先展示简要修改说明，等用户确认后再执行 commit + push。不得擅自推送。

**Why:** 用户要求——确保每次提交都是有意为之，避免半成品或错误推送到公开仓库。

**How to apply:** 每次修改完成后，输出 3-5 行改动摘要，明确问"确认提交吗？"，用户回复确认后再执行 git commit + push。

# Neko Zone

这个仓库现在的主目标，不是做一个泛泛的 Agent 市场站，而是落一套可施工的 `Agent Community` 主仓设计。

大白话说，这里要做的是一套让多个 agent 在同一个社区里协作、被人类观察、被人类干预、并通过 skill 接入的系统。它包含三块核心能力：

1. 社区服务端：保存消息、管理群组、推进工作流、投影到前端。
2. 人类观察与操作前端：你可以登录后台，观察群组、看工作流、必要时干预。
3. agent 侧接入 skill：让外部 agent 以统一方式加入社区、接消息、回消息。

## 文档入口

- [正式设计文档](./docs/community-system-design.md)
  - 当前最重要的施工文档。身份、权限、协议挂载、消息成立条件、模型配置、失败态都写在这里。
- [系统蓝图（产品视角）](./docs/agents-community-blueprint.md)
  - 解释这套系统从用户视角长什么样，前端和后台该围绕什么闭环来做。
- [MVP 路线图](./docs/mvp-roadmap.md)
  - 从合同、后端、前端到 skill 联调的落地顺序。
- [仓库结构建议](./docs/repo-structure.md)
  - 主仓内部模块怎么拆，避免把协议、服务端、前端和投影逻辑搅在一起。
- [移机接力包](./docs/handoff/THREAD_MIGRATION_PACK_20260416.md)
  - 给另一台电脑上的 Codex 直接接手这个任务用的便携上下文。
- [下一线程 Prompt](./docs/handoff/NEXT_CODEX_PROMPT_20260416.md)
  - 可直接复制给另一台电脑上的 Codex。

## 先看什么

如果你后面让我直接写代码，默认按这个顺序看：

1. `docs/community-system-design.md`
2. `docs/mvp-roadmap.md`
3. `docs/repo-structure.md`
4. `docs/handoff/THREAD_MIGRATION_PACK_20260416.md`

## 边界

这套系统里不包含施工工具链本身。

也就是说：

- `本地 Codex -> HTTP 控制器 -> 远端 Codex`

这是开发和联调用的施工工具，不是社区正式运行链路的一部分。

社区正式链路是：

- `community server -> group protocol -> runtime -> agent protocol -> agent 自身行为 -> skill 封装发送`

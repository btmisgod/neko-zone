# 下一台电脑上的 Codex 接力 Prompt

把下面整段直接发给新的 Codex：

```text
这是一个 implementation 线程，不是 discussion 线程。请直接按可施工方式接手，不要重新泛聊产品梦想。

先在当前仓库内阅读这些文件，按顺序：
1. README.md
2. docs/community-system-design.md
3. docs/handoff/THREAD_MIGRATION_PACK_20260416.md
4. docs/handoff/SKILL_AND_TOOLCHAIN_NOTES_20260416.md
5. docs/handoff/LIVE_TEST_PROVIDER_NOTES_20260416.md
6. docs/mvp-roadmap.md
7. docs/repo-structure.md

注意这些边界：
- 当前主线是 Agent Community 协作系统，不是泛 Agent 内容社区。
- 正式链路是：community server -> group protocol -> runtime -> agent protocol -> agent self behavior -> skill。
- community-skill 属于社区系统。
- 本地 Codex -> HTTP 控制器 -> 远端 Codex 属于施工工具链，不属于正式架构。
- canonical effect 当前定义为 message persisted。
- 只有 manager 的正式 signal 才能推进 gate。
- 人类角色已经拆成 platform_admin 和 group_operator。
- agent 身份必须区分 agent_id、agent_secret、access_token。
- 运行时模型配置必须收敛到单一真相源，不允许继续多路 fallback 猜配置。

当前仓库已经完成的是文档基线，不是代码基线。你的直接目标不是再写设计，而是开始搭第一版代码骨架。

建议你直接从这几个方向开工：
1. 建 packages/contracts，先定义消息、signal、presence、protocol card 的 schema 和类型。
2. 建 apps/server，先铺 auth、onboarding、groups、group_sessions、role_bindings、messages 的最小骨架。
3. 如果时间够，再给 apps/web 铺登录页、群列表、会话总览和时间线骨架。

严格不要做这些事：
- 不要把施工工具链写进正式系统模型。
- 不要把内容社区/市场站逻辑混进协作总线主线。
- 不要把真实 key、token、secret 写进仓库。
- 不要重新把系统扩大成全量重写。

和用户沟通时保持中文、直接、客观、简洁。术语要配大白话解释，不要拍马屁。
```

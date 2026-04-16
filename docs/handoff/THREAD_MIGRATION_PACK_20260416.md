# 线程移机接力包 2026-04-16

## 1. 目的

这份文档是给另一台电脑上的 Codex 直接接手当前任务用的便携上下文。

它的目标不是复述所有历史，而是把当前已经确定的东西、已经踩过的坑、以及下一步该干什么，一次讲清。

## 2. 当前线程性质

- 当前线程身份：`implementation`
- 当前语言要求：中文
- 当前沟通风格：直接、客观、简洁，不拍马屁；术语要配大白话解释

## 3. 当前仓库与相关仓库

### 主仓

- GitHub: `https://github.com/btmisgod/neko-zone`
- 本仓当前职责：`community server + human console` 的主设计与后续主代码仓

### skill 仓库

- OpenClaw skill 仓：`https://github.com/btmisgod/neko-skill-for-openclaw`
- Hermes skill 仓：`https://github.com/btmisgod/neko-skill-for-Hermes`

### 施工工具链仓

- 本地已知工具链目录：`G:\community agnts\Agents-controller-HTTP`
- 边界结论：
  - `本地 Codex -> HTTP 控制器 -> 远端 Codex` 是施工工具链
  - 它不是社区正式运行架构的一部分

## 4. 这条线现在到底在做什么

不是在做一个泛泛的 Agent 内容社区。

当前主线是：

- 搭一套多 agent 协作的社区系统
- 人类可以登录后台观察和干预
- agent 通过 skill 接入
- manager 推进工作流
- worker 汇报结果
- server 保存事实并投影到 UI

## 5. 当前已经写进主仓的正式文档

优先阅读顺序：

1. `docs/community-system-design.md`
2. `docs/mvp-roadmap.md`
3. `docs/repo-structure.md`
4. `docs/agents-community-blueprint.md`

这些文档已经替换掉旧的“泛市场/泛内容站”方向，收束到了社区协作系统主线。

## 6. 已经定下来的核心架构结论

### 6.1 正式分层

正式链路固定为：

`community server -> group protocol -> runtime -> agent protocol -> agent self behavior -> skill`

### 6.2 skill 的定位

skill 是接入层、封装层，不是社区事实裁判。

### 6.3 人类角色

人类不只叫观察者，正式拆成：

- `platform_admin`
- `group_operator`

人类在群里发消息，默认是上下文消息，不自动推进工作流。

### 6.4 agent 身份

必须分清：

- `agent_id`: 稳定身份
- `agent_secret`: 长期接入凭证
- `access_token`: 短期访问令牌

不能把 token 当身份本身。

### 6.5 canonical effect

当前成立标准写死为：

- `message persisted`

消息落库算成立，不要求立刻可见，也不要求立刻推进 gate。

### 6.6 kickoff / step0

它是控制层消息流，不是社区中自然发生的事实。

### 6.7 角色与 gate

- 只有 manager 的正式 signal 才能推进 gate
- worker 只能更新自己的状态、提交结果、供 UI 观测
- worker 不能推进总 gate，也不能改别人的状态

### 6.8 角色绑定

必须同时有：

- `GroupProtocol`
- `GroupSession.role_bindings`

原因：

- 协议定义有哪些角色
- 会话定义这次到底谁是 manager / worker

### 6.9 协议卡缓存

规则已经定为：

- agent 自己拉取协议缓存
- runtime 决定当前会话挂哪些协议卡
- agent 发消息要带 `protocol_version`
- server 发现旧版本时返回 `protocol_stale`
- agent 收到后重拉协议卡

### 6.10 并发推进

manager 推进 gate 时要带：

- `idempotency_key`
- `expected_gate_version`

### 6.11 安全最小方案

- 公网请求必须走 `HTTPS`
- agent 写 server 用 `access_token`
- server 调 webhook 用 `webhook_secret` 做签名
- webhook 要带 `timestamp + event_id + signature`
- skill 本地做时间窗口校验和去重

### 6.12 模型配置合同

这是当前最重要的工程优化结论：

- 安装阶段可以从多来源读取
- 运行阶段只能读一个解析后的最终配置
- 推荐落点：`resolved-model-config.json`
- 运行时不再多路 fallback 猜配置
- 缺失配置时，presence 进入 `blocked`，并发错误摘要

### 6.13 失败态

最少拆成：

- `degraded`
- `retrying`
- `blocked`

不要把所有失败都叫一个 `error`。

## 7. 关于 community-skill 的关键理解

当前线程已经读过并确认过的理解：

- `community-skill` 是社区接入 skill，不是施工工具 skill
- 它负责：
  - onboarding
  - webhook 接收
  - runtime 最低义务判断
  - 调模型接口
  - 结构化消息回发
- 它当前调用模型的方式是 OpenAI 兼容接口：
  - `POST /chat/completions`
- 它最少依赖三项模型配置：
  - `MODEL_BASE_URL`
  - `MODEL_API_KEY`
  - `MODEL_ID`

## 8. 关于施工工具链的关键边界

这条边界非常重要，不能再混：

- `community-skill` 属于社区系统
- `Agents-controller-HTTP + 远端 Codex` 属于施工工具链

两者关系是：

- 工具使用系统
- 不是系统依赖工具

## 9. 关于 live 测试免费模型的结论

当前线程已经做过学习和实测，结论如下：

### 可用接口风格

两个备选测试 API 都是 OpenAI 兼容风格：

- `POST /chat/completions`

### 当前建议顺序

1. `OpenRouter`
   - 用固定 free 模型
   - 不要用 `openrouter/free` 这种路由别名
2. `SiliconFlow`
   - 当第二备选
   - 要接受免费额度限流

### 为什么不能偷懒用路由别名

因为它可能把请求路由到安全审查模型，而不是正常生成模型。

大白话说：

- 你以为在测聊天
- 实际测成了“安检门”

## 10. 当前仓库里已经做了什么

### 已落地文档

- `README.md`
- `docs/community-system-design.md`
- `docs/agents-community-blueprint.md`
- `docs/mvp-roadmap.md`
- `docs/repo-structure.md`

### 本次移机新增接力文档

- `docs/handoff/THREAD_MIGRATION_PACK_20260416.md`
- `docs/handoff/SKILL_AND_TOOLCHAIN_NOTES_20260416.md`
- `docs/handoff/LIVE_TEST_PROVIDER_NOTES_20260416.md`
- `docs/handoff/NEXT_CODEX_PROMPT_20260416.md`

## 11. 没有被打包进 GitHub 的东西

下面这些东西故意不进 GitHub：

- 真实 `agent token`
- 真实 `webhook secret`
- 真实模型 API key
- 本机 SSH 私钥
- 本机 `.ssh/config`
- 带 live key 的临时测试 JSON

原因不是形式主义，而是这些东西一旦进仓库，后面会直接变安全事故。

## 12. 另一台电脑上的 Codex 该怎么接

最少步骤：

1. clone `neko-zone`
2. 先读 `README.md`
3. 读 `docs/community-system-design.md`
4. 读本接力包
5. 再读 `docs/mvp-roadmap.md` 和 `docs/repo-structure.md`
6. 直接从 `apps/server` 和 `packages/contracts` 开始搭第一版骨架

## 13. 下一步最合理的施工顺序

建议下一线程不要继续空谈设计，直接开始代码骨架：

1. 建 `packages/contracts`
   - 消息 schema
   - signal schema
   - presence schema
   - protocol card schema
2. 建 `apps/server`
   - auth 基础
   - onboarding 基础
   - groups / sessions / role bindings 基础
   - messages 落库基础
3. 再建 `apps/web`
   - 登录页
   - 群列表
   - 会话总览
   - 时间线和状态面板占位

## 14. 当前阶段不要偏航到哪里

不要在下一线程里默认跳去做这些：

- 整个系统重写
- 多身份 / 子 agent 体系
- 把 HTTP 控制器写进正式架构
- 把真实密钥塞进仓库
- 把产品市场化页面先做一大堆

## 15. 最后一条提醒

如果下一线程又把“内容社区”和“协作总线”混回一块，那就是倒退，不是推进。

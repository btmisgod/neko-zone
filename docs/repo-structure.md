# 仓库结构建议

## 1. 原则

这个主仓是 `community server + human console` 的主工程。

所以目录结构不能再按“泛网站 + 泛 runtime”去拆。

要围绕这 4 类东西来拆：

1. 服务端合同与规则
2. 前端观测与操作台
3. 共享协议与类型
4. 基础设施

## 2. 建议目录

```text
neko-zone/
├─ apps/
│  ├─ server/
│  └─ web/
├─ packages/
│  ├─ contracts/
│  ├─ workflow/
│  ├─ auth/
│  └─ ui/
├─ infra/
│  ├─ docker/
│  ├─ db/
│  └─ deploy/
└─ docs/
```

## 3. 每个目录负责什么

### `apps/server`

主社区服务端。

它负责：

- 人类账号与权限
- agent onboarding
- token 与 webhook 验签
- 群组、会话、角色绑定
- 消息落库
- gate 推进
- projection 给前端

### `apps/web`

人类观察与操作前端。

它负责：

- 登录
- 群列表
- 会话总览
- 消息时间线
- 阶段面板
- agent 状态
- 错误摘要和运维观察面板

### `packages/contracts`

放共享合同。

比如：

- 消息 schema
- signal schema
- presence schema
- protocol card schema
- API 请求和响应类型

### `packages/workflow`

放工作流规则和纯逻辑。

比如：

- gate 判定
- manager-only 推进规则
- role binding 校验
- protocol stale 判定
- canonical effect 辅助逻辑

### `packages/auth`

放鉴权和签名相关代码。

比如：

- access token
- webhook signature
- timestamp 窗口校验
- event_id 去重辅助

### `packages/ui`

放共享前端组件。

比如：

- 时间线组件
- 阶段卡片
- 状态标签
- 运维摘要卡片

## 4. 为什么这样拆

最怕的不是目录不漂亮，而是职责搅一起。

后面最容易乱掉的是 4 类逻辑：

1. 页面展示逻辑
2. 服务端规则逻辑
3. 协议结构定义
4. 鉴权与安全逻辑

如果这四类东西混在一个目录里，后面改一个点就会牵一片。

## 5. 当前不建议做的工程过度设计

这阶段先不要：

- 一上来拆很多微服务
- 一上来拆很多仓库
- 一上来做很重的消息中间件架构
- 一上来把 skill 代码并入主仓

原因很简单：

- 现在最重要的是把合同和最小闭环做对
- 不是把工程外形做得很大

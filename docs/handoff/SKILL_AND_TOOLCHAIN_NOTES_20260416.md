# Skill 与工具链边界笔记 2026-04-16

## 1. community-skill 是什么

它是社区接入层，不是施工控制器。

它负责：

- onboarding
- 接收社区 webhook
- 运行 runtime 的最低义务判断
- 调模型接口
- 把结果封装成结构化社区消息并回发

## 2. community-skill 不是什么

它不是：

- 远端 Codex 控制器
- HTTP 施工调度器
- 社区正式规则的唯一裁判

## 3. 模型调用方式

当前理解固定为：

- `community-skill` 走 OpenAI 兼容接口
- 最少读三项模型配置：
  - `MODEL_BASE_URL`
  - `MODEL_API_KEY`
  - `MODEL_ID`
- 然后请求：
  - `POST ${MODEL_BASE_URL}/chat/completions`

## 4. runtime 的职责

runtime 不直接替 agent“思考”。

它主要负责：

- 判断这条输入是必须处理、可选处理，还是只观察
- 决定当前会话挂载哪些协议卡

## 5. community-skill 与施工工具链的关系

关系是：

- 社区系统可以独立存在
- 施工工具链只是开发和联调时的外部工具

正式边界：

- `community-skill` 属于社区架构
- `Agents-controller-HTTP + 远端 Codex` 不属于社区正式架构

## 6. 为什么这条边界要守住

因为一旦把工具链写进正式系统：

- 架构图会假
- 后端会依赖错误对象
- 前端会看到不该出现的系统概念
- 将来真正部署时会反复返工

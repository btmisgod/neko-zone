# Live 测试模型提供商笔记 2026-04-16

## 1. 当前结论

未来做真实 agent 的 live 测试时，可以使用免费模型作为测试 profile。

但它们只能是测试方案，不能成为正式生产依赖。

## 2. 接口格式

两个备选方案都按 OpenAI 兼容方式处理：

- `POST /chat/completions`

因此最少配置项固定为：

- `MODEL_BASE_URL`
- `MODEL_API_KEY`
- `MODEL_ID`

## 3. 推荐顺序

### 第一备选：OpenRouter

推荐：

- 用固定 free 模型

不推荐：

- `openrouter/free`

原因：

- 这是路由别名
- 不稳定
- 可能把请求导向安全审查模型

建议写法示例：

```env
MODEL_BASE_URL=https://openrouter.ai/api/v1
MODEL_API_KEY=<replace-me>
MODEL_ID=arcee-ai/trinity-large-preview:free
```

### 第二备选：SiliconFlow

建议写法示例：

```env
MODEL_BASE_URL=https://api.siliconflow.cn/v1
MODEL_API_KEY=<replace-me>
MODEL_ID=Qwen/Qwen3-8B
```

注意：

- 免费额度可能会限流
- 被限流不代表接法错了

## 4. 配置建议

不要把 live key 直接写进主配置文件。

更稳的做法是：

1. 主环境只放社区接入配置
2. 单独放模型 profile 文件
3. 用 `COMMUNITY_MODEL_CONFIG_FILES` 指向当前测试 profile

## 5. 为什么要这样拆

大白话说：

- 社区接入配置是“接哪儿”
- 模型 profile 是“拿谁说话”

这两件事混在一个文件里，后面切模型、换环境、做联调时很容易乱。

## 6. 不要做的事

不要把下面这些东西推到 GitHub：

- 真实 API key
- 带 live key 的 JSON 测试文件
- 带 token 的运行时快照

如果要跨电脑迁移：

- 只传脱敏模板
- 真实 key 在新机器本地重新填写

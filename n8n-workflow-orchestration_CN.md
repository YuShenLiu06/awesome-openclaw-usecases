# OpenClaw + n8n 工作流程编排

让你的 AI 智能体直接管理 API 密钥并调用外部服务是一个安全事件隐患。每次新集成都意味着 `.env.local` 中的另一个凭据、智能体意外泄漏或误用的另一个表面。

这个使用案例描述了一种模式，其中 OpenClaw 通过 webhook 将所有外部 API 交互委托给 n8n 工作流程 —— 智能体从不接触凭据，每个集成都可视且可锁定。

## 痛点

当 OpenClaw 直接处理所有内容时，你会遇到三个复合问题：
- **没有可见性**：很难检查当智能体被埋在 JavaScript 技能文件或 shell 脚本中时它实际构建了什么
- **凭据蔓延**：每个 API 密钥都生活在智能体的环境中，一个糟糕的提交就会暴露
- **浪费的令牌**：确定性子任务（发送电子邮件、更新电子表格）在可以作为简单工作流运行时消耗 LLM 推理令牌

## 功能说明

- **代理模式**：OpenClaw 带有传入 webhook 的 n8n 工作流程，然后调用那些 webhook 进行所有未来的 API 交互
- **凭据隔离**：API 密钥生活在 n8n 的凭据存储中 —— 智能体只知道 webhook URL
- **可视调试**：每个工作流程在 n8n 的拖放 UI 中可检查
- **可锁定工作流**：一旦构建和测试了工作流程，你可以锁定它，这样智能体无法修改它如何与 API 交互
- **保护步骤**：你可以在任何外部调用在 n8n 中执行之前添加验证、速率限制和批准门

## 它是如何工作的

1. **智能体设计工作流**：告诉 OpenClaw 你需要什么（例如，"创建一个工作流，当新的 GitHub 问题标记为 `urgent` 时发送 Slack 消息"）
2. **智能体在 n8n 中构建它**：OpenClaw 通过 n8n 的 API 创建工作流，包括传入 webhook 触发器
3. **你添加凭据**：打开 n8n 的 UI，手动添加你的 Slack 令牌 / GitHub 令牌
4. **你锁定工作流**：防止智能体进一步修改
5. **智能体调用 webhook**：从现在开始，OpenClaw 调用 `http://n8n:5678/webhook/my-workflow` 并带有 JSON 有效负载 —— 它从不看到 API 密钥

```text
┌──────────────┐     webhook 调用      ┌─────────────┐     API 调用     ┌──────────────┐
│   OpenClaw   │ ───────────────────→  │   n8n 工作流程   │ ─────────────→  │   外部      │
│   （智能体）   │                       │   （无凭据）    │  │  （凭据      │ 服务     │
│              │                       │   API 密钥）      │  │ （留在这里）    │ （Slack 等）│
└──────────────┘                       └─────────────────┘                  └──────────────┘
```

## 所需技能

- `n8n` API 访问（用于创建/触发工作流）
- `fetch` 或 `curl` 用于 webhook 调用
- Docker（如果使用预配置的堆栈）
- n8n 凭据管理（手动，每次集成都一次设置）

## 如何设置

### 选项 1：预配置的 Docker 堆栈

一个社区维护的 Docker Compose 设置（[openclaw-n8n-stack](https://github.com/caprihan/openclaw-n8n-stack)）在共享 Docker 网络上预配置了所有内容：

```bash
git clone https://github.com/caprihan/openclaw-n8n-stack.git
cd openclaw-n8n-stack
cp .env.template .env
# 将你的 Anthropic API 密钥添加到 .env
docker-compose up -d
```

这给了你：
- 端口 3456 上的 OpenClaw
- 端口 5678 上的 n8n
- 共享 Docker 网络，以便 OpenClaw 可以直接调用 `http://n8n:5678/webhook/...`
- 预构建的工作流模板（多 LLM 事实核查、电子邮件分类、社交媒体监控）

### 选项 2：手动设置

1. 安装 n8n（`npm install n8n -g` 或通过 Docker 运行）
2. 配置 OpenClaw 以知道 n8n 基础 URL
3. 将此添加到你的 AGENTS.md：

```text
## n8n 集成模式

当我需要与外部 API 交互时：

1. 绝不在我的环境或技能文件中存储 API 密钥
2. 检查此集成的 n8n 工作流程是否已存在
3. 如果没有，使用 n8n API 创建一个带有 webhook 触发器的工作流
4. 通知用户添加凭据并锁定工作流
5. 对于所有未来调用，使用带有 JSON 有效负载的 webhook URL

工作流命名：openclaw-{service}-{action}
示例：openclaw-slack-send-message

webhook 调用格式：
curl -X POST http://n8n:5678/webhook/{workflow-name} \
    -H "Content-Type: application/json" \
    -d '{"channel": "#general", "message": "来自 OpenClaw 的你好"}'
```

## 核心见解

- **一次实现三个胜利**：通过 n8n 可视 UI 实现可观察性、通过凭据隔离实现安全性、通过作为工作流而不是 LLM 调用实现性能。
- **测试后锁定**："构建 → 测试 → 锁定"循环至关重要 —— 如果不锁定，智能体会静默修改工作流。
- **n8n 有 400+ 个集成**：你想连接的大多数外部服务已经有 n8n 节点，将智能体从编写自定义 API 调用中解放出来。
- **免费的审计踪迹**：n8n 使用输入/输出数据记录每个工作流执行。

## 灵感来源

这种模式由 [Simon Høiberg](https://x.com/SimonHoiberg/status/2020843874382487959) 描述，他概述了这种方法比让 OpenClaw 直接处理 API 交互好的三个原因：通过 n8n 可视 UI 实现可观察性、通过凭据隔离实现安全性、通过作为工作流而不是 LLM 调用运行确定性子任务。[openclaw-n8n-stack](https://github.com/caprihan/openclaw-n8n-stack) 仓库提供了实现此模式的准备好运行的 Docker Compose 设置。

## 相关链接

- [n8n 文档](https://docs.n8n.io/)
- [openclaw-n8n-stack (Docker 设置)](https://github.com/caprihan/openclaw-n8n-stack)
- [n8n Webhook 触发器文档](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)

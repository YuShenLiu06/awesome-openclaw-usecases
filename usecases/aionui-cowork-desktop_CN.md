# 在桌面 Cowork (AionUi) 中使用 OpenClaw —— 远程救援与多智能体中心

从桌面 Cowork UI 使用 OpenClaw，并在你不在计算机时通过 Telegram 或 WebUI 访问它，当它无法连接时远程修复它。AionUi 是一个免费、开源的应用，在统一的工作区中运行 **OpenClaw 作为一流智能体**，同时还有 12+ 个其他智能体（Claude Code、Codex、Qwen Code 等），并内置了 **OpenClaw 部署专家**用于安装、诊断和修复 —— 包括**远程救援**，当 OpenClaw 无法连接而你不在机器时。

## 为什么 OpenClaw + AionUi

| 如果你想要…… | AionUi 提供给你…… |
|---------------|--------------------|
| **在真实的桌面 UI 中使用 OpenClaw** | Cowork 工作区，你可以看到 OpenClaw（和其他智能体）读/写文件、运行命令、浏览网页 —— 而不只是终端/聊天。 |
| **当 OpenClaw 无法连接且你不在计算机时修复它** | 通过 **Telegram 或 WebUI** 从任何地方打开 AionUi → 使用内置的 **OpenClaw 部署专家**来运行 `openclaw doctor`、修复配置、重启网关。许多用户依赖于此。一个常见模式：在另一台机器或从手机上使用 WebUI 时，当 OpenClaw 无法连接时通过 Telegram 或 WebUI 修复它。 |
| **一个用于 OpenClaw + 其他智能体的地方** | 在一个应用中的 OpenClaw、内置智能体（Gemini/OpenAI/Anthropic/Ollama）、Claude Code、Codex 和 12+ 个其他智能体 —— 一个界面、并行会话，所有智能体相同的 MCP 配置。 |
| **远程访问你的 OpenClaw** | 使用 WebUI、Telegram、Lark 或 DingTalk 到达你的 AionUi 实例（从而到达 OpenClaw）—— 从手机或其他设备与智能体对话。 |
| **可选自动化** | AionUi cron 可以按计划运行 OpenClaw（或其他智能体）以进行 24/7 任务。 |

## 痛点

你已经在通过 CLI 或 Telegram 使用 OpenClaw 了，但是：
- 你想要**查看**智能体在做什么（文件、终端、网页），而不是从日志中推断。
- 当 **OpenClaw 无法连接**而你不在计算机时，你没有办法运行 `openclaw doctor` 或修复配置 —— 你需要访问到可以修复 OpenClaw 的东西。
- 你使用多个 CLI 智能体（OpenClaw、Claude Code、Codex……）并且不想管理多个应用程序或为每个重新配置 MCP。

## 它的作用

- **OpenClaw 作为 Cowork 智能体**：安装 AionUi 并在 AionUi 中安装 OpenClaw。AionUi 自动检测 OpenClaw 并在其本地运行它作为一流智能体。你获得可见的文件操作、命令历史和基于文件的会话。
- **远程 OpenClaw 救援**：当 OpenClaw 无法连接（或出现错误）时，你可以打开 AionUi（通过 Telegram 或 WebUI）并使用内置的 **OpenClaw 部署专家**来：
  - 诊断问题（`openclaw doctor`）
  - 修复配置错误
  - 重启 OpenClaw 网关/服务
  - 指导你完成恢复
- **一个应用中的多智能体**：OpenClaw 是内置智能体。AionUi 同时运行 12+ 个其他智能体（Claude Code、Codex、Qwen Code、DeepSeek 等）。你可以切换或并行运行它们，而它们都共享相同的 MCP 配置和会话历史。
- **远程访问**：使用 WebUI、Telegram、Lark 或 DingTalk 从手机或其他设备与你的 OpenClaw（通过 AionUi 实例）对话。

## 你需要

- **OpenClaw**（例如，`npm install -g openclaw@latest`）
- **AionUi**：[AionUi 版本](https://github.com/iOfficeAI/AionUi/releases)（macOS / Windows / Linux）。
- 你的 AI 模型的 API 密钥或认证（OpenClaw 配置 + 任何内置智能体在 AionUi 中的密钥）。

## 如何设置

### 1. 安装 AionUi（如果需要）

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon   # 可选：24/7 守护进程
```

### 2. 打开 AionUi

AionUi 将自动检测 OpenClaw。如果没有：
1. 使用 AionUi 内置的 **OpenClaw 设置助手**来引导你完成安装。
2. 或者手动在 AionUi 中安装 OpenClaw（添加新的连接 → 选择"OpenClaw"类型）。

### 3. 验证

1. 在 AionUi 中，你应该看到 OpenClaw 作为一流智能体运行。
2. 创建一个 Cowork 会话并选择 OpenClaw 作为智能体。
3. 测试可见性：在 OpenClaw 中创建一个文件 → 应该在 AionUi 中可见。

### 4. 配置远程救援（可选但推荐）

在 AionUi 设置中：
- 添加你的 **Telegram** 和/或 **WebUI** 访问信息。
- 确保 AionUi 的 cron/自动化已启用（用于 24/7 监控和远程修复）。

### 5. 多智能体工作流（可选）

1. 在 AionUi 中，添加你其他 AI 模型的 API 密钥（Gemini、Qwen、DeepSeek 等）。
2. AionUi 将自动配置它们。
3. 你可以切换智能体或并行运行它们。

## 关键场景

### 场景 1：远程救援（OpenClaw 故障）

**问题**：你在手机上，OpenClaw 在家中电脑上无法连接。

**解决方案**：
1. 通过手机上的 WebUI 或 Telegram 打开 AionUi。
2. 使用内置的"OpenClaw 部署专家"运行 `openclaw doctor`。
3. 遵循诊断和修复步骤。
4. 重启 OpenClaw。

### 场景 2：多智能体并行工作

**问题**：你想让 OpenClaw 研究主题 X，同时让 Claude Code 编写代码。

**解决方案**：
1. 在 AionUi 中，同时向 OpenClaw 和 Claude Code 发送不同的消息。
2. 两个智能体共享相同的 MCP 配置和会话上下文。
3. 结果更快、更协调。

### 场景 3：从任何地方访问

**问题**：你正在旅行，需要在手机上访问你的 OpenClaw。

**解决方案**：
1. 使用 AionUi WebUI 从手机浏览器访问 AionUi 实例。
2. 与 OpenClaw（以及任何其他智能体）对话。

### 场景 4：24/7 监控和自动化

**问题**：你需要智能体每小时检查系统并在关键故障时通知你。

**解决方案**：
1. 在 AionUi 中为 OpenClaw（或其他智能体）设置 cron 作业。
2. 使用 Telegram 或 Lark 进行通知。

## 关键见解

- **一个界面管理所有内容**：不再需要在终端、Web UI 和专用应用之间切换。
- **远程救援是救命稻草**：当 OpenClaw 故障而你在其他设备上时，AionUi 允许你运行诊断和修复而无需物理访问。
- **智能体共享**：所有智能体（内置和外部）都共享相同的 MCP 和会话上下文 —— 无需重复配置。
- **AionUi 自动化**：安装、检测和并行运行多个智能体都自动完成。
- **文件可见性**：在 AionUi 中，OpenClaw（以及任何其他智能体）的文件操作立即可见 —— 你确切知道创建、删除或修改了什么。

## 相关链接

- [AionUi GitHub](https://github.com/iOfficeAI/AionUi)
- [AionUi 网站](https://www.aionui.com)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw 文档](https://docs.openclaw.ai)

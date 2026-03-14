# 构建前想法验证器

在 OpenClaw 开始构建任何新东西之前，它会自动检查想法是否已经存在于 GitHub、Hacker News、npm、PyPI 和 Product Hunt 上 —— 并根据发现的内容调整其方法。

## 功能说明

- 在编写任何代码之前扫描 5 个真实数据源（GitHub、Hacker News、npm、PyPI、Product Hunt）
- 返回一个 `reality_signal` 分数（0-100），表明空间有多拥挤
- 显示带有星星计数和描述的顶级竞争对手
- 当空间饱和时建议转向方向
- 作为构建前门工作：高信号 = 停止并讨论，低信号 = 继续

## 痛点

你告诉你的智能体"为我构建一个 AI 代码审查工具"，它很高兴地花了 6 小时编码。与此同时，GitHub 上已经存在 143,000+ 个仓库 —— 顶级的那个有 53,000 颗星星。智能体从不检查，因为你从未要求，而且它不知道要查看。只有在你投入大量时间后你才发现竞争对手。每个新项目想法都会重复这种模式。

## 所需技能

- [idea-reality-mcp](https://github.com/mnemox-ai/idea-reality-mcp) —— 扫描真实数据源并返回竞争分数的 MCP 服务器

## 如何设置

1. 安装 idea-reality-mcp：

```bash
uvx idea-reality-mcp
```

2. 将 MCP 服务器添加到你的 OpenClaw 配置：

```json
{
  "mcpServers": {
    "idea-reality": {
      "command": "uvx",
      "args": ["idea-reality-mcp"]
    }
  }
}
```

3. 将此添加到你的 OpenClaw 智能体指令：

```text
在开始任何新项目、功能或工具之前，总是先运行 idea_check。

规则：
- 如果 reality_signal > 70：停止。报告带有星星计数的前 3 个竞争对手。
  询问我想继续、转向还是放弃。
- 如果 reality_signal 30-70：向我显示结果和 pivot_hints。
  建议现有项目未涵盖的小众角度。
- 如果 reality_signal < 30：继续构建。
  提到空间是开放的。
- 在编写任何代码之前，总是显示 reality_signal 分数和顶级竞争对手。
```

## 真实世界示例

**你：**"为我构建一个用于 AI 代码审查的 CLI 工具"

OpenClaw 运行 `idea_check("AI code review tool")` 并响应：

> **reality_signal: 90/100 (非常高)**
>
> 顶级竞争对手：
> 1. Gitea — 53,940 颗星星
> 2. reviewdog — 9,104 颗星星
> 3. Danger (Ruby) — 5,649 颗星星
>
> 这个空间有 143,000+ 个相关仓库。顶级玩家成熟且资金充足。
>
> **转向建议：**
> - 专注于特定语言（仅 Rust/Go 的 AI 代码审查）
> - 针对特定框架（React/Vue 组件审查）
> - 针对特定行业（金融/医疗代码合规审查）
>
> 我应该继续差异化角度，还是转向其他东西？

**你：**"用于构建前想法验证的 MCP 服务器怎么样？"

> **reality_signal: 8/100 (非常低)**
>
> 在 PyPI 或 npm 上找到 0 个直接竞争对手。GitHub 上有一些切线相关的仓库，但没有一个解决这个确切问题。
>
> 空间完全开放。继续构建。

## 变体

- **用于严肃决策的深度模式**：使用 `depth="deep"` 并行扫描所有 5 个来源（GitHub + HN + npm + PyPI + Product Hunt）用于主要项目决策。
- **批量验证**：在黑客马拉松之前，给 OpenClaw 一个包含 10 个想法的列表，并让它按 `reality_signal` 对它们进行排名 —— 最低分数 = 最原创的机会。
- **先尝试 Web 演示**：无需安装即可在 [mnemox.ai/check](https://mnemox.ai/check) 上尝试，看看工作流程是否适合你的需求。

## 核心见解

- 这防止了构建中最昂贵的错误：**解决已经解决的问题**。
- `reality_signal` 基于真实数据（仓库计数、星星分布、HN 讨论量），而不是 LLM 猜测。
- 高分并不意味着"不要构建" —— 它意味着"差异化或别费心。"
- 低分意味着真正的空白空间存在。那是个人构建者有最佳机会的地方。

## 相关链接

- [idea-reality-mcp GitHub](https://github.com/mnemox-ai/idea-reality-mcp)
- [Web 演示](https://mnemox.ai/check)（无需安装即可尝试）
- [PyPI](https://pypi.org/project/idea-reality-mcp/)

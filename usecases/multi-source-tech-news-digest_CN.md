# 多源科技新闻摘要

自动聚合、评分和传递来自 109+ 来源的科技新闻，涵盖 RSS、Twitter/X、GitHub 发布和网页搜索 —— 全部通过自然语言管理。

## 痛点

在 AI、开源和前沿科技领域保持更新需要每天检查数十个 RSS 信息源、Twitter 账户、GitHub 仓库和新闻网站。手动策划既耗时，而且大多数现有工具要么缺乏质量过滤，要么需要复杂的配置。

## 功能说明

一个按计划运行的四层数据流水线：

1. **RSS 信息源**（46 个来源）—— OpenAI、Hacker News、MIT Tech Review 等。
2. **Twitter/X KOL**（44 个账户）—— @karpathy、@sama、@VitalikButerin 等。
3. **GitHub 发布**（19 个仓库）—— vLLM、LangChain、Ollama、Dify 等。
4. **网页搜索**（4 个主题搜索）—— 通过 Brave Search API

所有文章都会合并，通过标题相似性去重，并进行质量评分（优先来源 +3、多来源 +5、时效性 +2、互动 +1）。最终摘要发送到 Discord、电子邮件或 Telegram。

该框架完全可定制 —— 在 30 秒内添加您自己的 RSS 信息源、Twitter 句柄、GitHub 仓库或搜索查询。

## 提示词

**安装并设置每日摘要：**
```text
从 ClawHub 安装 tech-news-digest。设置上午 9 点发送每日科技摘要到 Discord #tech-news 频道。同时发送到我的电子邮件 myemail@example.com。
```

**添加自定义来源：**
```text
将这些添加到我的科技摘要来源中：
- RSS: https://my-company-blog.com/feed
- Twitter: @myFavResearcher
- GitHub: my-org/my-framework
```

**按需生成：**
```text
生成过去 24 小时的科技摘要并发送到这里。
```

## 所需技能

- [tech-news-digest](https://clawhub.ai/skills/tech-news-digest) — 通过 `clawhub install tech-news-digest` 安装
- [gog](https://clawhub.ai/skills/gog)（可选）—— 用于通过 Gmail 发送电子邮件

## 环境变量（可选）

- `X_BEARER_TOKEN` — 用于 KOL 监控的 Twitter/X API bearer token
- `BRAVE_API_KEY` — 用于网页搜索层的 Brave Search API 密钥
- `GITHUB_TOKEN` — 用于更高 API 速率限制的 GitHub token

## 相关链接

- [GitHub 仓库](https://github.com/draco-agent/tech-news-digest)
- [ClawHub 页面](https://clawhub.ai/skills/tech-news-digest)

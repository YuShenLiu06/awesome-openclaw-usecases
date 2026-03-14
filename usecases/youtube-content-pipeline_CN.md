# YouTube 内容流水线

作为一个日常 YouTube 创作者，在整个网络和 X/Twitter 上查找新鲜、及时的视频想法既耗时又困难。跟踪你已经覆盖的内容可以防止重复并帮助你保持领先趋势。

这个工作流程自动化了整个内容发掘和研究流水线：

• 每小时 cron 作业扫描突发 AI 新闻（网络 + X/Twitter）并向 Telegram 推送视频想法
• 维护一个带有查看计数和主题分析的 90 天视频目录，以避免重复覆盖主题
• 将所有推送内容存储在带有向量嵌入的 SQLite 数据库中，用于语义去重（因此你永远不会被推送相同的想法两次）
• 当你在 Slack 中分享链接时，OpenClaw 研究主题、搜索 X 中的相关帖子、查询你的知识库，并创建一个带有完整大纲的 Asana 卡片

## 所需技能

- `web_search`（内置）
- [x-research-v2](https://clawhub.ai) 或自定义 X/Twitter 搜索技能
- [knowledge-base](https://clawhub.ai) 技能用于 RAG
- Asana 集成（或 Todoist）
- `gog` CLI 用于 YouTube Analytics
• 用于接收推送的 Telegram 主题

## 如何设置

1. 设置一个用于视频想法的 Telegram 主题并在 OpenClaw 中配置它。

2. 安装 knowledge-base 技能和 x-research 技能。

3. 创建一个 SQLite 数据库用于推送跟踪：
```sql
CREATE TABLE pitches (
  id INTEGER PRIMARY KEY,
  timestamp TEXT,
  topic TEXT,
  embedding BLOB,
  sources TEXT
);
```

4. 提示 OpenClaw：
```text
运行一个每小时的 cron 作业来：
1. 搜索网络和 X/Twitter 以获取突发 AI 新闻
2. 根据我的 90 天 YouTube 目录进行检查（通过 gog 从 YouTube Analytics 获取）
3. 根据数据库中所有过去的推送检查语义相似性
4. 如果是新的，将想法推送到我的 Telegram "video ideas" 主题并附带来源

此外：当我在 Slack #ai_trends 中分享链接时，自动：
1. 研究主题
2. 搜索 X 中的相关帖子
3. 查询我的知识库
4. 在视频流水线中创建一个带有完整大纲的 Asana 卡片
```

# 带有自动联系人发现功能的个人 CRM

手动跟踪你见过的人、时间以及讨论的内容是不可能的。重要的跟进会遗漏，而在重要会议之前你会忘记上下文。

这个工作流程自动构建和维护个人 CRM：

• 每日 cron 作业扫描电子邮件和日历以查找新联系人和互动
• 将联系人存储在带有关系上下文的结构化数据库中
• 自然语言查询："我对[某人]了解什么？"、"谁需要跟进？"、"我上次和[某人]是什么时候交谈的？"
• 每日会议准备简报：在每天会议之前，通过 CRM + 电子邮件历史研究外部与会者并提供简报

## 所需技能

- `gog` CLI（用于 Gmail 和 Google Calendar）
- 自定义 CRM 数据库（SQLite 或类似）或使用 [crm-query](https://clawhub.ai) 技能（如果可用）
- 用于 CRM 查询的 Telegram 主题

## 如何设置

1. 创建 CRM 数据库：
```sql
CREATE TABLE contacts (
  id INTEGER PRIMARY KEY,
  name TEXT,
  email TEXT,
  first_seen TEXT,
  last_contact TEXT,
  interaction_count INTEGER,
  notes TEXT
);
```

2. 设置一个名为 "personal-crm" 的 Telegram 主题用于查询。

3. 提示 OpenClaw：
```text
在上午 6 点运行每日 cron 作业以：
1. 扫描我的 Gmail 和日历过去 24 小时的内容
2. 提取新联系人并更新现有联系人
3. 记录互动（会议、电子邮件），包括时间戳和上下文

此外，每天早上 7 点：
1. 检查我的日历今天的会议
2. 对于每个外部与会者，搜索我的 CRM 和电子邮件历史
3. 向 Telegram 发送简报，包括：他们是谁、我们上次交谈的时间、我们讨论的内容以及任何后续事项

当我在 personal-crm 主题中询问联系人时，搜索数据库并告诉我你所知道的一切。
```

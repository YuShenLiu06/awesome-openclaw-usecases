# 项目状态管理系统：事件驱动的看板替代方案

传统的看板是静态的，需要手动更新。你会忘记移动卡片，在会话之间丢失上下文，并且无法跟踪状态变化背后的"原因"。项目在没有清晰可见性的情况下漂移。

这个工作流程用自动跟踪项目状态的事件驱动系统替换看板：

• 在数据库中存储项目状态并具有完整历史
• 捕获上下文：决策、阻塞因素、后续步骤、关键见解
• 事件驱动更新："刚完成 X，被 Y 阻塞" → 自动状态转换
• 自然语言查询："[项目]的状态是什么？"、"我们为什么在[功能]上转向？"
• 每日站会摘要：昨天发生了什么、今天计划了什么、什么被阻塞
• Git 集成：将提交链接到项目事件以实现可追溯性

## 痛点

看板变得陈旧。你浪费时间更新卡片而不是做工作。上下文丢失 —— 三个月后，你不记得为什么做出关键决策。代码更改和项目进展之间没有自动链接。

## 功能说明

与其拖动卡片，你与助理聊天："完成身份验证流程，开始仪表板。"系统记录事件，更新项目状态，并保留上下文。当你问"我们在仪表板上的情况？"时，它会给你完整的故事：完成了什么、下一步是什么、什么阻止了你以及为什么。

Git 提交被自动扫描并链接到项目。你的每日站会摘要会自动编写。

## 所需技能

- `postgres` 或 SQLite 用于项目状态数据库
- `github`（gh CLI）用于提交跟踪
- Discord 或 Telegram 用于更新和查询
- Cron 作业用于每日摘要
- 用于并行项目分析的子智能体

## 如何设置

1. 设置项目状态数据库：
```sql
CREATE TABLE projects (
  id SERIAL PRIMARY KEY,
  name TEXT UNIQUE,
  status TEXT, -- 例如，"active"、"blocked"、"completed"
  current_phase TEXT,
  last_update TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE events (
  id SERIAL PRIMARY KEY,
  project_id INTEGER REFERENCES projects(id),
  event_type TEXT, -- 例如，"progress"、"blocker"、"decision"、"pivot"
  description TEXT,
  context TEXT,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE blockers (
  id SERIAL PRIMARY KEY,
  project_id INTEGER REFERENCES projects(id),
  blocker_text TEXT,
  status TEXT DEFAULT 'open', -- "open"、"resolved"
  created_at TIMESTAMPTZ DEFAULT NOW(),
  resolved_at TIMESTAMPTZ
);
```

2. 创建一个 Discord 频道用于项目更新（例如，#project-state）。

3. 提示 OpenClaw：
```text
你是我的项目状态管理器。不要看板，我会对话式地告诉你我在做什么。

当我说诸如：
- "完成[任务]" → 记录一个"进展"事件，更新项目状态
- "被[问题]阻塞" → 创建一个阻塞条目，将项目状态更新为"blocked"
- "开始[新任务]" → 记录一个"进展"事件，更新当前阶段
- "决定[决策]" → 记录一个"决策"事件并包含完整上下文
- "转向[新方法]" → 记录一个"pivot"事件并包含推理

当我问：
- "[项目]的状态是什么？" → 获取最新事件、阻塞因素和当前阶段
- "我们为什么决定[X]？" → 搜索事件的决策上下文
- "什么在阻止我们？" → 列出项目中所有开放的阻塞因素

每天上午 9 点，运行一个 cron 作业来：
1. 扫描过去 24 小时的 git 提交（通过 gh CLI）
2. 根据分支名称或提交消息将提交链接到项目
3. 向 Discord #project-state 发布每日站会摘要：
   - 昨天发生了什么（事件 + 提交）
   - 今天计划了什么（基于当前阶段和最近的对话）
   - 什么被阻塞（开放的阻塞因素）

当我计划一个冲刺时，生成一个子智能体来分析每个项目的状态并建议优先级。
```

4. 集成到你的工作流程：只需自然地与你的助理谈论你在做什么。系统会捕获所有内容。

## 相关链接

- [事件溯源模式](https://martinfowler.com/eaaDev/EventSourcing.html)
- [为什么看板对个人开发者无效](https://blog.nuclino.com/why-kanban-doesnt-work-for-me)

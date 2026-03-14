# 带有子智能体生成的动态仪表板

静态仪表板显示陈旧数据，并且需要持续的手动更新。你想要跨多个数据源的实时可见性，而无需构建自定义前端或达到 API 速率限制。

这个工作流程创建一个实时仪表板，生成子智能体以并行获取和处理数据：

• 同时监控多个数据源（API、数据库、GitHub、社交媒体）
• 为每个数据源生成子智能体以避免阻塞并分散 API 负载
• 将结果聚合到统一仪表板（文本、HTML 或 Canvas）
• 每 N 分钟使用新鲜数据更新
• 当指标超过阈值时发送警报
• 在数据库中维护历史趋势以进行可视化

## 痛点

构建自定义仪表板需要数周时间。到完成时，需求已经改变。顺序轮询多个 API 既慢又会达到速率限制。你现在就需要洞察，而不是在周末编码之后。

## 功能说明

你可以对话式定义要监控的内容："跟踪 GitHub 星星、Twitter 提及、Polymarket 交易量和系统健康状况。"OpenClaw 生成子智能体以并行获取每个数据源，聚合结果，并将格式化的仪表板发送到 Discord 或作为 HTML 文件。更新按 cron 计划自动运行。

示例仪表板部分：
- **GitHub**：星星、fork、开放问题、最近的提交
- **社交媒体**：Twitter 提及、Reddit 讨论、Discord 活动
- **市场**：Polymarket 交易量、预测趋势
- **系统健康**：CPU、内存、磁盘使用、服务状态

## 所需技能

- 用于并行执行的子智能体生成
- `github`（gh CLI）用于 GitHub 指标
- `bird`（Twitter）用于社交数据
- `web_search` 或 `web_fetch` 用于外部 API
- `postgres` 用于存储历史指标
- Discord 或 Canvas 用于渲染仪表板
- Cron 作业用于计划更新

## 如何设置

1. 设置指标数据库：
```sql
CREATE TABLE metrics (
  id SERIAL PRIMARY KEY,
  source TEXT, -- 例如，"github"、"twitter"、"polymarket"
  metric_name TEXT,
  metric_value NUMERIC,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE alerts (
  id SERIAL PRIMARY KEY,
  source TEXT,
  condition TEXT,
  threshold NUMERIC,
  last_triggered TIMESTAMPTZ
);
```

2. 创建一个 Discord 频道用于仪表板更新（例如，#dashboard）。

3. 提示 OpenClaw：
```text
你是我的动态仪表板管理器。每 15 分钟，运行一个 cron 作业来：

1. 并行生成子智能体以从以下位置获取数据：
   - GitHub：星星、fork、开放问题、提交（过去 24 小时）
   - Twitter：提及"@username"、情感分析
   - Polymarket：跟踪市场的交易量
   - 系统：通过 shell 命令的 CPU、内存、磁盘使用情况

2. 每个子智能体将结果写入指标数据库。

3. 聚合所有结果并格式化仪表板：

📊 **仪表板更新** — [时间戳]

**GitHub**
- ⭐ 星星：[数量] (+[变化])
- 🍴 Fork：[数量]
- 🐛 开放问题：[数量]
- 💻 提交（24小时）：[数量]

**社交媒体**
- 🐦 Twitter 提及：[数量]
- 📈 情感：[正面/负面/中性]

**市场**
- 📊 Polymarket 交易量：$[金额]
- 🔥 趋势：[市场名称]

**系统健康**
- 💻 CPU：[使用]%
- 🧠 内存：[使用]%
- 💾 磁盘：[使用]%

4. 发布到 Discord #dashboard。

5. 检查警报条件：
   - 如果 GitHub 星星在 1 小时内变化 > 50 → 通知我
   - 如果系统 CPU > 90% → 警报
   - 如果 Twitter 上出现负面情感激增 → 通知

将所有指标存储在数据库中用于历史分析。
```

4. 可选：使用 Canvas 渲染带有图表和图形的 HTML 仪表板。

5. 查询历史数据："向我展示过去 30 天的 GitHub 星星增长情况。"

## 相关链接

- [使用子智能体进行并行处理](https://docs.openclaw.ai/subagents)
- [仪表板设计原则](https://www.nngroup.com/articles/dashboard-design/)

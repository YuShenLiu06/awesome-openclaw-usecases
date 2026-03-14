# Polymarket 自动驾驶：自动模拟交易

手动监控预测市场的套利机会并执行交易既耗时又需要持续关注。你想在不冒真实资本风险的情况下测试和完善交易策略。

这个工作流程使用自定义策略在 Polymarket 上自动化模拟交易：

• 通过 API 监控市场数据（价格、交易量、价差）
• 使用 TAIL（趋势跟随）和 BONDING（逆向）策略执行模拟交易
• 跟踪投资组合表现、盈亏和胜率
• 将每日摘要连同交易日志和见解发送到 Discord
• 从模式中学习：根据回测结果调整策略参数

## 痛点

预测市场移动很快。手动交易意味着错失机会、情绪化决策，以及难以跟踪什么有效。在了解市场行为之前用真钱测试策略会带来损失风险。

## 功能说明

自动驾驶持续扫描 Polymarket 中的机会，使用可配置策略模拟交易，并记录所有内容用于分析。你醒来时会看到它隔夜"交易"了什么、什么有效、什么无效的摘要。

示例策略：
- **TAIL**：当交易量激增且势头清晰时跟随趋势
- **BONDING**：当市场对新闻过度反应时买入逆向头寸
- **SPREAD**：识别定价错误且具有套利潜力的市场

## 所需技能

- `web_search` 或 `web_fetch`（用于 Polymarket API 数据）
- `postgres` 或 SQLite 用于交易日志和投资组合跟踪
- Discord 集成用于每日报告
- Cron 作业用于持续监控
- 子智能体生成用于并行市场分析

## 如何设置

1. 设置一个用于模拟交易的数据库：
```sql
CREATE TABLE paper_trades (
  id SERIAL PRIMARY KEY,
  market_id TEXT,
  market_name TEXT,
  strategy TEXT,
  direction TEXT,
  entry_price DECIMAL,
  exit_price DECIMAL,
  quantity DECIMAL,
  pnl DECIMAL,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE portfolio (
  id SERIAL PRIMARY KEY,
  total_value DECIMAL,
  cash DECIMAL,
  positions JSONB,
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

2. 创建一个 Discord 频道用于更新（例如，#polymarket-autopilot）。

3. 提示 OpenClaw：
```text
你是 Polymarket 模拟交易自动驾驶。持续运行（通过 cron 每 15 分钟）：

1. 从 Polymarket API 获取当前市场数据
2. 使用这些策略分析机会：
   - TAIL：跟随强趋势（>60% 概率 + 交易量激增）
   - BONDING：对过度反应的逆向操作（新闻导致突然下跌 >10%）
   - SPREAD：当 YES+NO > 1.05 时套利
3. 在数据库中执行模拟交易（起始资本：$10,000）
4. 跟踪投资组合状态并更新头寸

每天上午 8 点，向 Discord #polymarket-autopilot 发布摘要：
- 昨天的交易（进入/退出价格、盈亏）
- 当前投资组合价值和开放头寸
- 胜率和策略表现
- 市场见解和建议

使用子智能体在高交易量期间并行分析多个市场。

绝不使用真钱。这只是模拟交易。
```

4. 根据表现迭代策略。调整阈值，添加新策略，回测历史数据。

## 相关链接

- [Polymarket API](https://docs.polymarket.com/)
- [模拟交易最佳实践](https://www.investopedia.com/articles/trading/11/paper-trading.asp)

# 多渠道 AI 客户服务平台

小企业在多个应用程序中兼顾 WhatsApp、Instagram DM、电子邮件和 Google 评价。客户期望即时响应 24/7，但雇用员工进行全天候覆盖既昂贵。

这个使用案例将所有客户接触点整合到一个单一的 AI 驱动的收件箱，它代表你智能响应。

## 功能说明

- **统一收件箱**：在一个地方提供 WhatsApp Business、Instagram DM、Gmail 和 Google 评价
- **AI 自动响应**：自动处理常见问题解答、预约请求和常见咨询
- **人工交接**：将复杂问题转交以进行审核或标记以供审核
- **测试模式**：在不影响真实客户的情况下向客户演示系统
- **业务上下文**：针对你的服务、定价和策略进行训练

## 真实业务示例

在 Futurist Systems，我们为本地服务企业（餐厅、诊所、沙龙）部署此功能。一家餐厅将响应时间从 4+ 小时减少到不到 2 分钟，自动处理 80% 的咨询。

## 所需技能

- WhatsApp Business API 集成
- Instagram Graph API（通过 Meta Business）
- `gog` CLI 用于 Gmail
- Google Business Profile API 用于评价
- AGENTS.md 中的消息路由逻辑

## 如何设置

1. **通过 OpenClaw 配置连接频道**：
   - WhatsApp Business API（通过 360dialog 或官方 API）
   - Instagram 通过 Meta Business Suite
   - Gmail 通过 `gog` OAuth
   - Google Business Profile API token

2. **创建业务知识库**：
   - 服务和定价
   - 营业时间和地点
   - 常见问题解答响应
   - 升级触发器（例如，投诉、退款请求）

3. **配置 AGENTS.md** 带有路由逻辑：

```text
## 客户服务模式

当接收客户消息时：

1. 识别渠道（WhatsApp/Instagram/电子邮件/评价）
2. 检查是否为此客户端启用了测试模式
3. 分类意图：
   - 常见问题解答 → 从知识库响应
   - 预约 → 检查可用性，确认预订
   - 投诉 → 标记以供人工审核，确认收到
   - 评价 → 感谢反馈，解决疑虑

响应风格：
- 友好、专业、简洁
- 匹配客户的语言（ES/EN/UA）
- 绝不编造知识库中没有的信息
- 用业务名称签名结束

测试模式：
- 用 [TEST] 为响应添加前缀
- 记录但不发送到真实渠道
```

4. **设置心跳检查** 用于响应监控：

```text
## 心跳：客户服务检查

每 30 分钟：
- 检查超过 5 分钟的未回复消息
- 如果响应队列备份则警报
- 记录每日响应指标
```

## 核心见解

- **语言检测很重要**：自动检测并用客户的语言响应
- **测试模式必不可少**：客户端需要在上线之前看到它工作
- **交接规则**：定义明确的升级触发器以避免 AI 超越
- **响应模板**：为敏感主题（退款、投诉）预先批准的模板

## 相关链接

- [WhatsApp Business API](https://developers.facebook.com/docs/whatsapp)
- [Instagram Messaging API](https://developers.facebook.com/docs/instagram-api/guides/messaging)
- [Google Business Profile API](https://developers.google.com/my-business)

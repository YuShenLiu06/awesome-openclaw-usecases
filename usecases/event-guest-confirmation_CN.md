# 活动嘉宾确认

你正在举办一个活动 —— 晚宴、婚礼、公司郊游 —— 你需要从嘉宾名单确认出席。手动给 20+ 个人打电话很繁琐：你玩电话标签，忘记谁说了什么，并丢失对饮食限制或携带者的跟踪。发短信有时有效，但人们会忽略消息。真正的电话电话的回复率高得多。

这个使用案例让 OpenClaw 使用 [SuperCall](https://clawhub.ai/wonder/supercall) 插件逐一给你名单上的每个嘉宾打电话，确认他们是否参加，收集任何笔记，并为你在一份摘要中汇编所有内容。

## 功能说明

- 迭代嘉宾名单（姓名 + 电话号码）并给每人打电话
- AI 以友好的个性向你介绍自己作为你的活动协调员
- 向嘉宾确认活动日期、时间和地点
- 询问他们是否参加，并收集任何笔记（饮食需求、携带者、到达时间等）
- 所有电话完成后，汇编摘要：谁确认了、谁拒绝了、谁没接听以及任何笔记

## 为什么使用 SuperCall

这个使用案例专门与 [SuperCall](https://clawhub.ai/wonder/supercall) 插件一起工作 —— 而不是内置的 `voice_call` 插件。关键区别：SuperCall 是一个完全独立的语音智能体。电话上的 AI 个性**只能访问你提供的上下文**（个性名称、目标和开场白）。它无法访问你的网关智能体、你的文件、你的其他工具或任何其他东西。

这对于嘉宾确认很重要，因为：
- **安全性**：电话另一端的人无法通过对话操纵或访问你的智能体。没有提示词注入或数据泄露的风险。
- **更好的对话**：因为 AI 范围限定于单个专注任务（确认出席），它保持话题并比通用语音智能体更自然地处理电话。
- **批量友好**：你要给不同的人打很多电话。每次通话重置的沙盒化个性正是你想要的 —— 对话之间没有渗漏。

## 所需技能

- [SuperCall](https://clawhub.ai/wonder/supercall) —— 通过 `openclaw plugins install @wonder/supercall` 安装
- 一个带有电话号码的 Twilio 账户（用于拨出电话）
- 一个 OpenAI API 密钥（用于 GPT-4o 实时语音 AI）
- ngrok（用于 web 隧道 —— 免费层适用）

参见 [SuperCall README](https://github.com/wonder/supercall) 获取完整配置说明。

## 如何设置

1. 按照[设置指南](https://github.com/wonder/supercall#configuration)安装和配置 SuperCall。确保在你的 OpenClaw 配置中启用了钩子。

2. 准备你的嘉宾名单。你可以直接在聊天中粘贴它或将其保存在文件中：

```text
嘉宾名单 —— 夏季 BBQ，6 月 14 日星期六，下午 4 点，23 Oak 街

- Sarah Johnson: +15551234567
- Mike Chen: +15559876543
- Rachel Torres: +15555551234
- David Kim: +15558887777
```

3. 提示 OpenClaw：
```text
我需要你为我的活动确认出席。以下是详细信息：

活动：夏季 BBQ
日期：6 月 14 日星期六，下午 4 点
地点：23 Oak 街

以下是我的嘉宾名单：
<在此粘贴你的嘉宾名单>

对于每位嘉宾，使用 supercall 给他们打电话。使用个性"Jamie，活动协调员"
代表 [你的名字]"。每次通话的目标是确认他们是否参加，
并记录任何饮食限制、携带者或其他评论。

每次通话后，记录结果。所有通话完成后，给我一个完整摘要：
- 谁确认了
- 谁拒绝了
- 谁没接听
- 每位嘉宾的任何笔记或特殊请求
```

4. OpenClaw 将使用 SuperCall 逐一给每位嘉宾打电话，然后汇编结果。你可以随时通过询问状态更新来检查进度。

## 核心见解

- **从小测试开始**：先用 2-3 位嘉宾尝试，以确保个性和开场白听起来正确。在给完整名单打电话之前你可以调整语气。
- **注意通话时间**：不要安排太早或太晚的电话。你可以告诉 OpenClaw 只在特定时间之间打电话。
- **审查转录**：SuperCall 将转录记录到 `~/clawd/supercall-logs`。在第一批之后浏览它们以查看对话进展如何。
- **无应答处理**：如果某人没接听，OpenClaw 可以记录它，你可以决定稍后重试还是通过短信跟进。
- **真实电话电话花费资金**：每次电话都使用 Twilio 分钟数。在你的 Twilio 账户中设置适当的限制，特别是对于大型嘉宾名单。

## 相关链接

- [ClawHub 上的 SuperCall](https://clawhub.ai/wonder/supercall)
- [GitHub 上的 SuperCall](https://github.com/wonder/supercall)
- [Twilio 控制台](https://console.twilio.com)
- [OpenAI 实时 API](https://platform.openai.com/docs/guides/realtime)
- [ngrok](https://ngrok.com)

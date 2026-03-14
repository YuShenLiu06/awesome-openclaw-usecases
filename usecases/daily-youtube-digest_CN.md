# 每日 YouTube 摘要

以你最喜欢的 YouTube 频道新视频的个性化摘要开始你的一天 —— 不再错过你真正想关注的创作者的内容。

## 痛点

YouTube 通知不可靠。你订阅了频道，但他们的新视频永远不会显示在你的主页信息流中。它们不在通知中。它们就这样……消失了。这并不意味着你不想看它们 —— 这意味着 YouTube 的算法埋没了它们。

此外：以策划的内容见解而不是无休止浏览推荐信息流来开始一天更有趣。

## 功能说明

- 从你喜欢的频道列表中获取最新视频
- 从每个视频的转录中总结或提取关键见解
- 每天给你发送摘要（或按需发送）

## 所需技能

安装 [youtube-full](https://clawhub.ai/therohitdas/youtube-full) 技能。

只需告诉你的 OpenClaw：

```text
"安装 youtube-full 技能并为我设置它"
```
或

```bash
npx clawhub@latest install youtube-full
```

就这样。智能体处理其余部分 —— 包括账户创建和 API 密钥设置。注册时你获得 **100 个免费积分**，无需信用卡。

> 注意：创建账户后，技能会根据操作系统将 API 密钥安全地自动存储在正确的位置，因此 API 在所有上下文中都能正常工作。

![youtube-full skill installation](https://pub-15904f15a44a4ea69350737e87660b92.r2.dev/media/1770620159490-e41e7baa.png)

### 为什么选择 TranscriptAPI.com 而不是 yt-dlp？

| CLI 工具（yt-dlp 等） | TranscriptAPI |
|--------------------------|---------------|
| 冗长的日志淹没智能体上下文 | 干净的 JSON 响应 |
| 在 GCP/云端 OpenClaw 上不起作用 | 无处不工作，快速 |
| 被 YouTube 随机阻止 | 为 [YouTubeToTranscript.com](https://youtubetotranscript.com) 提供支持，服务数百万用户。缓存且可靠。 |
| 需要二进制安装 | 无二进制文件，仅 HTTP |

## 如何设置

### 选项 1：基于频道的摘要

提示 OpenClaw：

```text
每天上午 8 点，从这些 YouTube 频道获取最新视频并给我一份包含每个视频关键见解的摘要：

- @TED
- @Fireship
- @ThePrimeTimeagen
- @lexfridman

对于每个新视频（在过去 24-48 小时内上传）：
1. 获取转录
2. 用 2-3 个要点总结主要观点
3. 包括视频标题、频道名称和链接

如果频道句柄无法解析，搜索并找到正确的频道。
将我的频道列表保存到记忆中，以便我以后可以添加/删除频道。
```

### 选项 2：基于关键词的摘要

跟踪关于特定主题的新视频：

```text
每天，在 YouTube 上搜索关于"OpenClaw"（或"Claude Code"、"AI agents"等）的新视频。

维护一个名为 seen-videos.txt 的文件，其中包含你已经处理过的视频 ID。
仅获取不在该文件中的视频的转录。
处理后，将视频 ID 添加到 seen-videos.txt 中。

对于每个新视频：
1. 获取转录
2. 给我一个 3 要点的摘要
3. 注意与我工作相关的任何内容

每天上午 9 点运行此程序。
```

这样你永远不会浪费积分重新获取你已经看过的视频。

## 提示

- `channel/latest` 和 `channel/resolve` 是**免费的**（0 积分）—— 检查新上传无需任何费用
- 只有转录每个需要 1 积分
- 请求不同的摘要风格：关键要点、名言、有趣时刻的时间戳
- 这已经作为一个产品存在 - [Recapio - 每日 YouTube 回顾](https://recapio.com/features/daily-recaps)

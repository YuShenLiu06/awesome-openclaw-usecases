# 自主教育游戏开发流水线

## 痛点

**起源故事：** 一位"老学校的 LANero"父亲想为他的女儿 Susana（3 岁）和即将到来的 Julieta 创建一个安全、无广告和高质量的游戏门户。现有网站被垃圾信息、激进广告和欺骗性按钮（暗色模式）所困扰，这让他的幼儿感到沮丧。

**挑战：** 构建"干净、快速且简单"的门户很容易。真正的挑战是用**40+ 个教育游戏**填充它，这些游戏针对特定的发展阶段（0-15 岁），而无需开发团队。手动开发对于单亲开发者来说太慢，而在数十个游戏中保持一致性正在成为噩梦。

## 功能说明

这个使用案例定义了一个"游戏开发智能体"，它自主管理游戏创建和维护的整个生命周期。该工作流程强制执行**"Bug 优先"**策略，即智能体必须在实施新功能之前检查并解决报告的 Bug。

**效率：** 该流水线能够以**每 7 分钟生产 1 个新游戏或 Bug 修复**。智能体不知疲倦地迭代 41+ 个计划游戏的积压，在创建新内容和纠正在前一个周期中检测到的问题之间交替。

当路径清晰时，智能体：
1. **选择**：基于"轮循"策略从队列（`development-queue.md`）中识别下一个游戏，以平衡各个年龄段的内容。
2. **实施**：为游戏编写 HTML5/CSS3/JS 代码，遵循严格的 `game-design-rules.md`（无框架、移动优先、可离线）。
3. **注册**：自动将游戏元数据添加到中央注册表（`games-list.json`）。
4. **记录**：更新 `CHANGELOG.md` 和 `master-game-plan.md` 状态。
5. **部署**：处理 Git 工作流程：获取 master、创建功能分支、使用传统提交提交更改并合并回来。

## 提示词

该工作流程的核心是**系统指令**，提供给智能体。此提示词将 LLM 转变为尊重项目刚性结构的纪律严明的开发者。

*（*注意：* 生产中使用的实际提示词是**西班牙语**（`es-419`），以与项目的目标受众（拉丁美洲儿童）和该地区的潜在未来贡献者保持一致。下面的版本已翻译用于此文档。）*

```text
作为 Web 游戏开发和儿童 UX 方面的专家。
你的目标是开发生产队列中的下一个游戏。

在开始之前请阅读和分析以下上下文文件：

1. BUG 上下文（最高优先级 - 关键）：
    @[bugs/]
    （检查此文件夹。如果有文件，你的任务仅是修复**按字母顺序的第一个文件**。暂时忽略其余的 bug 和游戏队列）。

2. 队列上下文（下一个游戏是什么）：
    @[development-queue.md]
    （识别在"下一个游戏"部分标记为 [NEXT] 的游戏。仅当没有 bug 时）。

3. 设计规则（技术标准）：
    @[game-design-rules.md]
    （严格遵循这些规则：纯 HTML/CSS/JS、文件夹结构、移动响应能力）

4. 游戏规格（机制和资产）：
    （根据游戏 ID 在 games-backlog/ 中识别对应文件）

5. 中央注册表（集成）：
    @[public/js/games-list.json]
    （你必须在此文件中注册新游戏，以便它出现在主页上）

任务：
0. **Bug 优先！**：如果 `bugs/` 文件夹有内容，你的唯一优先级是按字母顺序修复**第一个 bug**。创建一个 `fix/...` 分支，解决**那个** bug，更新状态并合并。**不要一次尝试修复多个 Bug。**
   - 如果没有 Bug，继续下一个游戏：

1. **同步**：`git fetch && git pull origin master`（关键）。
2. 创建一个新分支：`git checkout -b feature/[game-id]`。
3. 在 'public/games/[game-id]/' 中创建文件夹和文件。
4. 根据积压和设计规则实施逻辑和设计。
5. 在 'games-list.json' 中注册游戏（关键）。
6. 完成时：
    - 更新 `CHANGELOG.md`，递增版本。
    - 更新 `master-game-plan.md` 和 `development-queue.md`。
    - 记录更改：`git commit -m "feat: add [game-id]"`。
7. **交付**：
    - 推送：`git push origin feature/[game-id]`。
    - 请求合并到 master。
    - 一旦在 master 中，推送更改（`git push origin master`）。
```

## 所需技能

-   **Git**：用于管理分支、提交和合并。

## 相关链接

-   [项目起源故事（LinkedIn）](https://www.linkedin.com/feed/update/urn:li:activity:7429739200301772800/) - 配置 OpenClaw 后该项目是如何出现的。
-   [El Bebe Games 仓库](https://github.com/duberblockito/elbebe/tree/master) - 源代码。
-   [El Bebe Games 现场网站](https://elbebe.co/) - 该流水线的结果。
-   [HTML5 游戏开发最佳实践](https://developer.mozilla.org/en-US/docs/Games)

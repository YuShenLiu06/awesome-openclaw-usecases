# 带有子智能体的自主项目管理

管理具有多个并行工作流的复杂项目令人筋疲力尽。你最终不断地上下文切换、跨工具跟踪状态，并手动协调交接。

这个使用案例实施了一种去中心化的项目管理模式，其中子智能体自主处理任务，通过共享状态文件而不是中央编排器进行协调。

## 痛点

传统的编排器模式创建瓶颈 —— 主智能体成为交通警察。对于复杂项目（多仓库重构、研究冲刺、内容流水线），你需要能够在没有持续监督的情况下并行工作的智能体。

## 功能说明

- **去中心化协调**：智能体读/写共享的 `STATE.yaml` 文件
- **并行执行**：多个子智能体同时在独立任务上工作
- **无编排器开销**：主会话保持精简（CEO 模式 —— 仅战略）
- **自我记录**：所有任务状态持久化在版本控制的文件中

## 核心模式：STATE.yaml

每个项目维护一个 `STATE.yaml` 文件，作为单一真实来源：

```yaml
# STATE.yaml - 项目协调文件
project: website-redesign
updated: 2026-02-10T14:30:00Z

tasks:
  - id: homepage-hero
    status: in_progress
    owner: pm-frontend
    started: 2026-02-10T12:00:00Z
    notes: "Working on responsive layout"

  - id: api-auth
    status: done
    owner: pm-backend
    completed: 2026-02-10T14:00:00Z
    output: "src/api/auth.ts"

  - id: content-migration
    status: blocked
    owner: pm-content
    blocked_by: api-auth
    notes: "Waiting for new endpoint schema"

next_actions:
  - "pm-content: 既然 api-auth 完成，立即恢复迁移"
  - "pm-frontend: 与设计团队审查 hero"
```

## 它是如何工作的

1. **主智能体接收任务** → 生成具有特定范围的子智能体
2. **子智能体读取 STATE.yaml** → 找到其分配的任务
3. **子智能体自主工作** → 在进展时更新 STATE.yaml
4. **其他智能体轮询 STATE.yaml** → 获取未阻塞的工作
5. **主智能体定期检查** → 审查状态，调整优先级

## 所需技能

- `sessions_spawn` / `sessions_send` 用于子智能体管理
- 用于 STATE.yaml 的文件系统访问
- Git 用于状态版本化（可选但推荐）

## 设置：AGENTS.md 配置

```text
## PM 委派模式

主会话 = 仅协调器。所有执行都交给子智能体。

工作流程：
1. 新任务到达
2. 检查 PROJECT_REGISTRY.md 以获取现有 PM
3. 如果 PM 存在 → sessions_send(label="pm-xxx", message="[task]")
4. 如果是新项目 → sessions_spawn(label="pm-xxx", task="[task]")
5. PM 执行，更新 STATE.yaml，报告回来
6. 主智能体向用户总结

规则：
- 主会话：最多 0-2 次工具调用（仅生成/发送）
- PM 拥有自己的 STATE.yaml 文件
- PM 可以生成子子智能体用于并行子任务
- 所有状态更改提交到 git
```

## 示例：生成 PM

```text
用户："重构身份验证模块并更新文档"

主智能体：
1. 检查注册表 → 没有活动的 pm-auth
2. 生成：sessions_spawn(
     label="pm-auth-refactor",
     task="重构身份验证模块，更新文档。在 STATE.yaml 中跟踪"
   )
3. 响应："已生成 pm-auth-refactor。完成后我会报告。"

PM 子智能体：
1. 创建 STATE.yaml 并包含任务分解
2. 处理任务，更新状态
3. 提交更改
4. 向主智能体报告完成
```

## 核心见解

- **STATE.yaml > 编排器**：基于文件的协调比消息传递扩展得更好
- **Git 作为审计日志**：提交 STATE.yaml 更改以获得完整历史
- **标签约定很重要**：使用 `pm-{project}-{scope}` 以便于跟踪
- **精简的主会话**：主智能体做的事情越少，它响应得越快

## 基于

这种模式受 [Nicholas Carlini 的方法](https://nicholas.carlini.com/) 启发，关于自主编码智能体 —— 让智能体自我组织而不是微观管理它们。

## 相关链接

- [OpenClaw 子智能体文档](https://github.com/openclaw/openclaw)
- [Anthropic：构建有效的智能体](https://www.anthropic.com/research/building-effective-agents)

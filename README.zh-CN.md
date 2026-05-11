# AiPlus Agent Velocity
[English README](README.md)

## The Problem（问题）

Agents mis-bill their work in human-engineer hours, badly mismatched with
reality.

你让 Agent 做一次重构。Agent 说五小时。你据此安排下午的时间。二十分钟后，
Agent 报告已完成。下周，一个类似的任务又得到同样的五小时预估。还是二十分钟。
没有人记分，所以同样的高估下周再次发生。一个月后，你不再相信 Agent 给出的
任何预估。

## The Solution（解决方案）

AiPlus Agent Velocity 将每次预估和实际完成时间记录为 `.aiplus/velocity/`
下的本地 JSONL。它追踪：

- 人类预估（Agent 最初预测的时间）
- 实际完成时间（真正花了多久）
- 任务类型（重构、功能、Bug 修复、审阅）
- 模型和工作流标签（用于上下文）

系统会检测 **人类时间偏差** —— 将预估锚定在工程师小时数而非 Agent 分钟数的
倾向。积累若干记录后，它会输出：

- **p50 预估** —— 该任务类型的 AI 原生中位时间
- **p90 预估** —— 保守上限
- **下次预估调整** —— 应用于未来人类风格预估的乘数

不存储原始提示词。不上传数据。常规记录保留最近 200 条，罕见案例保留
最近 20 条。

## Quick Start（快速开始）

已安装 AiPlus 的情况下：

```bash
cd MyProject
aiplus install codex        # 或：claude-code、opencode、all
aiplus velocity init
```

然后使用 CLI：

```bash
aiplus velocity init                      # 初始化 velocity 追踪
aiplus velocity estimate                  # 创建 AI 原生预估
aiplus velocity complete                  # 记录实际完成时间
aiplus velocity bias --task <id>          # 检查指定任务的偏差
aiplus velocity report                    # 显示整体偏差与调整建议
aiplus velocity doctor                    # 运行 velocity 健康检查
aiplus velocity purge --yes               # 手动清理旧记录
```

## What's Inside（项目结构）

- `core/schemas/` —— 配置、预估记录、运行记录、罕见案例记录的 JSON Schema
- `core/` —— 时长解析器、偏差检测算法、保留策略逻辑
- `DESIGN.md` —— 架构决策与设计 rationale

## Storage（存储）

所有数据以本地 JSONL 文件形式存放在 `.aiplus/velocity/`：

```
.aiplus/velocity/
  config.json           # 配置
  estimates.jsonl       # 预估记录
  runs.jsonl            # 完成记录
  rare-cases.jsonl      # 罕见案例记录
  multipliers.json      # 聚合调整乘数
  rotation-state.json   # 轮换追踪
```

- 无 SQLite。无数据库。
- 常规记录：保留最近 200 条
- 罕见案例：保留最近 20 条
- 聚合乘数在原始记录轮换后仍然保留

## Safety Boundaries（安全边界）

AiPlus Agent Velocity 不会：

- 存储原始提示词、会话记录或源代码
- 上传数据或实现遥测
- 替代测试、审阅或 Owner 关卡
- 充当生产力追踪或 KPI 系统
- 以缩短预估为借口跳过验证

## More Info（更多信息）

查看 [主 AiPlus 仓库](https://github.com/izhiwen/aiplus) 获取完整平台信息。

当前缺口与计划工作：
[v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)。

## License（许可证）

[Apache-2.0](LICENSE)

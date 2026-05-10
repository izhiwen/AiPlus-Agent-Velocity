# AiPlus Agent Velocity

[English README](README.md)

## 问题所在

agent 说重构任务要五小时。你按这个安排了下午。二十分钟后 agent 报告完成。下周类似任务又估五小时。又是二十分钟。没人记录实际时间。没人调整未来估时。一个月后，你不再相信 agent 给的任何估时。

## 它能做什么

AiPlus Agent Velocity 把每次估时和实际完成时间记为本地 JSONL，存在 `.aiplus/velocity/` 下。它追踪：

- 人类估时（agent 最初预测的时间）
- 实际完成时间（真正花了多久）
- 任务类型（重构、功能、bugfix、review）
- 模型和工作流标签（用于上下文）

系统检测 **human-time bias** — 把估时锚定在工程师小时而不是 agent 分钟的趋势。积累几条记录后，它输出：

- **p50 估时** — 该任务类型的 AI-native 中位数时间
- **p90 估时** — 保守上限
- **Next-estimate adjustment** — 应用于未来人类风格估时的乘数

不存 raw prompt。不上传数据。普通记录保留最新 200 条，rare case 保留最新 20 条。

## 安装

已安装 AiPlus：

```bash
cd MyProject
aiplus install codex        # 或: claude-code, opencode, all
aiplus velocity init
```

或使用 standalone 源码：

```bash
git clone https://github.com/izhiwen/aiplus-agent-velocity.git
cd aiplus-agent-velocity
```

## Runtime 支持

Velocity 支持所有三种 agent。为对应 runtime 安装 AiPlus：

```bash
aiplus install codex        # Codex
aiplus install claude-code  # Claude Code
aiplus install opencode     # OpenCode
```

## 工作原理

**创建估时：**

```bash
aiplus velocity estimate \
  --task-type compact-hardening \
  --human-estimate 5h \
  --model kimi-k2.7 \
  --workflow HEAVY
```

输出显示人类估时是否包含 bias，以及 AI-native 估时应该是多少。

**记录完成：**

```bash
aiplus velocity complete \
  --task-id task_123 \
  --actual 20m \
  --outcome pass
```

系统更新内部模型并报告高估比例。

**检查 bias：**

```bash
aiplus velocity report      # 显示总体 bias 和调整
aiplus velocity bias --task task_123  # 检查特定任务 bias
```

## 仓库结构

- `core/schemas/` — config、estimate-record、run-record、rare-case-record 的 JSON schema
- `core/` — Duration parser、bias detection 算法、retention logic
- `DESIGN.md` — 架构决策和设计原理
- `README.md` — 本文档

## 命令

```bash
aiplus velocity init                      # 初始化 velocity 追踪
aiplus velocity estimate                  # 创建 AI-native 估时
aiplus velocity complete                  # 记录实际完成时间
aiplus velocity bias --task <id>          # 检查特定任务 bias
aiplus velocity report                    # 显示总体 bias 和调整报告
aiplus velocity doctor                    # 运行 velocity 健康检查
aiplus velocity purge --yes               # 手动清理旧记录
```

## 存储

所有数据以本地 JSONL 存在 `.aiplus/velocity/`：

```
.aiplus/velocity/
  config.json           # 配置
  estimates.jsonl       # 估时记录
  runs.jsonl            # 完成记录
  rare-cases.jsonl      # Rare case 记录
  multipliers.json      # 聚合调整乘数
  rotation-state.json   # 轮换追踪
```

- 无 SQLite。无数据库。
- 普通记录：保留最新 200 条
- Rare cases：保留最新 20 条
- 聚合乘数在原始记录轮换后仍然保留

## 安全

AiPlus Agent Velocity 不：
- 存储 raw prompts、transcripts 或源代码
- 上传数据或实现 telemetry
- 替代 tests、review 或 Owner gates
- 作为 productivity tracker 或 KPI 系统
- 以估时变短为借口跳过验证

## 更多信息

见 [主 AiPlus 仓库](https://github.com/izhiwen/aiplus) 了解完整平台。

当前缺口和计划工作：[v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)。

## License

[Apache-2.0](LICENSE)

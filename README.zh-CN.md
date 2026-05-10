# AiPlus Agent Velocity

[English README](README.md)

## 痛点

agent 说一个任务要 5 小时。你点头去喝咖啡。20 分钟后它说"做完了"。下周同一个 agent 对类似任务又估 5 小时。又是 20 分钟。没人记下实际时间。这个模式反复出现，直到你不再相信任何估时。

## 解决方案

AiPlus Agent Velocity 把每次估时和实际完成时间记为本地 JSONL，存在 `.aiplus/velocity/` 下。它检测 human-time bias：当估时锚定在工程师小时而不是 agent 分钟时触发提醒。积累几条记录后，它输出 p50 和 p90 的 AI-native 估时，并显示 next-estimate adjustment。不存 raw prompt，不上传数据。普通记录保留最新 200 条，rare case 保留最新 20 条。

## 快速开始

如果你已安装 AiPlus：

```bash
cd MyProject
aiplus install codex        # 或: claude-code, opencode, all
aiplus velocity init
aiplus velocity estimate --task-type compact-hardening --human-estimate 5h
```

或 clone standalone 源码：

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

## 内部结构

- `core/schemas/` — config、estimate-record、run-record、rare-case-record 的 JSON schema
- `core/` — Duration parser、bias detection、retention logic
- `README.md` — 本文档
- `DESIGN.md` — 架构与设计决策

## 常用命令

```bash
aiplus velocity init                      # 初始化 velocity 追踪
aiplus velocity estimate                  # 创建 AI-native 估时
aiplus velocity complete                  # 记录实际完成时间
aiplus velocity bias --task <id>          # 检查任务 bias
aiplus velocity report                    # 显示 bias 和调整报告
aiplus velocity doctor                    # 运行 velocity 健康检查
aiplus velocity purge --yes               # 清理旧记录
```

## 存储

所有数据以本地 JSONL 存在 `.aiplus/velocity/`：

- `estimates.jsonl` — 估时记录
- `runs.jsonl` — 完成记录
- `rare-cases.jsonl` — Rare case 记录
- `config.json` — 配置
- `multipliers.json` — 聚合调整

无 SQLite。无云端。无 telemetry。

## 安全边界

AiPlus Agent Velocity 不：
- 存储 raw prompts 或 transcripts
- 上传数据或实现 telemetry
- 替代 tests、review 或 Owner gates
- 作为 productivity tracker 或 KPI 系统

## 路线图

见 [主 AiPlus 仓库](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md) 了解当前缺口。

## License

[Apache-2.0](LICENSE)

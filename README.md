# GSD - Get Shit Done for Trae

> 将 GSD (Get Shit Done) 方法论移植到 Trae 的完整开发工作流系统

## 概述

GSD 是 [Get Shit Done](https://github.com/gsd-build/get-shit-done) 在 Trae 平台上的原生移植实现。GSD 是一套经过验证的 AI 辅助软件开发方法论，本项目将其核心能力移植到 Trae 的 Skills 系统中。

## 核心特性

- 🧠 **自主判断** - 分析项目状态，自主决定调用哪个子 Skill
- 🔄 **完整闭环**：规划 → 执行 → 验证 → 调试
- 🎯 **目标导向**：目标回溯验证，确保交付价值
- 📋 **结构化流程**：基于阶段的工作流管理
- 🧠 **持久状态**：跨会话的状态管理
- 🔧 **工具适配**：完整映射 GSD 工具到 Trae

## 架构

```
gsd/
├── SKILL.md                          # 主入口
├── gsd-orchestrator/                 # 主控 - 自主判断和流程协调
├── gsd-planner/                      # 规划 - 任务分解
├── gsd-executor/                     # 执行 - 任务执行
├── gsd-verifier/                     # 验证 - 目标验证
├── gsd-debugger/                     # 调试 - 问题诊断
├── gsd-researcher/                   # 研究 - 技术调研
├── gsd-plan-checker/                 # 检查 - 计划检查
├── gsd-roadmap/                      # 路线图 - 里程碑规划
├── gsd-codebase-mapper/              # 代码库映射 - 结构分析
├── gsd-integration-checker/          # 集成检查 - 跨阶段验证
├── gsd-progress/                     # 进度查看 - 状态展示
├── gsd-pause/                        # 暂停保存 - 创建交接文件
├── gsd-resume/                       # 恢复工作 - 加载上下文
└── gsd-context/                      # 上下文 - 状态管理
```

## 核心工作流

```
用户输入 → 新项目研究 → 路线图创建
                ↓
           阶段规划 → 计划检查 → 执行
                ↓              ↓
           验证 ← ← ← ← ← ← ← ←
                ↓
           调试 (如有问题)
```

## 使用方法

### 自然语言触发（推荐）

```
用户: "我想创建一个新项目"
GSD: → 自动触发 gsd-orchestrator → gsd-researcher → gsd-roadmap

用户: "规划下一个阶段"
GSD: → 自动触发 gsd-orchestrator → gsd-planner → gsd-plan-checker

用户: "执行"
GSD: → 自动触发 gsd-orchestrator → gsd-executor → gsd-verifier

用户: "验证一下"
GSD: → 自动触发 gsd-orchestrator → gsd-verifier

用户: "我遇到问题了"
GSD: → 自动触发 gsd-orchestrator → gsd-debugger

用户: "进度怎么样"
GSD: → 自动触发 gsd-orchestrator → gsd-progress
```

### 显式命令（备选）

也可以显式调用：
- `@gsd-orchestrator new-project`
- `@gsd-orchestrator plan-phase --phase 3`
- `@gsd-orchestrator execute-phase --phase 3`

## 文件结构

GSD 使用 `.planning/` 目录管理项目状态（保留原版 GSD 结构）：

```
.planning/
├── config.json
├── STATE.md
├── ROADMAP.md
├── PROJECT.md
├── REQUIREMENTS.md
├── phases/
│   ├── 01-init/
│   │   ├── 01-PLAN.md
│   │   ├── 01-SUMMARY.md
│   │   └── ...
│   └── ...
└── milestones/
    └── ...
```

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |
| Edit | SearchReplace |
| Bash | RunCommand |
| Glob | Glob |
| Grep | Grep |
| WebSearch | WebSearch |
| WebFetch | WebFetch |
| Task | Task |

## 与原版 GSD 的差异

| 方面 | GSD | 本移植 |
|-----|-----|--------|
| 运行平台 | Claude Code CLI | Trae |
| Agent 实现 | Claude Code Agents | Trae Skills |
| 命令系统 | 斜杠命令 | 自然语言触发 |
| 触发方式 | 明确命令 | 自主判断意图 |

## 最佳实践

1. **使用 Task 工具** - 调用子技能
2. **保持 .planning/ 目录** - 存储阶段状态
3. **保持小闭环** - 每阶段 2-3 个任务
4. **验证驱动** - 每个任务有明确验证标准
5. **记录偏差** - 透明记录所有偏差处理

## 参考

- [GSD 原版项目](https://github.com/gsd-build/get-shit-done)
- [SZD 移植范例](../szd/) - Suzent 平台上的 GSD 移植

## 许可证

本项目基于 GSD 方法论移植，遵循相同的开源精神。

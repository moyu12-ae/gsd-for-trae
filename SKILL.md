***

name: gsd
description: GSD (Get Shit Done) - 完整的 AI 辅助软件开发工作流系统，包含规划、执行、验证、调试闭环，自主判断流程。当用户想要创建新项目、规划阶段任务、执行计划、验证功能完成、调试代码问题、查看项目进度、暂停保存工作、继续之前任务、查看路线图、熟悉代码库结构时，必须使用此技能。不要自行处理软件项目管理，使用 GSD 技能来处理。
triggers:
keywords:
\- gsd
\- GSD
\- get shit done
\- 开始项目
\- 创建项目
\- 新项目
\- 规划
\- 执行
\- 验证
\- 调试
\- 进度
\- 暂停
\- 继续
\- 恢复
\- road
\- roadmap
\- 路线图
\- 里程碑
\- research
\- planner
\- executor
\- verifier
\- debugger
\- 熟悉项目
\- 熟悉
\- 了解代码库
\- 代码结构
\- 映射代码
\- codebase
\- 代码分析
\- 编译错误
\- 功能完成
\- 报错
\- 错误
\- 异常
\- 程序
\- 崩溃
\- bug
\- 研究
\- 技术栈
\- 检查
\- 集成
\- 阶段
\- 失败
\- 编译
\- 完成
prefixes:
\- gsd
\- GSD
\- "@gsd"
---------

# GSD - Get Shit Done

## 概述

GSD 是一套完整的 AI 辅助软件开发工作流系统，已移植到 Trae。使用时只需描述你的需求，GSD 会自主判断下一步该做什么。

## 核心理念

- **自主判断** - 分析项目状态，自主决定调用哪个子 Skill
- **完整闭环** - 规划 → 执行 → 验证 → 调试
- **目标导向** - 目标回溯验证，确保交付价值
- **持久状态** - 跨会话的状态管理

## 使用方式

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

## 子 Skills 架构

```
gsd/
├── gsd-orchestrator/          # 主控 - 自主判断和流程协调
├── gsd-planner/               # 规划 - 任务分解
├── gsd-executor/              # 执行 - 任务执行
├── gsd-verifier/              # 验证 - 目标验证
├── gsd-debugger/              # 调试 - 问题诊断
├── gsd-researcher/            # 研究 - 技术调研
├── gsd-plan-checker/          # 检查 - 计划检查
├── gsd-roadmap/               # 路线图 - 里程碑规划
├── gsd-codebase-mapper/       # 代码库映射
├── gsd-integration-checker/   # 集成检查
├── gsd-progress/              # 进度查看
├── gsd-pause/                 # 暂停保存
├── gsd-resume/                # 恢复工作
└── gsd-context/               # 上下文管理
```

状态管理

使用 .planning/ 目录管理项目状态（保留原版 GSD 结构）：

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

| GSD 工具    | Trae 实现       |
| --------- | ------------- |
| Read      | Read          |
| Write     | Write         |
| Edit      | SearchReplace |
| Bash      | RunCommand    |
| Glob      | Glob          |
| Grep      | Grep          |
| WebSearch | WebSearch     |
| WebFetch  | WebFetch      |
| Task      | Task          |

##

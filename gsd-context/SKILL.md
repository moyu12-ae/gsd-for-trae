---
name: gsd-context
description: 管理 GSD 工作流的上下文和状态，包括用户决策、阶段状态、会话日志和交接文件。
---

# GSD Context - 上下文管理技能

## 概述

GSD Context 负责管理 GSD 工作流的上下文和状态，确保跨阶段和会话的信息持久化。它是所有 GSD skills 的状态管理中心。

## 核心职责

1. **状态持久化** - 存储和检索工作流状态
2. **决策管理** - 记录和管理用户决策
3. **上下文传递** - 在阶段间传递上下文
4. **历史追踪** - 追踪工作流历史
5. **会话日志** - 记录每次关键操作
6. **交接管理** - 创建和加载暂停交接文件

## 文件结构

```
.planning/
├── STATE.md           # 项目状态
├── CONTEXT.md         # 上下文和决策
├── SESSION.md         # 会话日志
├── ROADMAP.md         # 路线图
├── PLAN.md            # 当前计划
├── SUMMARY.md         # 执行总结
├── VERIFICATION.md    # 验证报告
├── DEBUG.md           # 调试记录
├── handoff-*.md       # 暂停交接文件
└── research/
    ├── STACK.md
    ├── FEATURES.md
    ├── ARCHITECTURE.md
    ├── PITFALLS.md
    └── SUMMARY.md
```

## 状态文件模板

### STATE.md

```markdown
---
project: [项目名称]
current_phase: P2
current_status: executing | paused | completed
last_updated: [日期时间]
---

# Project State

## Current Phase
- **Phase:** P2
- **Status:** executing
- **Started:** [日期]

## Completed Phases
- P1: ✓ VERIFIED

## Active Tasks
| Task | Status | Started | Notes |
|------|--------|---------|-------|
| T1 | in_progress | [日期] | [备注] |
| T2 | pending | - | - |

## Blockers
[如果有阻塞项列出，否则留空]

## Recent Actions
1. [日期] [动作描述]
2. [日期] [动作描述]
```

### CONTEXT.md

```markdown
---
project: [项目名称]
created: [日期]
---

# Project Context

## Project Description
[项目描述]

## Decisions (锁定)
用户已做出的决策，必须严格遵守：

### 技术决策
- **框架:** [选择] - [理由]
- **语言:** [选择] - [理由]

### 功能决策
- **布局风格:** [选择] - [理由]

## Deferred Ideas (延期)
用户明确延期的想法，不应出现在当前计划中：

- [功能]: [延期原因] (v2)

## Claude's Discretion
Claude 可以自行决定的事项：

- 代码风格
- 测试框架选择

## Constraints
- [约束 1]
- [约束 2]
```

### SESSION.md

```markdown
---
project: [项目名称]
session_start: [日期时间]
last_updated: [日期时间]
---

# Session Log

## [2024-03-05 14:30] Plan Created
**Phase:** P2 - Authentication
**Action:** 创建执行计划
**Plan File:** .planning/PLAN.md
**Tasks:** T1, T2, T3
**Next:** 执行 T1

## [2024-03-05 14:45] Task Completed
**Phase:** P2 - Authentication
**Task:** T1 - JWT Implementation
**Status:** completed
**Files:** auth/jwt.ts, auth/middleware.ts
**Decision:** 使用 RS256 算法
**Next:** 执行 T2

## [2024-03-05 15:00] Session Paused
**Phase:** P2 - Authentication
**Task:** T2 - Token Refresh
**Progress:** 60%
**Handoff:** .planning/handoff-2024-03-05-1500.md
**Next:** 恢复后继续 T2
```

### handoff-{timestamp}.md

```markdown
---
created: [时间戳]
phase: P2
task: T2
status: in_progress
progress: 60%
---

# Handoff - 工作交接

## 当前位置
**Phase:** P2 - Authentication
**Task:** T2 - Token Refresh
**Progress:** 60% 完成

## 已完成
- [x] JWT 基础实现
- [x] Token 验证中间件
- [x] Refresh token 生成

## 进行中
- [ ] Token rotation 逻辑 - 60%
  - 已完成: 生成逻辑
  - 剩余: 存储和清理

## 待完成
- [ ] Token rotation 逻辑 (剩余 40%)
- [ ] 单元测试
- [ ] 集成测试

## 决策记录
- **算法选择:** RS256，因为更安全
- **存储方案:** Redis，因为需要快速访问

## 遇到的问题
[如有问题记录，否则留空]

## 下一步操作
恢复后首先执行:
1. 完成 token rotation 的存储逻辑
2. 添加自动清理过期 token 的定时任务

## 恢复命令
说 "继续" 或 "resume" 恢复工作
```

## 核心操作

### 1. save_session_snapshot（保存会话快照）

**触发时机：**
- 计划创建后（由 gsd-planner 调用）
- 任务完成后（由 gsd-executor 调用）
- 验证通过后（由 gsd-verifier 调用）
- 遇到阻塞时（由 gsd-debugger 调用）
- 用户请求保存时（由 gsd-pause 调用）

**执行流程：**
```
1. 读取当前 STATE.md
2. 提取关键信息：
   - 当前阶段/任务
   - 操作类型（plan_created/task_completed/verified/blocked）
   - 最新决策
   - 修改的文件
   - 遇到的问题
3. 追加到 SESSION.md
```

### 2. create_handoff（创建交接文件）

**触发时机：**
- 用户说 "暂停" / "保存进度" / "我要离开"
- 由 gsd-pause 调用

**执行流程：**
```
1. 收集当前状态（STATE.md, CONTEXT.md, 当前工作内容）
2. 生成交接文件 .planning/handoff-{timestamp}.md
3. 更新 STATE.md 的 status 为 paused
```

### 3. load_session（加载会话）

**触发时机：**
- 用户说 "继续" / "恢复" / "resume"
- 由 gsd-resume 调用

**执行流程：**
```
1. 检查恢复点（优先级）：
   - .planning/handoff-*.md（最新的交接文件）
   - .planning/SESSION.md（最后一条记录）
   - .planning/STATE.md
2. 读取并解析
3. 组装恢复信息
4. 返回给调用者
```

### 4. update_state（更新状态）

**执行流程：**
```
1. 读取当前 STATE.md
2. 更新指定字段
3. 更新 last_updated 时间戳
4. 写入更新后的 STATE.md
```

### 5. record_decision（记录决策）

**执行流程：**
```
1. 读取 CONTEXT.md
2. 在 Decisions 部分添加决策
3. 写入更新后的 CONTEXT.md
```

## 工具映射

| 操作 | Trae 工具 |
|------|----------|
| 读取文件 | Read |
| 写入文件 | Write |
| 编辑文件 | SearchReplace |

## 最佳实践

1. **状态及时更新** - 每次操作后更新
2. **决策明确记录** - 避免歧义，包含理由
3. **会话日志追加** - 不删除历史记录
4. **交接文件完整** - 包含足够信息恢复工作

## 与其他 Skills 的关系

- **gsd-planner** - 计划创建后调用 save_session_snapshot
- **gsd-executor** - 任务完成后调用 save_session_snapshot
- **gsd-verifier** - 验证后调用 save_session_snapshot
- **gsd-pause** - 调用 create_handoff
- **gsd-resume** - 调用 load_session
- **gsd-progress** - 读取 STATE.md 和 SESSION.md

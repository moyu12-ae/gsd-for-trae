---
name: gsd-pause
description: 暂停当前工作，保存会话状态，创建交接文件以便后续恢复。
triggers:
  keywords:
    - 暂停
    - 保存
    - 歇一会
    - pause
  prefixes:
    - "@gsd-pause"
---

# GSD Pause - 暂停保存技能

## 概述

GSD Pause 负责暂停当前工作，保存会话状态，创建交接文件以便后续恢复。

## 核心职责

1. **状态保存** - 保存当前工作状态
2. **交接文件创建** - 创建详细的交接文件
3. **状态更新** - 更新 STATE.md 为 paused
4. **会话记录** - 追加到 SESSION.md

## 工作流程

### Step 1: 收集当前状态

```
使用 Read 工具读取:
- .planning/STATE.md (当前状态)
- .planning/CONTEXT.md (上下文)
- 当前工作文件 (PLAN.md、SUMMARY.md 等)
- 最近的代码变更
```

### Step 2: 创建交接文件

生成 `.planning/handoff-{timestamp}.md`：

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

### Step 3: 更新状态

更新 `.planning/STATE.md`：

```markdown
**Status:** paused
**Paused At:** [时间戳]
**Resume File:** .planning/handoff-{timestamp}.md
```

### Step 4: 记录会话

追加到 `.planning/SESSION.md`：

```markdown
## [2024-03-05 15:00] Session Paused
**Phase:** P2 - Authentication
**Task:** T2 - Token Refresh
**Progress:** 60%
**Handoff:** .planning/handoff-2024-03-05-1500.md
**Next:** 恢复后继续 T2
```

### Step 5: 确认暂停

向用户确认：

```
## ⏸ 工作已暂停

**项目:** [项目名称]
**阶段:** P2 - Authentication
**任务:** T2 - Token Refresh
**进度:** 60% 完成

**交接文件:** .planning/handoff-2024-03-05-1500.md

### 恢复工作
说 "继续" 或 "resume" 即可恢复工作。
```

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |

## 最佳实践

1. **详细记录** - 交接文件要足够详细
2. **上下文完整** - 包含所有决策和理由
3. **下一步明确** - 明确恢复后的操作
4. **状态更新** - 确保所有状态文件都更新

## 与其他 Skills 的关系

- **输入**: 当前工作状态
- **输出**: 交接文件
- **状态**: gsd-context (保存暂停状态)
- **配对**: gsd-resume 用于恢复工作

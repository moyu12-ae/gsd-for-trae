---
name: gsd-resume
description: 恢复之前暂停的工作，加载交接文件，恢复会话状态，继续执行。
triggers:
  keywords:
    - 继续
    - 恢复
    - resume
    - 重新开始
  prefixes:
    - "@gsd-resume"
---

# GSD Resume - 恢复工作技能

## 概述

GSD Resume 负责恢复之前暂停的工作，加载交接文件，恢复会话状态，继续执行。

## 核心职责

1. **恢复点检测** - 查找最新的交接文件
2. **状态加载** - 加载交接文件和状态
3. **上下文恢复** - 恢复项目上下文
4. **继续执行** - 从暂停点继续工作

## 工作流程

### Step 1: 检测恢复点

按优先级检查恢复点：

```
1. .planning/handoff-*.md（最新的交接文件）
2. .planning/SESSION.md（最后一条记录）
3. .planning/STATE.md
```

使用 Glob 工具查找交接文件：

```
Glob pattern: .planning/handoff-*.md
```

### Step 2: 加载交接文件

读取最新的交接文件：

```markdown
---
created: 2024-03-05T15:00:00Z
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

## 下一步操作
恢复后首先执行:
1. 完成 token rotation 的存储逻辑
2. 添加自动清理过期 token 的定时任务
```

### Step 3: 恢复状态

更新 `.planning/STATE.md`：

```markdown
**Status:** executing (resumed)
**Resumed From:** .planning/handoff-2024-03-05-1500.md
**Last Activity:** [当前时间]
```

### Step 4: 记录恢复

追加到 `.planning/SESSION.md`：

```markdown
## [2024-03-06 09:00] Session Resumed
**Phase:** P2 - Authentication
**Task:** T2 - Token Refresh
**Resumed From:** .planning/handoff-2024-03-05-1500.md
**Previous Progress:** 60%
**Next:** 继续执行 T2 的剩余工作
```

### Step 5: 呈现恢复信息

向用户呈现恢复信息：

```
## ▶ 工作已恢复

**项目:** [项目名称]
**阶段:** P2 - Authentication
**任务:** T2 - Token Refresh
**之前进度:** 60% 完成

### 已完成
- ✓ JWT 基础实现
- ✓ Token 验证中间件
- ✓ Refresh token 生成

### 进行中
- ◐ Token rotation 逻辑 - 60%
  - 已完成: 生成逻辑
  - 剩余: 存储和清理

### 待完成
- ◯ Token rotation 逻辑 (剩余 40%)
- ◯ 单元测试
- ◯ 集成测试

### 决策记录
- **算法选择:** RS256，因为更安全
- **存储方案:** Redis，因为需要快速访问

### 下一步
恢复后首先执行:
1. 完成 token rotation 的存储逻辑
2. 添加自动清理过期 token 的定时任务

---

**准备好继续了吗？** 说 "继续" 或直接描述你想做什么。
```

### Step 5: 返回用户确认

向用户返回恢复信息：

```
## ▶️ 恢复工作

**项目:** [项目名称]
**阶段:** [阶段名]
**任务:** [任务名]
**进度:** [进度百分比]

**已完成:**
- [x] 任务 1
- [x] 任务 2

**进行中:**
- [ ] 任务 3 - [进度]

**待完成:**
- [ ] 任务 4
- [ ] 任务 5

### 下一步操作
恢复后首先执行:
1. [操作 1]
2. [操作 2]
```

### Step 6: 确认后执行（必须使用 AskUserQuestion）

**⚠️ 重要：必须先询问用户确认后才能执行**

使用 AskUserQuestion 确认是否继续执行：

```markdown
AskUserQuestion(
  questions: [
    {
      header: "继续",
      question: "当前任务进度为 [X]%，是否继续执行？",
      options: [
        {
          label: "继续执行",
          description: "从暂停点继续执行当前任务"
        },
        {
          label: "换个任务",
          description: "不执行当前任务，改为执行其他任务"
        },
        {
          label: "先验证",
          description: "先验证已完成的工作，再继续"
        }
      ],
      multiSelect: false
    }
  ]
)
```

**根据用户选择：**
- "继续执行"：调用 gsd-executor 继续执行
- "换个任务"：返回用户，让用户指定要执行的任务
- "先验证"：调用 gsd-verifier 验证已完成的工作

### Step 7: 执行用户选择的任务

根据用户的确认，选择执行相应的操作：

**如果选择 "继续执行"：**
```markdown
Task(
  subagent_type="gsd-executor",
  query="继续执行 P2 的 T2 任务，从暂停点继续"
)
```

**如果选择 "换个任务"：**
返回用户，等待用户指定要执行的任务。

**如果选择 "先验证"：**
```markdown
Task(
  subagent_type="gsd-verifier",
  query="验证当前阶段的执行结果"
)
```

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |
| Glob | Glob |

## 最佳实践

1. **多恢复点** - 检查多个可能的恢复点
2. **完整呈现** - 向用户展示完整的恢复信息
3. **状态同步** - 确保所有状态文件都更新
4. **灵活继续** - 让用户决定如何继续

## 与其他 Skills 的关系

- **输入**: gsd-pause 创建的交接文件
- **输出**: 恢复的工作状态
- **状态**: gsd-context (恢复会话状态)
- **配对**: gsd-pause 用于暂停工作
- **后续**: 恢复后可调用 gsd-executor 继续执行

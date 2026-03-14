---
name: gsd-progress
description: 查看和报告 GSD 项目进度，包括当前状态、已完成工作、剩余工作和下一步建议。
triggers:
  keywords:
    - 进度
    - progress
    - 怎么样
    - 第几阶段
    - 状态
  prefixes:
    - "@gsd-progress"
---

# GSD Progress - 进度查看技能

## 概述

GSD Progress 负责查看和报告 GSD 项目进度，包括当前状态、已完成工作、剩余工作和下一步建议。

## 核心职责

1. **状态读取** - 读取 STATE.md、ROADMAP.md、SESSION.md
2. **进度分析** - 分析已完成和剩余工作
3. **报告生成** - 生成进度报告
4. **建议提供** - 提供下一步操作建议

## 工作流程

### Step 1: 读取状态文件

```
使用 Read 工具读取:
- .planning/STATE.md (当前状态)
- .planning/ROADMAP.md (路线图)
- .planning/SESSION.md (会话日志)
- .planning/phases/*/ (阶段文件)
```

### Step 2: 分析进度

计算和分析：

```yaml
progress:
  current_phase: "P2"
  current_phase_name: "核心功能"
  status: "executing"
  completed_phases: 1
  total_phases: 5
  completed_plans: 3
  total_plans: 10
  percent_complete: 30
  last_activity: "2024-03-05 15:00"
```

### Step 3: 生成进度报告

```markdown
# GSD 项目进度报告

## 当前状态
- **项目:** [项目名称]
- **当前阶段:** P2 - 核心功能
- **状态:** executing
- **最后活动:** 2024-03-05 15:00

## 整体进度
- **已完成阶段:** 1/5 (20%)
- **已完成计划:** 3/10 (30%)
- **进度条:** [████░░░░░░] 30%

## 阶段详情

### P1: 项目基础设施 ✓
- **状态:** VERIFIED
- **完成日期:** 2024-03-04
- **计划:** 2/2
- **验证:** ✓ 通过

### P2: 核心功能 (当前)
- **状态:** executing
- **当前计划:** 1/3
- **进度:** 33%

### P3-P5: 待执行
- **状态:** pending

## 最近活动
1. 2024-03-05 15:00 - 执行 T2: 用户认证
2. 2024-03-05 14:30 - 完成 T1: 数据库设置
3. 2024-03-04 16:00 - 验证 P1 完成

## 下一步建议
- 继续执行 P2 的剩余计划
- 或运行 `/gsd:execute-phase 2` 继续
```

### Step 4: 提供下一步建议

基于当前状态提供建议：

| 当前状态 | 建议操作 |
|---------|---------|
| 无项目 | 创建新项目 - `/gsd:new-project` |
| 无 ROADMAP | 创建路线图 - `/gsd:roadmap` |
| 有 ROADMAP 无 PLAN | 规划阶段 - `/gsd:plan-phase [N]` |
| 有 PLAN 无 SUMMARY | 执行阶段 - `/gsd:execute-phase [N]` |
| 有 SUMMARY 无 VERIFICATION | 验证阶段 - `/gsd:verify [N]` |
| 阶段完成 | 过渡到下一阶段 - `/gsd:transition` |

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Glob | Glob |

## 最佳实践

1. **全面扫描** - 读取所有相关文件
2. **清晰展示** - 使用进度条和表格
3. **具体建议** - 提供可执行的下一步
4. **历史追踪** - 显示最近活动

## 与其他 Skills 的关系

- **输入**: gsd-context 管理的状态文件
- **输出**: 进度报告
- **触发**: 用户说"进度怎么样"、"progress"等

# GSD Summary 模板

```yaml
---
phase: XX-name
plan: YY
subsystem: [主要类别: auth, payments, ui, api, database, infra, testing]
tags: [可搜索技术: jwt, stripe, react, postgres, prisma]

# 依赖图
requires:
  - phase: [依赖的阶段]
    provides: [该阶段提供的功能]
provides:
  - [此阶段提供的功能列表]
affects: [需要此上下文的阶段列表]

# 技术追踪
tech-stack:
  added: [添加的库/工具]
  patterns: [建立的架构/代码模式]

key-files:
  created: [创建的重要文件]
  modified: [修改的重要文件]

key-decisions:
  - "决策 1"
  - "决策 2"

patterns-established:
  - "模式 1: 描述"
  - "模式 2: 描述"

requirements-completed: []  # 从计划的 requirements 字段复制

# 指标
duration: Xmin
completed: YYYY-MM-DD
---
```

# Phase [X]: [Name] Summary

**[实质性一行描述 - 非 "阶段完成" 或 "实现完成"]**

## Performance

- **Duration:** [时间] (如 23 min, 1h 15m)
- **Started:** [ISO 时间戳]
- **Completed:** [ISO 时间戳]
- **Tasks:** [完成的任务数]
- **Files modified:** [文件数]

## Accomplishments
- [最重要的成果]
- [第二重要的成果]
- [第三，如适用]

## Task Commits

每个任务原子提交:

1. **Task 1: [任务名称]** - `abc123f` (feat/fix/test/refactor)
2. **Task 2: [任务名称]** - `def456g` (feat/fix/test/refactor)
3. **Task 3: [任务名称]** - `hij789k` (feat/fix/test/refactor)

**Plan metadata:** `lmn012o` (docs: 完整计划)

## Files Created/Modified
- `path/to/file.ts` - 功能描述
- `path/to/another.ts` - 功能描述

## Decisions Made
[关键决策及简要理由，或 "None - 按计划执行"]

## Deviations from Plan

[如无偏差: "None - 计划完全按预期执行"]

[如有偏差:]

### Auto-fixed Issues

**1. [Rule X - Category] 简要描述**
- **Found during:** Task [N] ([任务名称])
- **Issue:** [问题]
- **Fix:** [解决方案]
- **Files modified:** [文件路径]
- **Verification:** [如何验证]
- **Committed in:** [哈希]

---

**Total deviations:** [N] 自动修复 ([按规则分类])

## Issues Encountered
[问题及解决方式，或 "None"]

## User Setup Required

[如生成了 USER-SETUP.md:]
**外部服务需要手动配置。** 参见 [{phase}-USER-SETUP.md](./{phase}-USER-SETUP.md)

[如无 USER-SETUP.md:]
None - 无需外部服务配置

## Next Phase Readiness
[为下一阶段准备好的内容]
[任何阻塞或关注点]

---
*Phase: XX-name*
*Completed: [日期]*
```

## 一行描述规则

**好的示例:**
- "JWT 认证带刷新令牌轮换，使用 jose 库"
- "Prisma schema 包含 User, Session 和 Product 模型"
- "仪表板带实时指标通过 Server-Sent Events"

**不好的示例:**
- "阶段完成"
- "认证已实现"
- "基础已完成"

一行描述应该告诉别人实际交付了什么。

# GSD 模板文件

## PLAN.md 模板

```markdown
---
phase: XX-name
plan: NN
type: execute
wave: N
depends_on: []
files_modified: []
autonomous: true
requirements: []
user_setup: []
must_haves:
  truths: []
  artifacts: []
  key_links: []
---

&lt;objective&gt;
[本计划要达成的目标]

Purpose: [为什么重要]
Output: [创建的产物]
&lt;/objective&gt;

&lt;execution_context&gt;
[执行上下文引用]
&lt;/execution_context&gt;

&lt;context&gt;
[相关上下文文件引用]
&lt;/context&gt;

&lt;tasks&gt;

&lt;task type="auto"&gt;
  &lt;name&gt;Task 1: [动作导向名称]&lt;/name&gt;
  &lt;files&gt;path/to/file.ext&lt;/files&gt;
  &lt;action&gt;[具体实现说明]&lt;/action&gt;
  &lt;verify&gt;[验证命令或检查方法]&lt;/verify&gt;
  &lt;done&gt;[完成标准]&lt;/done&gt;
&lt;/task&gt;

&lt;task type="auto"&gt;
  &lt;name&gt;Task 2: [动作导向名称]&lt;/name&gt;
  &lt;files&gt;path/to/file.ext&lt;/files&gt;
  &lt;action&gt;[具体实现说明]&lt;/action&gt;
  &lt;verify&gt;[验证命令或检查方法]&lt;/verify&gt;
  &lt;done&gt;[完成标准]&lt;/done&gt;
&lt;/task&gt;

&lt;/tasks&gt;

&lt;verification&gt;
[整体阶段验证标准]
&lt;/verification&gt;

&lt;success_criteria&gt;
[可衡量的成功标准]
&lt;/success_criteria&gt;

&lt;output&gt;
完成后创建 .planning/phases/XX-name/XX-NN-SUMMARY.md
&lt;/output&gt;
```

## SUMMARY.md 模板

```markdown
---
phase: XX-name
plan: NN
status: complete|partial|blocked
completed_tasks: [1, 2, 3]
commits: []
deviations: []
verified_at: YYYY-MM-DDTHH:MM:SSZ
---

## Summary

[执行摘要]

## Completed Tasks

### Task 1: [任务名称]

**状态:** 完成 ✓

**变更:**
- [变更 1]
- [变更 2]

**文件修改:**
- path/to/file1.ext
- path/to/file2.ext

### Task 2: [任务名称]

[同上格式]

## Deviations

[记录的偏差，如无则写"无"]

## Issues Encountered

[遇到的问题及解决方案]

## Next Steps

[建议的下一步操作]
```

## VERIFICATION.md 模板

```markdown
---
phase: XX-name
status: passed|failed|partial
verified_at: YYYY-MM-DDTHH:MM:SSZ
---

## Verification Summary

[验证摘要]

## Must-Haves Status

### Truths (可观察真理)

| Truth | Status | Evidence |
|-------|--------|----------|
| 用户可以看到现有消息 | ✓ VERIFIED | Chat.tsx 渲染 Message[] |
| 用户可以发送消息 | ✓ VERIFIED | onSubmit 调用 POST /api/chat |
| 消息持久化 | ✗ FAILED | 数据库连接失败 |

### Artifacts (产物验证)

| Artifact | Exists | Substantive | Wired | Status |
|----------|--------|-------------|-------|--------|
| src/components/Chat.tsx | ✓ | ✓ | ✓ | ✓ |
| src/app/api/chat/route.ts | ✓ | ✓ | ✗ | ✗ |
| prisma/schema.prisma | ✓ | ✓ | - | ✓ |

### Key Links (关键连接)

| Link | Status | Evidence |
|------|--------|----------|
| Chat.tsx → /api/chat | ✓ | fetch in useEffect |
| route.ts → prisma.message | ✗ | 连接字符串错误 |

## Gaps

### Gap 1: 数据库连接失败

**描述:** API 路由无法连接数据库

**影响:** 消息无法持久化

**建议修复:** 检查 DATABASE_URL 环境变量

## Recommendations

1. [修复建议 1]
2. [修复建议 2]

## Verdict

- [ ] PASS - 阶段目标达成
- [ ] PARTIAL - 部分达成，需要修复 Gaps
- [ ] FAIL - 需要重新规划
```

## ROADMAP.md 模板

```markdown
---
project: [项目名称]
version: 1.0.0
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
---

# Project Roadmap

## Vision

[项目愿景描述]

## Goals

1. [主要目标 1]
2. [主要目标 2]
3. [主要目标 3]

## Phases

### Phase 1: [阶段名称]

**Goal:** [阶段目标]

**Status:** not_started | in_progress | completed | blocked

**Duration:** [预估时间]

**Requirements:**
- REQ-1: [需求描述]
- REQ-2: [需求描述]

**Success Criteria:**
- [ ] [成功标准 1]
- [ ] [成功标准 2]

**Dependencies:** None

---

### Phase 2: [阶段名称]

**Goal:** [阶段目标]

**Status:** not_started

**Requirements:**
- REQ-3: [需求描述]

**Success Criteria:**
- [ ] [成功标准 1]

**Dependencies:** Phase 1

---

## Milestones

| Milestone | Target Date | Status | Phase |
|-----------|-------------|--------|-------|
| MVP 完成 | YYYY-MM-DD | pending | Phase 1 |
| Beta 发布 | YYYY-MM-DD | pending | Phase 2 |

## Risks

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| [风险描述] | High/Medium/Low | High/Medium/Low | [缓解策略] |

## Notes

[其他备注]
```

## STATE.md 模板

```markdown
---
updated_at: YYYY-MM-DDTHH:MM:SSZ
---

## Current Status

**Phase:** 01-foundation
**Plan:** 01
**Task:** Task 2

## Position

- Last completed: Task 1 - Create user model
- Next to do: Task 2 - Create user API

## Progress

### Phase 1: Foundation
- [x] Plan 01 - Setup (completed)
- [ ] Plan 02 - Core models (in progress)

## Blockers

[当前阻塞项，如无则写"无"]

## Recent Activity

| Date | Action | Result |
|------|--------|--------|
| YYYY-MM-DD | 完成用户模型 | 成功 |

## Notes

[备注]
```

## CONTEXT.md 模板

```markdown
---
project: [项目名称]
updated_at: YYYY-MM-DDTHH:MM:SSZ
---

## Project Overview

[项目概述]

## Decisions

### Locked Decisions (必须精确实现)

- **技术栈:** Next.js 14 + TypeScript + Prisma
  - Made: 2024-01-15
  - Rationale: 团队熟悉，生态成熟

- **数据库:** PostgreSQL
  - Made: 2024-01-15
  - Rationale: 需要 JSON 支持，已有运维经验

### Deferred Ideas (不得出现在计划中)

- 暗黑模式
- 国际化
- 移动端适配

### Claude's Discretion

- 代码组织结构
- 测试框架选择
- 命名约定

## Technical Context

### Tech Stack
- Framework: Next.js 14
- Language: TypeScript
- Database: PostgreSQL + Prisma
- Styling: Tailwind CSS

### Constraints
- 必须支持 Node 18+
- 必须兼容 Vercel 部署

### Preferences
- 函数式组件优先
- 使用 Server Components
- 添加错误边界

## Open Questions

- [ ] 是否需要 SSR？
- [ ] 缓存策略？

## History

| Date | Decision | Made By |
|------|----------|---------|
| 2024-01-15 | 选择 Next.js | user |
| 2024-01-16 | 添加 Tailwind | claude |
```

## DEBUG.md 模板

```markdown
---
session_id: debug-YYYYMMDD-HHMMSS
status: investigating|found|fixed|blocked
created_at: YYYY-MM-DDTHH:MM:SSZ
updated_at: YYYY-MM-DDTHH:MM:SSZ
---

## Problem Statement

[用户描述的问题]

## Symptoms

- [症状 1]
- [症状 2]

## Evidence

### Logs
```
[相关日志]
```

### Stack Trace
```
[堆栈跟踪]
```

### Code Context
```typescript
[相关代码]
```

## Hypotheses

### H1: [假设描述]
- Status: pending|testing|confirmed|rejected
- Prediction: [预测结果]
- Test: [测试方法]
- Result: [测试结果]
- Confidence: 0-100%

### H2: [假设描述]
- Status: pending
- Prediction: [预测结果]
- Test: [测试方法]
- Result: -
- Confidence: 0%

## Root Cause

[找到根因后填写]

## Fix

[修复方案]

## Verification

[修复后验证步骤]

## Lessons Learned

[经验教训]
```

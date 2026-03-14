---
name: gsd-integration-checker
description: 检查跨阶段集成，验证阶段间接口、API 覆盖率、数据流完整性。
triggers:
  keywords:
    - 集成
    - 检查
    - integration
    - 接口
  prefixes:
    - "@gsd-integration-checker"
---

# GSD Integration Checker - 集成检查技能

## 概述

GSD Integration Checker 负责检查跨阶段集成，验证阶段间接口、API 覆盖率、数据流完整性。

## 核心职责

1. **接口检查** - 检查阶段间接口
2. **API 覆盖验证** - 验证 API 覆盖率
3. **数据流验证** - 验证数据流完整性
4. **集成报告生成** - 生成集成检查报告

## 工作流程

### Step 1: 识别集成点

```
使用 Read 和 Grep 工具查找:
- 阶段间的导入/导出
- API 调用
- 数据库访问
- 事件/消息传递
- 文件 I/O
```

### Step 2: 接口检查

检查阶段间接口：

```yaml
interfaces:
  - from: P1
    to: P2
    interface: "Database Schema"
    status: ✓ VERIFIED
    details: "Schema matches P1's output"
  
  - from: P2
    to: P3
    interface: "API Contract"
    status: ✗ FAILED
    details: "Missing endpoints for P3"
```

### Step 3: API 覆盖验证

验证 API 覆盖率：

```yaml
api_coverage:
  total_endpoints: 10
  implemented: 8
  coverage: 80%
  missing:
    - "DELETE /api/users/:id"
    - "PATCH /api/posts/:id"
  deprecated:
    - "GET /api/legacy"
```

### Step 4: 数据流验证

验证数据流完整性：

```yaml
data_flow:
  user_signup:
    - [✓] Frontend → API
    - [✓] API → Database
    - [✓] Database → API
    - [✓] API → Frontend
    - status: ✓ VERIFIED
  
  payment_process:
    - [✓] Frontend → API
    - [✓] API → Payment Gateway
    - [✗] Payment Gateway → Webhook
    - status: ✗ FAILED
```

### Step 5: 生成集成报告

```markdown
---
phases: [P1, P2, P3]
status: pass|fail|partial
score: 75
---

# Integration Check Report

## Overview
**检查阶段:** P1, P2, P3
**整体状态:** Partial
**评分:** 75/100

## Interface Checks

| From | To | Interface | Status | Details |
|------|----|-----------|--------|---------|
| P1 | P2 | Database Schema | ✓ VERIFIED | Schema matches |
| P2 | P3 | API Contract | ✗ FAILED | Missing endpoints |

## API Coverage
- **Total:** 10 endpoints
- **Implemented:** 8 (80%)
- **Missing:**
  - DELETE /api/users/:id
  - PATCH /api/posts/:id

## Data Flow Checks

### User Signup ✓
- [✓] Frontend → API
- [✓] API → Database
- [✓] Database → API
- [✓] API → Frontend

### Payment Process ✗
- [✓] Frontend → API
- [✓] API → Payment Gateway
- [✗] Payment Gateway → Webhook

## Issues Found

### High Priority
1. Missing DELETE /api/users/:id endpoint
2. Payment webhook not implemented

### Medium Priority
1. Missing PATCH /api/posts/:id endpoint

## Recommendations
1. Implement missing endpoints
2. Fix payment webhook
3. Add integration tests

## Next Steps
- Fix high priority issues
- Re-run integration check
- Proceed to next phase (if all issues fixed)
```

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Grep | Grep |
| Glob | Glob |

## 最佳实践

1. **端到端检查** - 从输入到输出完整检查
2. **多维度验证** - 接口、API、数据流畅通检查
3. **优先级排序** - 高优先级问题先解决
4. **可测试** - 集成问题应该可以通过测试验证

## 与其他 Skills 的关系

- **输入**: 多个阶段的输出
- **输出**: 集成检查报告
- **时机**: 多个阶段完成后
- **后续**: 问题修复后重新运行

# GSD Verification Report 模板

```yaml
---
phase: XX-name
plan: YY
verifier: gsd-verifier
timestamp: YYYY-MM-DDTHH:MM:SS
overall_status: [PASS|FAIL|PARTIAL]
---

# Verification Report

## Phase: [Phase Name]

### Executive Summary
[简短概述验证结果，包括通过/失败的任务数和主要发现]

---

## 1. Task Verification

| Task | Status | Duration | Notes |
|------|--------|----------|-------|
| Task 1: [名称] | ✅ PASS | Xs | |
| Task 2: [名称] | ✅ PASS | Xs | |
| Task 3: [名称] | ❌ FAIL | Xs | [失败原因] |

**Pass Rate:** X/Y (XX%)

---

## 2. Level 1: Task Verification

### 2.1 Functional Verification
- [x] 任务目标达成
- [x] 验证命令通过
- [x] 无错误/警告引入
- [x] 代码风格一致

### 2.2 Code Quality
- [x] 无 TypeScript 错误
- [x] 无 ESLint 警告
- [x] 无硬编码敏感信息
- [x] 无存根/占位代码

---

## 3. Level 2: Phase Verification

### 3.1 Integration Tests
- [x] 功能端到端可用
- [x] 无回归问题
- [x] 所有测试通过

### 3.2 Build Verification
```bash
# 构建命令输出
npm run build
# 结果: SUCCESS/FAIL
```

---

## 4. Level 3: Goal Backward Verification

### 4.1 Must-Haves Check
| Must-Have | Status | Evidence |
|-----------|--------|----------|
| [来自 PLAN.md must_haves] | ✅ | [验证方式] |
| [来自 PLAN.md must_haves] | ✅ | [验证方式] |

### 4.2 Requirements Coverage
- [ ] REQ-001: [需求描述] - ✅ SATISFIED
- [ ] REQ-002: [需求描述] - ✅ SATISFIED
- [ ] REQ-003: [需求描述] - ❌ NOT SATISFIED

---

## 5. Issues Found

### 5.1 Critical Issues (必须修复)
1. **[Issue Title]**
   - Location: [文件:行号]
   - Description: [问题描述]
   - Suggested Fix: [建议修复方案]

### 5.2 Warning Issues (建议修复)
1. **[Issue Title]**
   - Location: [文件:行号]
   - Description: [问题描述]

### 5.3 Info Issues (可选优化)
1. **[Issue Title]**
   - Description: [优化建议]

---

## 6. Gaps (需要后续修复)

- [ ] **GAP-001**: [描述] - Priority: [High/Medium/Low]
- [ ] **GAP-002**: [描述] - Priority: [High/Medium/Low]

---

## 7. Recommendations

1. [建议1]
2. [建议2]
3. [建议3]

---

## 8. Final Verdict

- [x] **APPROVED** - 可以进入下一阶段
- [ ] **NEEDS_WORK** - 需要修复后重新验证
- [ ] **BLOCKED** - 有阻塞问题需要用户介入

### Blocking Issues (如有)
[列出阻塞问题]

### Sign-off
- **Verifier:** gsd-verifier
- **Timestamp:** YYYY-MM-DDTHH:MM:SS
- **Phase:** XX-name
```

## 验证状态说明

| 状态 | 含义 |
|------|------|
| APPROVED | 验证通过，可进入下一阶段 |
| NEEDS_WORK | 有问题需要修复，修复后重新验证 |
| BLOCKED | 有阻塞问题需要用户介入决策 |

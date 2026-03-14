# GSD Plan Checker 检查报告模板

```yaml
---
phase: XX-name
plan: YY
checker: gsd-plan-checker
timestamp: YYYY-MM-DDTHH:MM:SS
status: [PASS|NEEDS_REVISION|REJECTED]
---
```

# Plan Check Report

## Plan: [Phase-Plan ID]

### Executive Summary
[简短概述检查结果]

---

## 1. Structure Completeness

| Check | Status | Notes |
|-------|--------|-------|
| Frontmatter complete | ✅/❌ | |
| Objective defined | ✅/❌ | |
| Context references | ✅/❌ | |
| Tasks list | ✅/❌ | |
| Verification items | ✅/❌ | |
| Success criteria | ✅/❌ | |

**Score:** X/6

---

## 2. Consistency Check

| Check | Status | Notes |
|-------|--------|-------|
| Tasks align with objective | ✅/❌ | |
| No conflicting tasks | ✅/❌ | |
| Dependencies correct | ✅/❌ | |
| File references exist | ✅/❌ | |
| Verification commands valid | ✅/❌ | |

**Score:** X/5

---

## 3. Feasibility Check

| Check | Status | Notes |
|-------|--------|-------|
| Executable in current env | ✅/❌ | |
| Tools available | ✅/❌ | |
| Time estimate reasonable | ✅/❌ | |
| Resource needs defined | ✅/❌ | |

**Score:** X/4

---

## 4. Completeness Check

| Check | Status | Notes |
|-------|--------|-------|
| No missing steps | ✅/❌ | |
| Edge cases considered | ✅/❌ | |
| Error handling planned | ✅/❌ | |
| Test strategy defined | ✅/❌ | |

**Score:** X/4

---

## 5. Task Analysis

| Task | Size | Independent | Verifiable | Overall |
|------|------|-------------|------------|---------|
| Task 1 | ✅/⚠️/❌ | ✅/❌ | ✅/❌ | ✅/⚠️/❌ |
| Task 2 | ✅/⚠️/❌ | ✅/❌ | ✅/❌ | ✅/⚠️/❌ |

**Size Guide:**
- ✅ Good: 1-2 hours
- ⚠️ Warning: 2-4 hours
- ❌ Too large: &gt;4 hours

---

## 6. Issues Found

### 6.1 Errors (Must Fix)
1. **[Error Title]**
   - Location: [位置]
   - Issue: [问题描述]
   - Fix: [建议修复]

### 6.2 Warnings (Should Fix)
1. **[Warning Title]**
   - Location: [位置]
   - Issue: [问题描述]
   - Suggestion: [建议]

### 6.3 Suggestions (Optional)
1. **[Suggestion]**
   - Description: [描述]

---

## 7. Quality Score

| Dimension | Score | Weight | Weighted |
|-----------|-------|--------|----------|
| Structure | X/5 | 20% | X |
| Consistency | X/5 | 25% | X |
| Feasibility | X/5 | 25% | X |
| Completeness | X/5 | 30% | X |
| **Total** | **X/5** | **100%** | **X** |

---

## 8. Recommendations

1. [建议1]
2. [建议2]
3. [建议3]

---

## 9. Final Verdict

- [ ] **PASS** - 计划质量良好，可以执行
- [ ] **NEEDS_REVISION** - 需要修订后重新检查
- [ ] **REJECTED** - 计划不可行，需重新规划

### Blocking Issues (如有)
[列出阻塞问题]

### Checker Sign-off
- **Checker:** gsd-plan-checker
- **Timestamp:** YYYY-MM-DDTHH:MM:SS
- **Plan:** XX-name-YY

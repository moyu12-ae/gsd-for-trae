# GSD Debug 模板

```yaml
---
session_id: debug-YYYYMMDD-HHMMSS
created: YYYY-MM-DDTHH:MM:SS
status: ACTIVE
symptom: [症状简述]
root_cause: null
---
```

# Debug Session: [Session ID]

## 1. Initial Symptom

**用户报告:**
&gt; [用户原话描述问题]

**环境信息:**
- 操作系统: [OS]
- Node版本: [version]
- 项目版本: [commit hash]

**复现步骤:**
1. [步骤1]
2. [步骤2]
3. [步骤3]

**预期行为:** [应该发生什么]
**实际行为:** [实际发生了什么]

---

## 2. Investigation Log

### Hypothesis 1: [假设标题]

**假设:** [具体假设内容]

**验证方法:**
```bash
[验证命令或步骤]
```

**结果:**
- 状态: ✅ CONFIRMED / ❌ DISPROVED
- 发现: [验证过程中的发现]

**结论:** [假设是否成立，以及为什么]

---

### Hypothesis 2: [假设标题]

**假设:** [具体假设内容]

**验证方法:**
```bash
[验证命令或步骤]
```

**结果:**
- 状态: ✅ CONFIRMED / ❌ DISPROVED
- 发现: [验证过程中的发现]

**结论:** [假设是否成立，以及为什么]

---

## 3. Root Cause Analysis

**根因:** [最终确定的根本原因]

**问题代码位置:**
- 文件: `path/to/file.ts`
- 行号: XX-YY

**问题原因:**
[详细解释为什么会出现这个问题]

---

## 4. Solution

**修复方案:**
```diff
- // 原代码
+ // 修复后代码
```

**修复提交:** `abc123f`

---

## 5. Verification

**验证步骤:**
1. [步骤1]
2. [步骤2]

**验证结果:**
- [x] 问题已修复
- [x] 无新问题引入
- [x] 相关测试通过

---

## 6. Prevention

**预防措施:**
1. [如何防止类似问题再次发生]
2. [是否需要添加测试用例]
3. [是否需要更新文档]

---

*Session Status: RESOLVED*
*Resolved at: YYYY-MM-DDTHH:MM:SS*

# GSD Verifier 验证检查清单

## 三级验证框架

### Level 1: 任务验证
验证单个任务是否按预期完成。

```markdown
- [ ] 任务目标达成
- [ ] 验证命令通过
- [ ] 无错误/警告引入
- [ ] 代码风格一致
```

### Level 2: 阶段验证
验证整个阶段的集成效果。

```markdown
- [ ] 所有任务完成
- [ ] 功能端到端可用
- [ ] 无回归问题
- [ ] 测试通过
```

### Level 3: 目标回溯验证
验证阶段是否达成原始目标。

```markdown
- [ ] 目标行为可观察
- [ ] 需求已满足
- [ ] 用户场景可用
- [ ] 无副作用
```

## 验证检查项

### 功能验证
- [ ] 功能按预期工作
- [ ] 边界情况处理
- [ ] 错误处理正确
- [ ] 日志输出合理

### 代码验证
- [ ] 无 TypeScript 错误
- [ ] 无 ESLint 警告
- [ ] 代码格式正确
- [ ] 无硬编码敏感信息

### 测试验证
- [ ] 单元测试通过
- [ ] 集成测试通过
- [ ] E2E 测试通过
- [ ] 测试覆盖合理

### 安全验证
- [ ] 输入验证完整
- [ ] 认证授权正确
- [ ] 无敏感信息泄露
- [ ] 依赖无已知漏洞

## 存根检测模式

### 常见存根模式
```typescript
// ❌ 存根模式 - 需要真正实现
const result = true; // 硬编码
if (true) return;    // 永真条件
throw new Error("Not implemented"); // 未实现
console.log("TODO"); // TODO 占位
```

### 验证方法
1. 搜索 `TODO`、`FIXME`、`Not implemented`
2. 检查测试是否真实运行
3. 验证返回值是否动态生成

## 反模式扫描

### 常见反模式
- 硬编码配置值
- 空的 catch 块
- 未使用的变量
- 过深的嵌套
- 过长的函数

### 扫描命令
```bash
# 检查 TODO
grep -r "TODO\|FIXME\|XXX" src/

# 检查空 catch
grep -r "catch.*{}" src/

# 检查 console.log
grep -r "console\\.log" src/
```

## 验证报告模板

```markdown
# Verification Report

## Phase: XX-name

### Task Verification
| Task | Status | Notes |
|------|--------|-------|
| Task 1 | ✅ PASS | |
| Task 2 | ✅ PASS | |
| Task 3 | ❌ FAIL | 需要修复 |

### Issues Found
1. [问题描述]
2. [问题描述]

### Gaps (需要后续修复)
- [ ] Gap 1: 描述
- [ ] Gap 2: 描述

### Recommendations
1. [建议]
2. [建议]

### Verdict
- [ ] APPROVED - 可以进入下一阶段
- [ ] NEEDS_WORK - 需要修复后重新验证
- [ ] BLOCKED - 有阻塞问题
```

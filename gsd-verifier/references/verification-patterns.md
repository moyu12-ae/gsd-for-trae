# GSD Verifier 验证模式参考

## 验证模式分类

### 模式 1: 静态验证
不运行代码，通过静态分析验证。

```bash
# TypeScript 类型检查
npx tsc --noEmit

# ESLint 检查
npx eslint src/

# 依赖检查
npm audit
```

### 模式 2: 单元测试验证
运行单元测试验证功能正确性。

```bash
# 运行所有测试
npm test

# 运行特定测试
npm test -- --grep "test name"

# 测试覆盖率
npm test -- --coverage
```

### 模式 3: 集成测试验证
验证组件间的集成。

```bash
# API 测试
npm run test:integration

# E2E 测试
npm run test:e2e
```

### 模式 4: 手动验证
需要人工参与的验证。

- UI 视觉检查
- 用户体验验证
- 业务逻辑确认

## 存根检测

### 常见存根模式

```typescript
// ❌ 硬编码返回值
function getUser() {
  return { id: 1, name: "Test User" };
}

// ❌ 空实现
function processPayment() {
  // TODO: implement
}

// ❌ 永远抛出错误
function connect() {
  throw new Error("Not implemented");
}

// ❌ 跳过验证
function validate(input) {
  return true; // always pass
}
```

### 检测方法

```bash
# 搜索 TODO/FIXME
grep -rn "TODO\|FIXME\|XXX" src/

# 搜索空函数体
grep -rn "function.*{}" src/

# 搜索硬编码返回
grep -rn "return true\|return false\|return {" src/
```

## 回归验证

### 回归测试清单

1. **核心功能**
   - [ ] 登录/注册
   - [ ] 数据 CRUD
   - [ ] 权限控制

2. **边界情况**
   - [ ] 空输入处理
   - [ ] 超长输入处理
   - [ ] 无效输入处理

3. **性能**
   - [ ] 响应时间正常
   - [ ] 内存使用正常
   - [ ] 无明显性能退化

## 验证报告生成

### 自动收集信息

```bash
# Git 信息
git log --oneline -5
git diff HEAD~1

# 测试结果
npm test -- --json &gt; test-results.json

# 覆盖率
npm test -- --coverage --json &gt; coverage.json
```

### 报告格式

```markdown
# Verification Report

## 自动检查结果
- TypeScript: ✅ No errors
- ESLint: ✅ No warnings
- Tests: ✅ 42/42 passed
- Coverage: 85%

## 手动验证结果
- [x] 登录流程正常
- [x] 数据展示正确
- [ ] 性能需要优化

## 发现的问题
1. [问题描述]
2. [问题描述]

## 建议
1. [建议]
2. [建议]
```

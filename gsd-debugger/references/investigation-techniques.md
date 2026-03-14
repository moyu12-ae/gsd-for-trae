# GSD Debugger 调查技术参考

## 科学方法框架

### 1. 观察与假设
```
观察症状 → 形成假设 → 设计实验 → 验证假设
```

### 2. 假设测试循环
1. 基于观察形成假设
2. 设计最小化测试验证假设
3. 根据结果修正假设
4. 重复直到找到根因

## 调查技术

### 技术 1: 二分法排查
通过逐步排除缩小问题范围。

```bash
# 示例: 找出引入问题的提交
git bisect start
git bisect bad HEAD
git bisect good &lt;known-good-commit&gt;
# Git 会自动引导你找到问题提交
```

### 技术 2: 日志追踪
通过日志定位问题发生位置。

```bash
# 搜索关键错误日志
grep -r "Error\|Exception\|Failed" logs/

# 追踪特定请求
grep "request-id-xxx" logs/*.log
```

### 技术 3: 状态检查
验证系统状态是否符合预期。

```bash
# 检查进程状态
ps aux | grep &lt;process-name&gt;

# 检查端口占用
lsof -i :&lt;port&gt;

# 检查环境变量
env | grep &lt;var-name&gt;
```

### 技术 4: 依赖分析
检查依赖是否正确安装和配置。

```bash
# 检查 npm 依赖
npm ls &lt;package-name&gt;

# 检查版本冲突
npm dedupe --dry-run
```

### 技术 5: 最小复现
创建最小化示例复现问题。

```javascript
// 创建独立的测试文件
// 逐步添加代码直到问题出现
```

### 技术 6: 对比分析
对比工作版本和问题版本。

```bash
# 对比文件差异
diff -r working-version/ broken-version/

# 对比配置
git diff HEAD~1 config.json
```

### 技术 7: 网络调试
检查网络请求和响应。

```bash
# 检查 API 响应
curl -v https://api.example.com/endpoint

# 检查 DNS
nslookup example.com

# 检查网络连接
telnet example.com 443
```

## 常见问题模式

### 模式 1: 环境问题
- 症状: 本地工作，部署失败
- 检查: 环境变量、依赖版本、配置文件

### 模式 2: 竞态条件
- 症状: 间歇性失败
- 检查: 异步操作顺序、锁机制、超时设置

### 模式 3: 内存问题
- 症状: 性能下降、崩溃
- 检查: 内存泄漏、大对象、缓存策略

### 模式 4: 权限问题
- 症状: 访问被拒绝
- 检查: 文件权限、API 权限、认证状态

## 调试文件协议

### DEBUG.md 结构
```yaml
---
session_id: debug-xxx
created: YYYY-MM-DDTHH:MM:SS
status: [ACTIVE|RESOLVED|BLOCKED]
root_cause: [待填写]
---

# 调试会话

## 症状描述
[用户报告的问题]

## 调查记录

### 假设 1: [假设内容]
- 验证方法: [如何验证]
- 结果: [PASS/FAIL]
- 结论: [结论]

### 假设 2: [假设内容]
- 验证方法: [如何验证]
- 结果: [PASS/FAIL]
- 结论: [结论]

## 根因
[最终确定的根因]

## 解决方案
[修复方案]

## 验证
[如何验证修复有效]
```

## 认知偏见警示

1. **确认偏见**: 不要只寻找支持假设的证据
2. **锚定效应**: 不要过度依赖第一个假设
3. **近因偏见**: 不要假设最近的变更就是问题根源
4. **可用性偏见**: 不要只检查熟悉的部分

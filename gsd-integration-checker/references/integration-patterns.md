# GSD Integration Checker 集成模式参考

## 集成验证模式

### 模式 1: 接口一致性验证

**目的**: 验证组件间的接口是否一致

**检查项:**
- [ ] API 端点存在且可访问
- [ ] 请求/响应格式匹配
- [ ] 数据类型一致
- [ ] 错误处理统一

**验证命令:**
```bash
# 检查 API 端点
curl -X GET http://localhost:3000/api/health

# 验证响应格式
curl -s http://localhost:3000/api/data | jq .
```

### 模式 2: 数据流验证

**目的**: 验证数据在系统中的流动是否正确

**检查项:**
- [ ] 数据从输入到输出完整传递
- [ ] 数据转换正确
- [ ] 数据持久化正确
- [ ] 缓存机制工作正常

**验证方法:**
- 追踪请求 ID 从前端到数据库
- 验证数据在各层的格式
- 检查数据库中的最终状态

### 模式 3: 事件驱动验证

**目的**: 验证事件系统的集成

**检查项:**
- [ ] 事件发布成功
- [ ] 事件订阅正常
- [ ] 事件处理正确
- [ ] 错误重试机制工作

**验证日志:**
```
[INFO] Event published: user.created {id: 123}
[INFO] Event received: user.created
[INFO] Handler executed: send_welcome_email
[INFO] Event completed: user.created
```

## 常见集成问题

### 问题 1: CORS 错误

**症状**: 前端无法调用后端 API

**检查**:
```bash
# 检查响应头
curl -I http://localhost:3000/api/data
```

**解决方案**: 配置正确的 CORS 中间件

### 问题 2: 数据库连接失败

**症状**: 服务启动时无法连接数据库

**检查**:
```bash
# 检查连接字符串
echo $DATABASE_URL

# 测试连接
psql $DATABASE_URL -c "SELECT 1"
```

**解决方案**: 检查环境变量和数据库状态

### 问题 3: 环境变量缺失

**症状**: 配置相关的错误

**检查**:
```bash
# 列出所有环境变量
env | grep -E "(API|DB|REDIS)"

# 检查 .env 文件
cat .env
```

**解决方案**: 创建或更新 .env 文件

## 集成测试清单

### 端到端测试

- [ ] 用户注册流程
- [ ] 用户登录流程
- [ ] 数据创建流程
- [ ] 数据更新流程
- [ ] 数据删除流程
- [ ] 错误处理流程

### API 测试

- [ ] GET 请求正常
- [ ] POST 请求正常
- [ ] PUT 请求正常
- [ ] DELETE 请求正常
- [ ] 错误响应正确
- [ ] 认证/授权正常

### 数据库测试

- [ ] 连接正常
- [ ] 读写正常
- [ ] 事务正常
- [ ] 索引生效
- [ ] 迁移正常

## 验证报告模板

```markdown
# Integration Check Report

## Summary
- **Components Checked:** X
- **Passed:** Y
- **Warnings:** Z
- **Errors:** W

## Results

### ✅ Passed Integrations
- Frontend → Backend API
- Backend → Database
- Auth → API

### ⚠️ Warnings
- CORS 配置过于宽松 (建议限制域名)
- 某些 API 响应时间 &gt; 500ms

### ❌ Errors
- Redis 连接失败 (需要检查 REDIS_URL)

## Recommendations
1. 修复 Redis 连接
2. 优化慢 API
3. 收紧 CORS 配置

## Verdict
- [ ] **APPROVED** - 集成正常
- [ ] **NEEDS_WORK** - 需要修复后继续
- [ ] **BLOCKED** - 有阻塞问题
```

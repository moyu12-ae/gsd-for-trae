# GSD Debugger 常见问题参考

## 环境问题

### 问题 1: 依赖安装失败
**症状**: npm install 失败
**检查**: 
- Node 版本是否匹配
- 网络是否正常
- package-lock.json 是否存在
**解决**: 
```bash
rm -rf node_modules package-lock.json
npm install
```

### 问题 2: 环境变量缺失
**症状**: 服务启动失败，提示缺少配置
**检查**:
```bash
# 检查 .env 文件
cat .env
# 检查环境变量
env | grep VAR_NAME
```
**解决**: 创建或更新 .env 文件

### 问题 3: 端口占用
**症状**: EADDRINUSE 错误
**检查**:
```bash
lsof -i :3000
```
**解决**: 
```bash
kill -9 &lt;PID&gt;
```

## 代码问题

### 问题 4: TypeScript 类型错误
**症状**: 类型不匹配
**调试**:
```bash
npx tsc --noEmit
```
**常见原因**:
- 类型定义缺失
- 类型导入错误
- 类型版本不匹配

### 问题 5: 异步操作问题
**症状**: Promise 未 resolve 或 rejected
**调试**:
- 添加 .catch() 捕获错误
- 使用 console.log 追踪执行顺序
- 检查 await 是否正确使用

### 问题 6: 内存泄漏
**症状**: 内存持续增长
**调试**:
```bash
node --inspect app.js
# 使用 Chrome DevTools 分析
```

## 网络问题

### 问题 7: API 请求失败
**症状**: 请求返回错误或超时
**调试**:
```bash
# 检查网络连接
curl -v https://api.example.com

# 检查 DNS
nslookup api.example.com

# 检查证书
openssl s_client -connect api.example.com:443
```

### 问题 8: CORS 错误
**症状**: 跨域请求被阻止
**检查**: 响应头是否包含正确的 CORS 头
**解决**: 配置服务端 CORS 中间件

## 数据库问题

### 问题 9: 连接失败
**症状**: 数据库连接超时
**检查**:
```bash
# 检查数据库状态
pg_isready -h localhost -p 5432

# 检查连接字符串
echo $DATABASE_URL
```

### 问题 10: 查询性能差
**症状**: 查询响应慢
**调试**:
- 使用 EXPLAIN ANALYZE 分析查询
- 检查索引是否存在
- 检查查询是否命中缓存

# SESSION.md 模板

---
project: "{项目名称}"
session_start: "{YYYY-MM-DD HH:mm}"
last_updated: "{YYYY-MM-DD HH:mm}"
gsd_active: true
---

# Session Log

此文件记录 GSD 会话的关键操作，用于跨会话恢复上下文。

## 日志格式

每条记录遵循以下格式：

```markdown
## [{timestamp}] {操作类型}

**Phase:** P{n} - {阶段名称}
**Task:** T{n} - {任务名称}
**Action:** {started | completed | paused | blocked | decision}
**Progress:** {进度描述}

{详细内容}
```

## 操作类型说明

| 类型 | 说明 | 触发时机 |
|------|------|---------|
| Plan Created | 计划创建 | gsd-planner 完成后 |
| Execution Started | 执行开始 | gsd-executor 开始时 |
| Task Completed | 任务完成 | gsd-executor 完成任务后 |
| Verification Passed | 验证通过 | gsd-verifier 通过后 |
| Session Paused | 会话暂停 | gsd-pause 执行后 |
| Session Resumed | 会话恢复 | gsd-resume 执行后 |
| Decision Made | 做出决策 | 用户确认重要决策后 |
| Blocker Found | 发现阻塞 | 遇到无法解决的问题时 |

## 示例

```markdown
## [2024-03-05 14:30] Plan Created

**Phase:** P2 - Authentication
**Action:** 计划创建
**Progress:** 3 个任务已规划

创建了 Phase 2 的执行计划，包含：
- T1: JWT Token Generation
- T2: Token Validation Middleware
- T3: Refresh Token Rotation

## [2024-03-05 14:45] Task Completed

**Phase:** P2 - Authentication
**Task:** T1 - JWT Token Generation
**Action:** completed
**Progress:** 1/3 tasks done

完成了 JWT token 生成功能：
- 创建了 generateToken() 函数
- 添加了 token 过期时间配置
- 编写了单元测试

**Decision:** 使用 RS256 算法，因为需要支持密钥轮换

## [2024-03-05 15:00] Session Paused

**Phase:** P2 - Authentication
**Task:** T2 - Token Validation Middleware
**Action:** paused
**Progress:** 50% - 中间件基础结构完成

**Handoff:** handoff-2024-03-05-1500.md
**Next:** 完成中间件的错误处理逻辑

## [2024-03-05 16:30] Session Resumed

**Phase:** P2 - Authentication
**Task:** T2 - Token Validation Middleware
**Action:** resumed
**From:** handoff-2024-03-05-1500.md

从交接文件恢复，继续实现错误处理逻辑。
```

## 最佳实践

1. **及时记录** - 每个关键操作后立即追加记录
2. **简洁明了** - 只记录关键信息，避免冗余
3. **决策必记** - 所有重要决策都要记录理由
4. **问题追踪** - 遇到阻塞要详细记录问题状态

## 文件管理

- SESSION.md 是追加模式文件，不应删除历史记录
- 如果文件过大，可以归档到 `archives/` 目录
- 归档时保留最近 50 条记录在主文件中

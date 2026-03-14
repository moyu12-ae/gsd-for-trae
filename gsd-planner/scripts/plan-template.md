# GSD Plan 模板

```yaml
---
phase: XX-name
plan: NN
type: execute
wave: N                     # 执行波次 (1, 2, 3...)
depends_on: []              # 依赖的计划 ID
files_modified: []          # 此计划修改的文件
autonomous: true            # 是否全自动执行
requirements: []            # 此计划满足的需求 ID
user_setup: []              # 需要用户手动配置的内容

# 目标回溯验证
must_haves:
  truths: []                # 必须成立的可观察行为
  artifacts: []             # 必须存在的文件
  key_links: []             # 关键连接

# 规划完整性检查结果
planning_check:
  functional_requirements: ✅/❌/N/A    # 功能需求是否完整
  non_functional: ✅/❌/N/A            # 非功能需求是否明确
  edge_cases: ✅/❌/N/A                # 边界情况是否考虑
  data_model: ✅/❌/N/A                # 数据模型是否设计
  api_design: ✅/❌/N/A                # API 接口是否规划
  error_handling: ✅/❌/N/A            # 错误处理是否规划
  config_env: ✅/❌/N/A                # 配置环境是否明确
  risk_assessment: ✅/❌/N/A           # 风险是否评估

# 缺失项警告
warnings:
  missing: []               # 缺失的关键项
  needs_attention: []       # 需要关注但未阻塞的项
---
```

<objective>
此计划完成什么目标。
</objective>

<context>
@.planning/PROJECT.md
@.planning/ROADMAP.md
@.planning/STATE.md
@.planning/designs/YYYY-MM-DD-<feature>-design.md
</context>

## File Structure

| File | Responsibility | Public API |
|------|---------------|------------|
| `src/models/user.ts` | 用户数据模型 | `User` interface |
| `src/services/auth.ts` | 认证逻辑 | `login()`, `logout()` |
| `src/api/auth.ts` | 认证 API | POST /login, POST /logout |

> **说明**: 在规划任务前，先填写文件结构表。每个文件应有单一职责。

## Tasks

### Task N: [动作导向的名称]

**Files:**
- Create: `path/to/file.ext`
- Modify: `path/to/existing.ext:123-145`

#### 超小粒度步骤 (参考 superpowers: 每个步骤 2-5 分钟)

- [ ] **Step 1: Write failing test**
```typescript
describe('functionName', () => {
  it('should do something', () => {
    expect(functionName(input)).toBe(expected);
  });
});
```

- [ ] **Step 2: Run test to verify it fails**
Run: `npm test -- --testNamePattern="functionName"`
Expected: FAIL - "functionName is not defined"

- [ ] **Step 3: Write minimal implementation**
```typescript
export function functionName(input: Type): ReturnType {
  return expected;
}
```

- [ ] **Step 4: Run test to verify it passes**
Run: `npm test -- --testNamePattern="functionName"`
Expected: PASS

- [ ] **Step 5: Commit**
```bash
git add .
git commit -m "feat: add specific feature"
```

---

#### 标准任务格式 (非 TDD 模式)

<task type="auto">
  <name>Task 1: [动作导向的名称]</name>
  <files>path/to/file.ext, another/file.ext</files>
  <action>[具体实现 - 做什么，怎么做，避免什么及原因]</action>
  <verify>[证明成功的命令或检查]</verify>
  <done>[可衡量的验收标准]</done>
</task>

---

#### 检查点任务示例

<task type="checkpoint:decision" gate="blocking">
  <decision>[需要决策的内容]</decision>
  <context>[为什么这个决策重要]</context>
  <options>
    <option id="option-a"><name>[名称]</name><pros>[优点]</pros><cons>[缺点]</cons></option>
    <option id="option-b"><name>[名称]</name><pros>[优点]</pros><cons>[缺点]</cons></option>
  </options>
  <resume-signal>选择: option-a 或 option-b</resume-signal>
</task>

<task type="checkpoint:human-verify" gate="blocking">
  <what-built>[构建的内容] - 服务运行于 [URL]</what-built>
  <how-to-verify>访问 [URL] 并验证: [仅视觉检查，无需 CLI 命令]</how-to-verify>
  <resume-signal>输入 "approved" 或描述问题</resume-signal>
</task>

---

<verification>
完成前检查:
- [ ] [特定测试命令]
- [ ] [构建/类型检查通过]
- [ ] [行为验证]
</verification>

<success_criteria>

- 所有任务完成
- 所有验证检查通过
- 无错误或警告引入
- [计划特定标准]

</success_criteria>

<output>
完成后创建 `.planning/phases/XX-name/{phase}-{plan}-SUMMARY.md`
</output>

## Additional Considerations

### Edge Cases & Error Handling
[列出本计划需要处理的边界情况和错误处理策略]
- 空值处理
- 异常流程
- 并发场景
- 降级策略

### Non-Functional Requirements
[如有明确的非功能需求，在此说明]
- Performance: [响应时间、并发量要求]
- Security: [认证、授权、数据保护要求]
- Compatibility: [浏览器/平台兼容性]

### Data Model (if applicable)
[如涉及数据设计，列出关键数据对象]
- Entity: [对象名]
- Fields: [字段]
- Relationships: [与其他对象的关系]

### External Dependencies
[列出本计划依赖的外部服务/库]
- [依赖项1]: [用途]
- [依赖项2]: [用途]

## Zero Context Checklist (零上下文自检)

在生成计划时，检查以下问题：

- [ ] 执行者知道每个文件在哪里吗？
- [ ] 执行者知道为什么要用这个技术/库吗？
- [ ] 执行者知道运行什么命令验证吗？
- [ ] 执行者知道预期的输出是什么吗？
- [ ] 执行者知道失败时如何调试吗？

> 如果任何一项不满足，则在计划中补充完整信息。

## 字段说明

| 字段 | 必需 | 用途 |
|------|------|------|
| `phase` | 是 | 阶段标识符 (如 `01-foundation`) |
| `plan` | 是 | 阶段内计划编号 (如 `01`, `02`) |
| `type` | 是 | `execute` 或 `tdd` |
| `wave` | 是 | 执行波次，规划时预计算 |
| `depends_on` | 是 | 依赖的计划 ID 数组 |
| `files_modified` | 是 | 此计划涉及的文件 |
| `autonomous` | 是 | `true` 无检查点，`false` 有检查点 |
| `requirements` | 是 | 满足的 ROADMAP 需求 ID |
| `must_haves` | 是 | 目标回溯验证标准 |
| `planning_check` | 是 | 规划完整性检查结果 |
| `warnings` | 否 | 缺失项警告列表 |

## 任务类型

| 类型 | 用途 | 自主性 |
|------|------|--------|
| `auto` | Claude 可独立完成的所有工作 | 完全自主 |
| `tdd` | TDD 模式 (RED-GREEN-REFACTOR) | 完全自主 |
| `checkpoint:human-verify` | 视觉/功能验证 | 暂停，返回用户 |
| `checkpoint:decision` | 实现选择 | 暂停，返回用户 |
| `checkpoint:human-action` | 真正无法避免的手动步骤 | 暂停，返回用户 |

## TDD 模式说明

当 `type: tdd` 时，任务应遵循以下步骤：

1. **RED** - 写失败的测试
2. **GREEN** - 写最少代码让测试通过
3. **REFACTOR** - 重构优化
4. **Commit** - 提交

每个步骤应该是 2-5 分钟可完成的动作。

## 规划指南

1. **垂直切片优先**: 一个计划完成一个完整功能（model + API + UI）
2. **2-3 任务/计划**: 每个计划保持精简
3. **约 50% 上下文使用**: 避免上下文溢出
4. **仅引用必需的 SUMMARY**: 不反射性链接前置计划
5. **文件结构先于任务**: 先规划文件结构和职责，再分解任务
6. **完整代码**: 不写"添加验证"，写完整代码
7. **精确命令**: 包含具体的运行命令和预期输出

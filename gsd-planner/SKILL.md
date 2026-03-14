---
name: gsd-planner
description: 创建可执行阶段计划，包含任务分解、依赖分析和目标回溯验证。
triggers:
  keywords:
    - 规划
    - 计划
    - plan
    - planner
  prefixes:
    - "@gsd-planner"
---

# GSD Planner - 规划技能

## 概述

GSD Planner 负责创建可执行的阶段计划（PLAN.md），将阶段目标分解为具体任务，分析依赖关系，并进行目标回溯验证。

## 核心职责

1. **交互式需求收集** - 通过提问逐步细化需求（参考 superpowers brainstorming）
2. **解析用户决策** - 从 CONTEXT.md 获取锁定决策
3. **文件结构规划** - 先规划文件结构和职责
4. **分解阶段任务** - 超小粒度任务（2-5分钟一步）
5. **构建依赖图** - 分配执行波次
6. **目标回溯验证** - 确保计划可实现目标
7. **计划审查** - 分块审查机制

## 规划启动声明

**开始时必须声明：** "I'm using gsd-planner to create the implementation plan."

## 增量记忆文件结构

### 概述

gsd-planner 采用**增量记忆写入**策略：每个 Step 完成后，将产出写入对应的增量记忆文件，而非全部停留在内存中。

### 文件命名规范

```
.planning/phases/XX-name/
├── 01-context.md         # Step 1: 加载的上下文
├── 02-research-check.md   # Step 1.5: 研究文件检查结果
├── 03-requirements.md    # Step 1.6: 收集的需求
├── 04-assumptions.md     # Step 1.7: 零上下文假设
├── 05-goals.md           # Step 2: 阶段目标解析
├── 06-filestructure.md   # Step 3: 文件结构规划
├── 07-tasks.md           # Step 4: 任务分解
├── 08-dependencies.md    # Step 5: 依赖分析
├── 09-verification.md    # Step 6: 目标回溯验证
└── PLAN.md               # Step 9: 最终汇总
```

### 断点恢复

gsd-planner 启动时检查 `.planning/phases/XX/` 目录是否存在增量文档：
- 存在 → 询问用户选择"继续"或"重新开始"
- 不存在 → 正常开始全新规划

## 探索级别协议

### 概述

gsd-planner 采用**探索级别 (Discovery Level) 协议**来确定规划前的研究深度。这个协议确保：
- 避免过度研究（浪费时间）
- 避免研究不足（导致错误决策）
- 根据任务复杂度动态调整研究深度

### 四个探索级别

| 级别 | 名称 | 适用场景 | 耗时 | 产出 |
|------|------|---------|------|------|
| **Level 0** | 跳过 | 纯内部工作，现有模式，无需新依赖 | 0 分钟 | 无 |
| **Level 1** | 快速验证 | 确认已知库的语法/版本 | 2-5 分钟 | 无 |
| **Level 2** | 标准研究 | 在 2-3 个选项中选择，或新外部集成 | 15-30 分钟 | DISCOVERY.md |
| **Level 3** | 深度研究 | 架构决策有长期影响，解决新问题 | 1+ 小时 | 详细 DISCOVERY.md |

### Level 判断标准

**Level 0 - 跳过研究** (必须全部满足):
- 所有工作遵循现有代码模式
- 无新的外部依赖
- 示例：添加删除按钮、给模型添加字段、创建简单 CRUD 端点

**Level 1 - 快速验证** (任一满足):
- 需要确认单一已知库的语法
- 需要确认已知库的版本信息
- 使用 Context7 快速查询即可解决

**Level 2 - 标准研究** (任一满足):
- 需要在 2-3 个技术选项中选择
- 需要集成新的外部服务/API
- 存在"选择/评估"类描述

**Level 3 - 深度研究** (任一满足):
- 架构决策有长期影响
- 涉及多个外部服务
- 数据建模设计
- 认证/安全设计

### 探索级别判断流程

```
1. 分析阶段描述中的关键词
2. 检查是否涉及新依赖/外部集成
3. 评估技术选择数量和复杂度
4. 确定最低必要级别

注意：可以升级级别，但不能跳过更高级别
```

### 探索级别处理

**Level 0**: 直接开始规划，无需额外研究

**Level 1**: 使用 Context7 快速查询，不生成 DISCOVERY.md

```markdown
**Context7 快速查询**:
使用 mcp__context7__* 工具查询相关文档
- 例如: "React useEffect cleanup function"
- 无需生成文档，直接使用查询结果
```

**Level 2**: 启动标准研究流程

```markdown
1. 使用 Task 工具启动 gsd-researcher
2. 生成 .planning/phases/XX-name/DISCOVERY.md
3. 在规划中使用研究结论
```

**Level 3**: 进行深度技术调研

```markdown
1. 启动完整技术调研
2. 考虑并行研究多个方面
3. 生成详细 DISCOVERY.md
4. 可能需要多次研究迭代
```

### 垂直切片优先原则

**重要**: 无论选择哪个级别，规划时应优先采用**垂直切片**而非水平分层。

**垂直切片** (推荐):
```
Plan 01: 用户功能 (模型 + API + UI)
Plan 02: 产品功能 (模型 + API + UI)
Plan 03: 订单功能 (模型 + API + UI)
```
结果: 三个计划可在 Wave 1 并行执行

**水平分层** (避免):
```
Plan 01: 创建所有模型
Plan 02: 创建所有 API
Plan 03: 创建所有 UI
```
结果: 完全顺序执行 (02 需等待 01，03 需等待 02)

### 何时需要探索级别评估

探索级别评估应在 **Step 1: 加载上下文** 和 **Step 1.5: 检查研究文件** 之间进行。

## 工作流程

### Step 1: 加载上下文

```
使用 Read 工具读取:
- .planning/ROADMAP.md (项目路线图)
- .planning/CONTEXT.md (用户决策)
- .planning/research/*.md (研究文件)
- .planning/PLANNING-CHECKLIST.md (规划清单，如存在)
```

**增量记忆写入**:
读取完成后，将加载的上下文信息写入 `.planning/phases/XX/01-context.md`:

```markdown
# Step 1: 加载上下文

## 加载时间
[时间戳]

## 读取的源文件
- .planning/ROADMAP.md
- .planning/CONTEXT.md
- .planning/research/*.md
- .planning/PLANNING-CHECKLIST.md

## 上下文摘要
[从 ROADMAP.md 提取的当前阶段信息]
[从 CONTEXT.md 提取的关键决策]
[研究文件的关键结论]
```

### Step 1.5: 检查研究文件（必须执行）

**⚠️ 重要：规划前必须确保已完成技术研究**

使用 Glob 工具检查 `.planning/research/` 目录是否存在研究文件：

```
Glob: .planning/research/*.md
```

**决策分支**:
- 存在研究文件：继续 Step 1.6（读取研究文件作为上下文）
- 不存在研究文件：
  1. 返回用户提示："⚠️ 规划需要先了解技术需求"
  2. 提示："请先进行技术研究，说 '研究 XX 技术'"
  3. **停止执行规划**，不生成 PLAN.md

**增量记忆写入**:
将检查结果写入 `.planning/phases/XX/02-research-check.md`:

```markdown
# Step 1.5: 研究文件检查

## 检查时间
[时间戳]

## 检查结果
- 状态: [存在/不存在]
- 发现的研究文件: [文件列表]

## 决策
[继续规划/停止规划]
```

### Step 1.6: 交互式需求收集

**目的**: 通过 AskUserQuestion 一次性收集完整需求信息。

**⚠️ 重要：必须使用 Trae 的 AskUserQuestion 工具**

Trae 的 AskUserQuestion 支持 questions 数组，可一次呈现多个问题。

**核心原则**:
- 一次性设计多维度问题（不是逐轮探索）
- 问题必须精准且全面，预期能达成对需求 90% 的了解
- 每个问题提供 2-4 个具体选项供用户选择
- 使用选择题而非开放式问题

**问题设计维度**:
1. 功能需求 - 用户想要什么
2. 非功能需求 - 性能/安全/可用性
3. 边界条件 - 异常情况处理
4. 技术约束 - 技术选型偏好
5. 优先级 - 核心功能 vs 锦上添花

其余维度视具体情况而定，并且每个维度不局限于一个问题，如果需要多个问题才能了解一个维度的需求，那么这个维度就多增加几个问题，直到能让你理解用户需求为止

**跳过机制**:
- ⚠️ **重要：提问阶段必须单独暂停，等待用户回答后再继续**
- 用户可以输入 "跳过" 跳过交互式提问
- 如果用户已有完整的研究文档，可以直接跳过

**增量记忆写入**:
将收集的需求写入 `.planning/phases/XX/03-requirements.md`:

```markdown
# Step 1.6: 需求收集

## 收集时间
[时间戳]

## 功能需求
- [需求1]
- [需求2]

## 非功能需求
- [性能要求]
- [安全要求]

## 边界条件
- [条件1]
- [条件2]

## 技术约束
- [约束1]

## 优先级
- P0: [核心功能]
- P1: [重要功能]
- P2: [锦上添花]
```

### Step 1.7: 零上下文假设 (参考 superpowers writing-plans)

**目的**: 假设执行者对项目零上下文，计划必须极其详尽。

**核心原则** (来自 superpowers: "Assume they are a skilled developer, but know almost nothing about our toolset or problem domain")：
- 假设执行者不了解项目代码库
- 假设执行者不了解技术选型原因
- 假设执行者不知道去哪里找参考资料
- 假设执行者不知道如何验证

**计划自检清单**:
在生成计划时，必须自问：
- [ ] 执行者知道每个文件在哪里吗？
- [ ] 执行者知道为什么要用这个技术/库吗？
- [ ] 执行者知道运行什么命令验证吗？
- [ ] 执行者知道预期的输出是什么吗？
- [ ] 执行者知道失败时如何调试吗？

**如果任何一项不满足，则在计划中补充完整信息。**

**增量记忆写入**:
将假设清单写入 `.planning/phases/XX/04-assumptions.md`:

```markdown
# Step 1.7: 零上下文假设

## 分析时间
[时间戳]

## 执行者假设
- [ ] 执行者不了解项目代码库
- [ ] 执行者不了解技术选型原因
- [ ] 执行者不知道去哪里找参考资料
- [ ] 执行者不知道如何验证

## 自检结果
- 文件位置说明: [是否需要添加]
- 技术选型说明: [是否需要添加]
- 验证命令: [是否需要添加]
- 预期输出: [是否需要添加]
- 调试指南: [是否需要添加]
```

### Step 2: 解析阶段目标

从 ROADMAP.md 提取当前阶段：
- 阶段目标 (Goal)
- 成功标准 (Success Criteria)
- 依赖项 (Dependencies)

**增量记忆写入**:
将目标解析结果写入 `.planning/phases/XX/05-goals.md`:

```markdown
# Step 2: 解析阶段目标

## 解析时间
[时间戳]

## 阶段名称
[XX-name]

## 阶段目标 (Goal)
[目标描述]

## 成功标准 (Success Criteria)
- [标准1]
- [标准2]

## 依赖项 (Dependencies)
- [依赖1]
- [依赖2]
```

### Step 3: 文件结构规划 (参考 superpowers writing-plans)

**目的**: 在定义任务前先规划文件结构和职责。

**核心原则** (来自 superpowers):
- 设计边界清晰、单责任的文件
- 每个文件应该有明确的职责
- 小而专注的文件比大而全的文件更好
- 一起变更的文件应该放在一起

**文件结构规划输出格式**:
```markdown
## File Structure

| File | Responsibility | Public API |
|------|---------------|------------|
| `src/models/user.ts` | 用户数据模型 | `User` interface |
| `src/services/auth.ts` | 认证逻辑 | `login()`, `logout()` |
| `src/api/auth.ts` | 认证 API | POST /login, POST /logout |
```

**增量记忆写入**:
将文件结构规划写入 `.planning/phases/XX/06-filestructure.md`:

```markdown
# Step 3: 文件结构规划

## 规划时间
[时间戳]

## File Structure

| File | Responsibility | Public API |
|------|---------------|------------|
| | | |

## 设计原则
- [原则1]
- [原则2]
```

### Step 4: 任务分解 - 超小粒度 (参考 superpowers writing-plans)

**目的**: 每个步骤只做一件事，2-5分钟可完成。

**任务步骤结构** (来自 superpowers):
```markdown
### Task N: [组件名]

**Files:**
- Create: `src/path/file.ts`
- Modify: `src/path/existing.ts:123-145`

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

- [ ] **Step 5: Commit
```

**TDD 原则** (来自 superpowers: "DRY. YAGNI. TDD. Frequent commits"):
- RED: 先写失败的测试
- GREEN: 写最少的代码让测试通过
- REFACTOR: 重构优化
- 频繁提交

**增量记忆写入**:
将任务列表写入 `.planning/phases/XX/07-tasks.md`:

```markdown
# Step 4: 任务分解

## 分解时间
[时间戳]

## 任务列表

### Task N: [任务名称]

**Files:**
- Create: `path/to/file.ext`
- Modify: `path/to/existing.ext`

- [ ] **Step 1: [描述]**
- [ ] **Step 2: [描述]**
- [ ] **Step 3: [描述]**

[...更多任务]
```

### Step 4.5: Trae Plan 模式准备

**⚠️ 为每个任务生成 Plan 模式所需的信息**

当任务复杂时（如任务步骤超过 5 步），为任务生成摘要信息，方便用户使用 Trae Plan 模式执行。

**任务摘要信息模板：**
```markdown
## Task N: [任务名称]

### 任务目标
[任务的具体目标描述]

### 技术栈
- 框架: [如 React, Vue, Node.js]
- 语言: [如 TypeScript, Python]
- 核心依赖: [主要依赖]

### 需要创建/修改的文件
- 创建: [文件列表]
- 修改: [文件列表]

### 验收标准
- [可验证的标准 1]
- [可验证的标准 2]

### 约束条件
- [边界限制]
```

**输出提示：**
```
📋 为任务 [T1] 生成的 Plan 模式摘要：

---
## Task N: [任务名称]

### 任务目标
[目标描述]

### 技术栈
- 框架: [框架]
- 语言: [语言]

### 需要创建/修改的文件
- 创建: [文件]
- 修改: [文件]

### 验收标准
- [标准1]
- [标准2]

### 约束条件
- [约束]

---

你可以复制以上信息，输入 / 选择 Plan 模式执行此任务。
```

**适用场景：**
- 任务步骤 ≥ 5 步
- 任务涉及多个文件
- 任务有复杂的依赖关系

**不适用场景：**
- 简单任务（步骤 ≤ 3）
- 用户已明确表示不需要

### Step 5: 依赖分析

构建任务依赖图：
- 识别任务间依赖
- 分配执行波次 (Wave)
- 标记可并行任务

### Step 6: 目标回溯验证

从目标反推验证：
1. 目标需要什么为真？
2. 这些真理需要什么文件存在？
3. 这些文件需要如何连接？

### Step 7: 计划审查 (参考 superpowers writing-plans)

**目的**: 计划写完后必须经过审查才能执行。

**审查流程**:
1. 将计划分成多个 chunk（每个 ≤500 行）
2. 对每个 chunk 调用 gsd-plan-checker 审查
3. 如果发现问题，修复后重新审查
4. 最多 5 次迭代，超过则提示用户人工介入

**Chunk 边界**:
```markdown
## Chunk 1: [组件名]

... task content ...

## Chunk 2: [组件名]

... task content ...
```

**审查循环模板**:
```
正在审查计划...

Chunk 1 审查结果: ✅ 通过 / ❌ 需要修复
- 问题 1: [具体问题]
- 问题 2: [具体问题]

[如果是问题] 正在修复...
[修复完成] 重新审查...
```

### Step 8: 设计文档持久化 (新增)

**目的**: 除了 PLAN.md，额外生成 DESIGN.md。

**设计文档结构**:
```markdown
# [功能名] Design

## 设计决策
- [决策 1]: [原因]
- [决策 2]: [原因]

## 技术方案
### 数据模型
[数据模型设计]

### API 设计
[API 设计]

### 架构
[架构描述]

## 已知限制
- [限制 1]
- [限制 2]

## 参考
- @.planning/research/xxx.md
```

**保存路径**: `.planning/designs/YYYY-MM-DD-<feature>-design.md`

### Step 9: 生成 PLAN.md (写入长期记忆)

**⚠️ 重要：PLAN.md 是规划的交付物，写入即完成**

PLAN.md 是 GSD 系统的核心交付物，写入完成后规划即完成。

```markdown
---
phase: XX-name
plan: NN
type: execute
wave: N
depends_on: []
files_modified: []
autonomous: true
requirements: []
user_setup: []

# 目标回溯验证
must_haves:
  truths: []
  artifacts: []
  key_links: []

# 规划完整性检查结果
planning_check:
  functional_requirements: ✅/❌/N/A
  non_functional: ✅/❌/N/A
  edge_cases: ✅/❌/N/A
  data_model: ✅/❌/N/A
  api_design: ✅/❌/N/A
  error_handling: ✅/❌/N/A
  config_env: ✅/❌/N/A
  risk_assessment: ✅/❌/N/A

warnings:
  missing: []
  needs_attention: []
---

<objective>
此计划完成什么目标。
</objective>

<context>
@.planning/PROJECT.md
@.planning/ROADMAP.md
@.planning/STATE.md
@.planning/designs/YYYY-MM-DD-feature-design.md
</context>

## File Structure

| File | Responsibility | Public API |
|------|---------------|------------|
| | | |

## Tasks

### Task N: [动作导向的名称]

**Files:**
- Create: `path/to/file.ext`
- Modify: `path/to/existing.ext:123-145`

- [ ] **Step 1: Write failing test**
```code
```

- [ ] **Step 2: Run test to verify it fails**
Run: `command`
Expected: FAIL - "expected error"

- [ ] **Step 3: Write minimal implementation**
```code
```

- [ ] **Step 4: Run test to verify it passes**
Run: `command`
Expected: PASS

- [ ] **Step 5: Commit**
```bash
git add .
git commit -m "feat: description"
```

<verification>
- [ ] 验证命令
</verification>

<success_criteria>
- 所有任务完成
- 所有验证通过
</success_criteria>

<output>
完成后创建 `.planning/phases/XX-name/{phase}-{plan}-SUMMARY.md`
</output>
```

### Step 10: (可选) 记录会话快照

**⚠️ 注意：SESSION.md 是可选的会话日志，不是规划交付物**

真正的规划交付物是 Step 9 完成的 PLAN.md。SESSION.md 仅用于：
- 跨会话恢复工作进度
- 追踪项目历史
- 记录用户决策

如果需要记录会话快照，使用 Write 工具将以下内容追加到 `.planning/SESSION.md`：

```markdown
## [{时间戳}] Plan Created
**Phase:** [阶段名]
**Action:** 创建执行计划
**Plan File:** .planning/PLAN.md
**Tasks:** [任务列表]
**Next:** 等待用户确认后执行
```

**如果 SESSION.md 不存在，则创建它：**
```markdown
---
project: [项目名]
session_start: [时间戳]
last_updated: [时间戳]
---

# Session Log

## [{时间戳}] Plan Created
**Phase:** [阶段名]
**Action:** 创建执行计划
**Plan File:** .planning/PLAN.md
**Tasks:** [任务列表]
**Next:** 等待用户确认后执行
```

### Step 11: 返回规划完成 (规划即交付)

**⚠️ 重要：规划即交付，无需询问确认**

规划已完成，PLAN.md 已写入长期记忆。用户可查看计划后主动说"执行"开始执行阶段。

**返回格式：**
```
✅ 规划已完成！

📋 计划文件: .planning/PLAN.md

你可以查看计划后，说 "执行" 开始执行计划
```

**禁止：**
- ❌ 询问用户"是否确认执行"
- ❌ 自动开始执行计划
- ❌ 调用 gsd-executor

## 工具映射

| GSD 工具 | Trae 工具 |
|---------|----------|
| Read | Read |
| Write | Write |
| Glob | Glob |
| Grep | Grep |

## 用户决策保真度

**关键原则：**

1. **锁定决策** (来自 `## Decisions`) - 必须精确实现
   - 用户说 "用库 X" → 任务必须用库 X
   - 用户说 "卡片布局" → 任务必须实现卡片

2. **延期想法** (来自 `## Deferred Ideas`) - 不得出现在计划中
   - 用户延期了 "搜索功能" → 不允许搜索任务

3. **Claude 自由裁量** (来自 `## Claude's Discretion`) - 使用判断力

## 规划哲学

### 单人开发者 + Claude 工作流

- 用户 = 愿景/产品负责人
- Claude = 构建者
- 以 Claude 执行时间估算，非人工时间

### 计划即提示词

PLAN.md 是给执行者的提示词，不是文档：
- 具体、可执行
- 包含上下文引用
- 明确验证标准

### 零上下文假设

(来自 superpowers writing-plans)

假设执行者：
- 不了解项目代码库
- 不了解技术选型原因
- 不知道去哪里找参考资料
- 不知道如何验证

**计划必须回答执行者的所有问题。**

### 超小粒度任务

(来自 superpowers writing-plans)

每个步骤：
- 2-5 分钟可完成
- 包含具体代码
- 包含验证命令
- 包含预期输出

### 频繁提交

(来自 superpowers: "Frequent commits")

每个任务步骤完成后立即提交，不要累积多个更改。

## 最佳实践

1. **每个任务独立可验证** - 有明确的完成标准
2. **依赖关系清晰** - 波次分配合理
3. **上下文引用充分** - 执行者不需要额外搜索
4. **验证标准明确** - 可通过工具检验
5. **文件结构清晰** - 单责任、可维护
6. **代码完整** - 不写 "添加验证"，写完整代码
7. **规划阶段无需 TodoWrite** - 规划需要持久化到 PLAN.md，TodoWrite 仅在执行阶段使用

## 与其他 Skills 的关系

- **输入**: gsd-orchestrator 触发，读取 gsd-roadmap 输出
- **前置检查**: gsd-planner 开始规划前必须检查 .planning/research/ 是否存在
  - 如缺失：停止规划，提示用户先进行技术研究
  - 如存在：读取研究文件作为上下文
- **输出**: PLAN.md 供 gsd-executor 使用
- **验证**: gsd-plan-checker 验证计划质量
- **状态**: gsd-context (保存会话状态)
- **可选**: gsd-codebase-mapper 可在规划前提供代码库上下文

## 审查循环规则

(来自 superpowers writing-plans)

- 同一 agent 修复问题（保持上下文）
- 最多 5 次迭代
- 超过 5 次则提示用户人工介入
- 审查者只是建议，不遵守需解释原因

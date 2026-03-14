---
name: gsd-executor
description: 执行 GSD 计划，包含原子提交、偏差处理、检查点协议和状态管理。
triggers:
  keywords:
    - 执行
    - execute
    - executor
    - 做
    - 开始做
  prefixes:
    - "@gsd-executor"
---

# GSD Executor - 执行技能

## 概述

GSD Executor 负责执行 PLAN.md 文件中的任务，创建每个任务的提交，处理偏差，在检查点暂停，并生成 SUMMARY.md 文件。

## 核心职责

1. **原子执行** - 每个任务独立执行和提交
2. **波次执行** - 按依赖波次并行执行，最大化效率
3. **偏差处理** - 自动处理规则 1-3，询问规则 4
4. **检查点协议** - 在检查点暂停等待用户输入
5. **状态管理** - 更新 STATE.md 和生成 SUMMARY.md
6. **两阶段审查** - Spec 合规性 + 代码质量双重审查
7. **Git 分支策略** - 支持 none/phase/milestone 分支策略

## Git 分支策略

### 概述

GSD 支持三种 Git 分支策略，可根据项目需求选择合适的策略。

### 分支策略类型

| 策略 | 描述 | 适用场景 |
|------|------|---------|
| **none** (默认) | 在当前分支提交 | 小项目，个人开发 |
| **phase** | 每个阶段一个分支 | 中型项目，团队开发 |
| **milestone** | 每个里程碑一个分支 | 大项目，多阶段发布 |

### none 策略 (默认)

**行为**:
- 所有提交在当前分支进行
- 直接提交，不创建新分支
- 适合个人项目或小团队

**配置**:
```json
{
  "git_strategy": "none"
}
```

### phase 策略

**行为**:
- 每个阶段创建独立分支
- 分支命名: `phase/N-name`
- 阶段完成后合并到主分支
- 支持 squash merge

**分支创建流程**:
```
1. 检查 config.json 中的 git_strategy
2. 如果是 "phase":
   a. 创建分支: phase/N-name
   b. 切换到新分支
   c. 在新分支上执行任务
   d. 阶段完成后合并到主分支
```

**分支命名规范**:
```
phase/1-user-auth
phase/2-product-crud
phase/3-order-system
```

**合并流程**:
```
1. 阶段执行完成
2. 创建 PR 或直接合并
3. (可选) squash merge
4. 删除阶段分支
```

### milestone 策略

**行为**:
- 每个里程碑创建独立分支
- 分支命名: `milestone/N-name`
- 里程碑完成后合并到主分支
- 适合大型项目

**分支命名规范**:
```
milestone/v1.0-release
milestone/v2.0-features
```

### Squash Merge 支持

**配置**:
```json
{
  "git_strategy": "phase",
  "squash_merge": true
}
```

**优点**:
- 保持主分支历史整洁
- 减少提交数量
- 便于回滚

### 分支策略配置

在 `.planning/config.json` 中配置:

```json
{
  "git_strategy": "phase",
  "squash_merge": true,
  "main_branch": "main"
}
```

### 执行时分支处理

**阶段开始时**:
1. 读取 config.json 中的 git_strategy
2. 根据策略决定是否创建分支
3. 切换到正确的分支

**任务提交时**:
1. 创建原子提交
2. 提交信息格式: `[Phase N] Task M: 任务描述`

**阶段完成时**:
1. 如果是 phase/milestone 策略: 合并分支
2. 更新 STATE.md 中的分支状态

## 工作流程

### Step 1: 加载项目状态

```
使用 Read 工具读取:
- .planning/PLAN.md (当前计划)
- .planning/STATE.md (项目状态)
- .planning/CONTEXT.md (上下文)
```

### Step 2: 解析计划

解析 PLAN.md：
- frontmatter (phase, plan, type, autonomous, wave, depends_on)
- objective (目标)
- context (@-references)
- tasks (任务列表)
- verification/success criteria (验证标准)
- output spec (输出规范)

### Step 2.5: 计算波次 (Wave)

**⚠️ 重要: 在执行任务前，必须先计算任务的波次**

从 PLAN.md 的 frontmatter 中提取 wave 信息，如果 wave 未指定则根据依赖关系计算。

**波次计算算法**:
```
waves = {}
for each task in dependency_order:
  if task.depends_on is empty:
    task.wave = 1
  else:
    task.wave = max(waves[dep] for dep in task.depends_on) + 1
  waves[task.id] = task.wave
```

**波次分组**:
```markdown
## Wave 分组

### Wave 1 (可并行)
- Task 1: [任务名]
- Task 2: [任务名]

### Wave 2 (需等待 Wave 1)
- Task 3: [任务名]
- Task 4: [任务名]

### Wave 3 (需等待 Wave 2)
- Task 5: [任务名]
```

**执行顺序**:
1. 先执行 Wave 1 的所有任务（可并行）
2. Wave 1 完成后，执行 Wave 2 的任务
3. 以此类推

**并行执行条件**:
- 同一 Wave 内任务可并行执行
- 无文件冲突的任务可安全并行

### Step 3: 确定执行模式

**模式 A: 完全自主 (无检查点)**
- 执行所有任务
- 创建 SUMMARY
- 提交

**模式 B: 有检查点**
- 执行到检查点
- 停止并返回结构化消息
- 等待恢复

**模式 C: 继续**
- 从 `<completed_tasks>` 恢复
- 验证已有提交
- 从指定任务继续

### Step 3.5: Trae Plan 模式推荐

**⚠️ 重要：判断是否推荐用户使用 Trae Plan 模式**

当任务满足以下条件时，推荐用户使用 Trae Plan 模式：
1. 任务有明确的输入/输出
2. 任务步骤可分解为 3-10 个子步骤
3. 任务边界清晰，不会蔓延
4. 任务复杂度中等或较高

**不适合使用 Plan 模式的情况：**
- 简单修改（如只改一行代码）
- 已有详细执行步骤的任务
- 用户明确要求直接执行

**推荐提示模板：**
```
🤔 这个任务 [T1: 任务名称] 适合使用 Trae Plan 模式。

优点：
- 先生成详细计划，你确认后再执行
- 零返工，每一步都经过你的审核
- 适合复杂任务，避免做到一半发现方向错了

操作：
输入 / 选择 Plan，然后粘贴以下任务信息：
```
[任务摘要信息]
```

是否想使用 Plan 模式？
```

**任务摘要信息模板：**
```
## 任务目标
[任务的具体目标]

## 技术栈
- 框架: [如 React, Vue, Node.js]
- 语言: [如 TypeScript, Python]
- 核心依赖: [主要依赖]

## 需要创建/修改的文件
- 创建: [文件列表]
- 修改: [文件列表]

## 验收标准
- [可验证的标准 1]
- [可验证的标准 2]

## 约束条件
- [边界限制，如：不使用 XXX、不超过 XXX]
```


### Step 4: 执行任务

**⚠️ 使用 TodoWrite 跟踪执行进度**

对于每个任务，使用 TodoWrite 显示当前进度：

```markdown
TodoWrite(
  todos: [
    {
      content: "执行任务 T1: [任务名称]",
      status: "in_progress",
      priority: "high"
    },
    {
      content: "执行任务 T2: [任务名称]",
      status: "pending",
      priority": "high"
    },
    {
      content: "执行任务 T3: [任务名称]",
      status: "pending",
      priority: "high"
    }
  ]
)
```

**执行过程中更新状态：**
- 任务开始时：更新为 `in_progress`
- 任务完成时：更新为 `completed`
- 遇到问题时：添加新 todo 描述问题

**任务完成后：**
- 清除 TodoWrite（任务已记录在 SUMMARY.md）
- 不需要保留在 TodoWrite 中（SUMMARY.md 是长期记录）

对于每个任务：

1. **如果 type="auto":**
   - 执行任务
   - 应用偏差规则
   - 处理认证错误
   - 运行验证
   - 提交
   - 记录完成状态

2. **如果 type="checkpoint:*":**
   - 立即停止
   - 返回检查点消息

### Step 4.5: 两阶段审查 (每个任务执行后)

**⚠️ 重要: 每个任务完成后必须经过两阶段审查**

每个任务执行完成后，必须进行两阶段审查以确保质量。

#### Stage 1: Spec 合规性审查

检查任务是否满足 PLAN.md 中的要求：

- [ ] 所有 locked decisions 是否实现
- [ ] 无 deferred ideas 出现
- [ ] 所有 must_haves 是否满足
- [ ] 边界条件是否处理
- [ ] 验证命令是否通过

**审查模板**:
```
## Stage 1: Spec 合规性审查

### 任务: [任务名]
- [ ] 需求实现完整
- [ ] 无额外功能
- [ ] 验证通过

结果: ✅ 通过 / ❌ 需修复
```

#### Stage 2: 代码质量审查

检查代码质量是否符合标准：

- [ ] 代码风格一致
- [ ] 无明显 bug
- [ ] 错误处理完整
- [ ] 无硬编码敏感信息
- [ ] 无安全漏洞

**审查模板**:
```
## Stage 2: 代码质量审查

### 任务: [任务名]
- [ ] 代码风格符合项目规范
- [ ] 无明显 bug 或逻辑错误
- [ ] 错误处理完善
- [ ] 安全检查通过

结果: ✅ 通过 / ❌ 需修复
```

#### 审查循环

如果任一阶段发现问题：

1. 修复问题
2. 重新审查该阶段
3. 最多 5 次循环
4. 超过 5 次则提示用户人工介入

### Step 5: 生成 SUMMARY.md

```markdown
---
phase: P1
plan: plan-001
duration: "2h 30m"
tasks_completed: 5
tasks_total: 5
---

# Phase P1 执行总结

## 完成任务
- [x] T1: 任务描述
- [x] T2: 任务描述

## 偏差处理
- [Rule 1 - Bug Fix] 修复了 xxx 问题

## 验证结果
- [x] 验证标准 1
- [x] 验证标准 2

## 输出文件
- file1.ts
- file2.ts
```

### Step 6: 保存会话快照 (必须执行)

**⚠️ 重要：执行完成后必须保存会话状态**

使用 Write 工具将以下内容追加到 `.planning/SESSION.md`：

```markdown
## [{时间戳}] Task Completed
**Phase:** [阶段名]
**Task:** [任务名]
**Status:** completed
**Files:** [修改的文件列表]
**Decision:** [如有决策记录]
**Next:** 等待用户下一步指示
```

### Step 7: 返回用户确认 (必须执行)

**⚠️ 重要：执行完成后必须返回用户确认**

执行完成后，必须向用户返回以下确认消息：

```
✅ 执行已完成！

📋 执行总结: .planning/phases/XX-name/{phase}-{plan}-SUMMARY.md

📝 完成任务:
- [x] T1: 任务名称
- [x] T2: 任务名称

📁 修改的文件:
- file1.ts
- file2.ts

⏭️  下一步:
请说 "验证" 开始验证执行结果
请说 "暂停" 保存进度
请说 "继续" 执行下一个任务
```

**禁止：**
- ❌ 自动继续执行下一个阶段
- ❌ 不告知用户就继续后续操作
- ❌ 直接调用 gsd-verifier

**必须：**
- ✅ 明确告知用户执行已完成
- ✅ 提供明确的下一步指令选项
- ✅ 等待用户明确指示后才继续

## 偏差处理规则

### RULE 1: 自动修复 Bug

**触发条件:** 代码不按预期工作

**处理方式:** 自动修复，无需询问

**示例:** 错误查询、逻辑错误、类型错误、空指针异常

### RULE 2: 自动添加缺失的关键功能

**触发条件:** 缺少正确性/安全性/基本操作所需的功能

**处理方式:** 自动添加，无需询问

**示例:** 缺少错误处理、无输入验证、缺少空检查

### RULE 3: 自动修复阻塞问题

**触发条件:** 某些问题阻止完成当前任务

**处理方式:** 自动修复，无需询问

**示例:** 缺少依赖、类型错误、导入错误

### RULE 4: 询问架构变更

**触发条件:** 修复需要重大结构性修改

**处理方式:** 必须使用 AskUserQuestion 确认

**示例:** 新数据库表、主要架构变更、切换框架

**使用 AskUserQuestion:**
```markdown
AskUserQuestion(
  questions: [
    {
      header: "确认",
      question: "修复需要[描述变更，如：添加新的数据库表]，这会改变系统架构，是否继续？",
      options: [
        {
          label: "继续执行",
          description: "按建议的架构变更继续执行"
        },
        {
          label: "只修复当前问题",
          description: "保持现有架构，只修复最小问题"
        },
        {
          label: "让我看看方案",
          description: "先查看详细的架构变更方案再做决定"
        }
      ],
      multiSelect: false
    }
  ]
)
```

**根据用户选择：**
- "继续执行"：按建议执行架构变更
- "只修复当前问题"：找到不改变架构的替代方案
- "让我看看方案"：先生成详细的变更方案，再让用户确认

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |
| Edit | SearchReplace |
| Bash | RunCommand |
| Glob | Glob |
| Grep | Grep |

## TDD 执行流

当任务标记 `tdd="true"`:

1. **红:** 先写失败的测试
2. **绿:** 写最少代码使测试通过
3. **重构:** 改进代码同时保持测试通过

## 最佳实践

1. **每个任务一个提交** - 原子性保证
2. **记录所有偏差** - 透明度
3. **验证每个任务** - 质量保证
4. **保持上下文更新** - 状态同步

## 与其他 Skills 的关系

- **输入**: gsd-planner 生成的 PLAN.md
- **输出**: SUMMARY.md
- **状态**: gsd-context (保存会话状态) 和代码提交
- **验证**: gsd-verifier 验证执行结果
- **可选**: gsd-codebase-mapper 可在执行过程中提供代码库上下文

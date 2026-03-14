---
name: gsd-plan-checker
description: 检查和验证 PLAN.md 的质量，确保计划完整、可执行、可验证。
---

# GSD Plan Checker - 计划检查技能

## 概述

GSD Plan Checker 负责检查和验证 PLAN.md 的质量，确保计划完整、可执行、可验证。

## 核心职责

1. **完整性检查** - 检查计划是否完整
2. **文件结构检查** - 检查是否有文件结构规划
3. **任务粒度检查** - 检查任务是否遵循超小粒度原则
4. **零上下文检查** - 检查计划是否满足零上下文假设
5. **可执行性检查** - 检查任务是否可执行
6. **可验证性检查** - 检查验证标准是否明确
7. **质量改进** - 提供改进建议
8. **Wave 执行检查** - 检查计划是否支持波次执行模型
9. **垂直切片检查** - 检查是否优先垂直切片而非水平分层
10. **Gap Closure 检查** - 检查修复计划是否包含 gap 修复任务

## 工作流程

### Step 1: 读取 PLAN.md 和 DESIGN.md

```
使用 Read 工具读取:
- .planning/phases/*/PLAN.md
- .planning/designs/YYYY-MM-DD-*-design.md (如存在)
```

### Step 1.5: 使用 TodoWrite 显示检查进度

**⚠️ 使用 TodoWrite 跟踪检查项**

使用 TodoWrite 显示检查进度，让用户直观看到检查状态：

```markdown
TodoWrite(
  todos: [
    {
      content: "完整性检查 - phase/goal/wave/Objective/Context/Tasks/Success Criteria",
      status: "pending",
      priority: "high"
    },
    {
      content: "文件结构检查 - File Structure 章节/Responsibility/Public API",
      status: "pending",
      priority: "high"
    },
    {
      content: "任务粒度检查 - 超小粒度/checkbox 格式/具体代码/运行命令",
      status: "pending",
      priority": "high"
    },
    {
      content: "零上下文检查 - 文件路径/命令/预期输出/TODO 检查",
      status: "pending",
      priority: "high"
    },
    {
      content: "可执行性检查 - 任务可执行性",
      status: "pending",
      priority: "medium"
    },
    {
      content: "可验证性检查 - 验证标准明确",
      status: "pending",
      priority: "medium"
    }
  ]
)
```

**检查过程中更新状态：**
- 开始检查时：更新为 `in_progress`
- 检查通过：更新为 `completed`
- 检查失败：添加问题描述到 content

**检查完成后：**
- 保留 TodoWrite 状态
- 在最终报告中引用检查结果

### Step 2: 完整性检查

检查以下项目：

```yaml
checklist:
  frontmatter:
    - [ ] phase 字段存在
    - [ ] goal 字段存在
    - [ ] wave 字段存在
  sections:
    - [ ] Objective 部分存在
    - [ ] Context 部分存在
    - [ ] Tasks 部分存在
    - [ ] Success Criteria 部分存在
    - [ ] Output Spec 部分存在
  tasks:
    - [ ] 至少 1 个任务
    - [ ] 每个任务有描述
    - [ ] 每个任务有类型
    - [ ] 每个任务有验证标准
```

### Step 3: 文件结构检查 (新增)

参考 superpowers plan-document-reviewer 的检查项：

```yaml
file_structure_check:
  - [ ] File Structure 章节存在
  - [ ] 每个文件有明确的 Responsibility
  - [ ] 每个文件有 Public API
  - [ ] 文件职责单一，不混合多层职责
  - [ ] 文件不太可能变得过大
```

### Step 4: 任务粒度检查 (新增)

参考 superpowers 的超小粒度原则：

```yaml
task_granularity_check:
  - [ ] 任务步骤是 checkbox 格式 (- [ ])
  - [ ] 每个步骤是单一动作 (2-5 分钟可完成)
  - [ ] 包含具体代码 (不是 "添加验证" 这种模糊描述)
  - [ ] 包含运行命令
  - [ ] 包含预期输出
  - [ ] 遵循 TDD 模式 (RED-GREEN-REFACTOR) 如果是 tdd 类型
```

### Step 5: 零上下文检查 (新增)

参考 superpowers 的零上下文假设：

```yaml
zero_context_check:
  - [ ] 计划假设执行者不了解项目代码库
  - [ ] 计划假设执行者不了解技术选型原因
  - [ ] 包含精确的文件路径
  - [ ] 包含具体的命令
  - [ ] 包含预期的输出
  - [ ] 没有 TODO 或 placeholder
  - [ ] 没有 "类似 X" 这种模糊描述
```

### Step 6: 可执行性检查

检查任务是否可执行：

```yaml
executability:
  - [ ] 任务描述具体明确
  - [ ] 任务大小适中
  - [ ] 依赖关系合理
  - [ ] 波次分配正确
  - [ ] 没有循环依赖
```

### Step 7: 可验证性检查

检查验证标准是否明确：

```yaml
verifiability:
  - [ ] 每个任务有明确的验证标准
  - [ ] 验证标准可测试
  - [ ] 验证标准不模糊
  - [ ] Success Criteria 可验证
```

### Step 8: 生成检查报告

```markdown
---
plan: P1-01
status: pass|fail|needs_review
score: 85
---

# Plan Check Report

## 计划信息
- **阶段:** P1
- **计划:** 01
- **目标:** [目标]

## 检查结果

### 完整性 ✓
- [x] frontmatter 完整
- [x] 所有 section 存在
- [x] 任务列表完整

### 文件结构检查 ✓ (新增)
- [x] File Structure 章节存在
- [x] 每个文件有明确职责
- [x] 文件职责单一

### 任务粒度检查 ✓ (新增)
- [x] 使用 checkbox 格式
- [x] 步骤是单一动作
- [x] 包含具体代码
- [x] 包含命令和预期输出

### 零上下文检查 ✓ (新增)
- [x] 假设执行者零上下文
- [x] 包含精确路径
- [x] 没有 TODO/placeholder

### 可执行性 ✓
- [x] 任务描述明确
- [x] 任务大小适中
- [x] 依赖关系合理

### 可验证性 ⚠
- [x] 每个任务有验证标准
- [ ] 验证标准可测试（部分模糊）
- [x] Success Criteria 完整

## 评分
- **总分:** 85/100
- **完整性:** 100%
- **文件结构:** 100%
- **任务粒度:** 100%
- **零上下文:** 100%
- **可执行性:** 100%
- **可验证性:** 70%

## 改进建议
1. T2 的验证标准需要更具体
2. 添加更多输出文件到 Output Spec

## 状态
**需要修订** - 请更新计划后重新检查
```

### Step 9: 提供反馈

向用户提供反馈：

| 评分 | 状态 | 反馈 |
|-----|------|------|
| 90-100 | ✓ 通过 | 计划质量很好，可以执行 |
| 70-89 | ⚠ 需要修订 | 计划基本可行，但有改进空间 |
| <70 | ✗ 失败 | 计划需要重大修订 |

## 审查要点总结

### 来自 superpowers plan-document-reviewer 的关键检查点：

| 类别 | 检查项 | 优先级 |
|------|--------|--------|
| Completeness | TODO、placeholder、不完整的任务 | 高 |
| Spec Alignment | 覆盖需求，无范围蔓延 | 高 |
| Task Decomposition | 任务原子性、清晰边界 | 高 |
| File Structure | 单一职责、按职责而非层级拆分 | 中 |
| File Size | 文件是否可能变得过大 | 中 |
| Task Syntax | checkbox 格式 (`- [ ]`) | 高 |
| Chunk Size | 每个 chunk ≤500 行 | 中 |

### 必须重点检查的内容：

1. **任何 TODO 标记或占位符文本**
2. **"类似 X" 而没有实际内容的步骤**
3. **不完整的任务定义**
4. **缺少验证步骤或预期输出**
5. **计划承担多项职责或可能变得难以维护的文件**

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |

## 最佳实践

1. **全面检查** - 完整性、文件结构、任务粒度、零上下文、可执行性、可验证性都检查
2. **量化评分** - 给计划打分，直观展示质量
3. **具体建议** - 提供可操作的改进建议
4. **迭代改进** - 支持多次检查和修订

## 审查循环规则

参考 superpowers writing-plans：

- 同一 agent 修复问题（保持上下文）
- 最多 5 次迭代
- 超过 5 次则提示用户人工介入
- 审查者只是建议，不遵守需解释原因

## 与其他 Skills 的关系

- **输入**: gsd-planner 生成的 PLAN.md
- **输出**: 检查报告和改进建议
- **上游**: gsd-planner 创建计划
- **下游**: 检查通过后 gsd-executor 执行

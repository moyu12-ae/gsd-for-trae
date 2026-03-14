---
name: gsd-verifier
description: 通过目标回溯分析验证阶段目标达成情况。检查代码库是否交付了阶段承诺，而非仅仅任务完成。生成 VERIFICATION.md 报告。
triggers:
  keywords:
    - 验证
    - verify
    - verifier
    - 完成
    - 检查是否完成
  prefixes:
    - "@gsd-verifier"
---

# GSD Verifier - 验证技能

## 概述

GSD Verifier 负责验证阶段是否达成了其目标，而不仅仅是完成了任务。采用目标回溯验证方法：从阶段应该交付的结果出发，验证实际存在于代码库中。

## 核心原则

**任务完成 ≠ 目标达成**

一个任务"创建聊天组件"可以在组件是占位符时标记为完成。任务已完成 - 文件已创建 - 但目标"工作的聊天界面"未达成。

目标回溯验证从结果反推：

1. 目标达成需要什么为真？
2. 这些真理需要什么存在？
3. 这些工件需要如何连接？

## 工作流程

### Step 0: 检查先前验证

```
使用 Read 工具读取:
- .planning/*-VERIFICATION.md (之前的验证报告)
```

如果有先前的验证且包含 `gaps:` 部分 → **重新验证模式**:
- 失败项目：完整 3 级验证
- 通过项目：快速回归检查

### Step 1: 加载上下文 (初始模式)

```
使用 Read 工具读取:
- .planning/ROADMAP.md (阶段数据)
- .planning/PLAN.md (计划)
- .planning/SUMMARY.md (执行总结)
```

从 ROADMAP.md 提取阶段目标 - 这是要验证的结果。

### Step 2: 建立必须项

**选项 A: 计划中的 must_haves**

从 PLAN.md frontmatter 提取：

```yaml
must_haves:
  truths:
    - "用户可以看到现有消息"
    - "用户可以发送消息"
  artifacts:
    - path: "src/components/Chat.tsx"
      provides: "消息列表渲染"
  key_links:
    - from: "Chat.tsx"
      to: "api/chat"
      via: "fetch in useEffect"
```

**选项 B: 从 ROADMAP 成功标准推导**

从 ROADMAP.md 提取 `success_criteria` 数组：
1. 使用每个成功标准直接作为真理
2. 为每个真理推导工件
3. 为每个工件推导关键链接

**选项 C: 从阶段目标推导 (回退)**

1. 陈述目标
2. 推导真理：3-7 个可观察、可测试的行为
3. 推导工件：映射到具体文件路径
4. 推导关键链接：这是存根隐藏的地方

### Step 3: 验证可观察真理

对于每个真理，确定代码库是否支持它。

**验证状态:**

- ✓ VERIFIED: 所有支持工件通过所有检查
- ✗ FAILED: 一个或多个工件缺失、存根或未连接
- ? UNCERTAIN: 无法程序化验证（需要人工）

### Step 4: 验证工件

对于每个工件，检查三个级别：

**级别 1: 存在**
```
使用 Glob 和 Read 工具确认文件存在
```

**级别 2: 实质性**
```
使用 Read 工具检查内容：
- 不是占位符
- 有实际实现
- 符合要求
```

**级别 3: 已连接**
```
使用 Grep 和 Read 工具检查：
- 导入正确
- 调用正确
- 数据流正确
```

### Step 5: 生成 VERIFICATION.md

```markdown
---
phase: P1
status: VERIFIED|FAILED|UNCERTAIN
gaps: 0|N
---

# Phase P1 验证报告

## 目标
[阶段目标]

## 必须项验证

### 真理
| 真理 | 状态 | 备注 |
|-----|------|------|
| 真理 1 | ✓ VERIFIED | - |

### 工件
| 工件 | 存在 | 实质性 | 已连接 | 状态 |
|-----|------|-------|--------|------|
| file.ts | ✓ | ✓ | ✓ | ✓ VERIFIED |

### 关键链接
| From | To | Via | 状态 |
|------|-----|-----|------|
| A.ts | B.ts | import | ✓ VERIFIED |

## 差距
[如果有，列出未达成的项目]

## 建议
[改进建议]
```

### Step 5.5: 验证结果反馈（使用 AskUserQuestion）

**⚠️ 验证完成后，询问用户结果反馈**

使用 AskUserQuestion 确认验证结果：

```markdown
AskUserQuestion(
  questions: [
    {
      header: "结果",
      question: "验证完成：[VERIFIED/FAILED/UNCERTAIN]。验证结果符合你的预期吗？",
      options: [
        {
          label: "符合预期",
          description: "验证通过，继续下一步"
        },
        {
          label: "查看详情",
          description: "让我详细解释验证结果"
        },
        {
          label: "生成报告",
          description: "将验证结果保存为 VERIFICATION.md"
        }
      ],
      multiSelect: false
    }
  ]
)
```

**根据用户选择：**
- "符合预期"：继续下一步或结束
- "查看详情"：解释验证结果的每个部分
- "生成报告"：确保 VERIFICATION.md 已保存到文件系统

### Step 6: 保存会话快照 (必须执行)

**⚠️ 重要：验证完成后必须保存会话状态**

使用 Write 工具将以下内容追加到 `.planning/SESSION.md`：

```markdown
## [{时间戳}] Verification Completed
**Phase:** [阶段名]
**Result:** [VERIFIED/FAILED/UNCERTAIN]
**Verification File:** .planning/VERIFICATION.md
**Next:** 等待用户下一步指示
```

## 验证检查表

**存在检查:**
- [ ] 文件存在于预期路径
- [ ] 文件名正确
- [ ] 文件扩展名正确

**实质性检查:**
- [ ] 文件不是空的
- [ ] 文件不是占位符
- [ ] 实现符合要求

**已连接检查:**
- [ ] 导入语句正确
- [ ] 函数调用存在
- [ ] 数据流正确

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |
| Bash | RunCommand |
| Grep | Grep |
| Glob | Glob |

## 最佳实践

1. **不要信任 SUMMARY.md 声明** - 验证实际存在
2. **验证所有三个级别** - 存在、实质性、已连接
3. **记录所有差距** - 透明度
4. **提供具体建议** - 可操作性

## Gap Closure 模式

### 概述

Gap Closure 模式允许在验证失败后自动生成修复计划，确保问题可以被闭环处理。

### 触发条件

当验证结果为 **FAILED** 且存在 `gaps:` 时，自动触发 Gap Closure 模式。

### Gap 解析

从 VERIFICATION.md 中解析 gaps：

```markdown
## 差距
- [Gap 1]: [描述]
- [Gap 2]: [描述]
```

每个 gap 包含：
- **truth**: 失败的行为
- **reason**: 失败原因
- **artifacts**: 有问题的文件
- **missing**: 需要添加/修复的内容

### Gap Closure 工作流程

#### Step 1: 解析 Gaps

解析 VERIFICATION.md 中的 gap 列表：
1. 读取失败的 truth
2. 识别失败原因
3. 确定需要修复的文件

#### Step 2: 生成修复计划

为每个 gap 生成修复任务：

```markdown
## Gap Closure Plan

### Gap 1: [gap 描述]

**原因**: [gap reason]

**修复任务**:
- Task 1: [修复任务描述]
  - Files: [需要修改的文件]
  - Action: [具体修复动作]
  - Verify: [验证命令]

### Gap 2: [gap 描述]
...
```

#### Step 3: 创建新计划

在 `.planning/phases/XX-name/` 目录创建 Gap Closure 计划：

```markdown
---
phase: XX-name
plan: NN
type: execute
wave: N
gap_closure: true
---

<objective>
修复验证中发现的 gaps
</objective>

<tasks>
[修复任务列表]
</tasks>
```

#### Step 4: 执行修复计划

执行完成后：
1. 重新运行验证
2. 确认所有 gaps 已修复
3. 标记验证为 PASSED

### --gaps-only 标志

支持 `--gaps-only` 执行标志来只执行 Gap Closure 计划：

```bash
/gsd:execute-phase --gaps-only
```

这将：
1. 查找所有标记为 `gap_closure: true` 的计划
2. 只执行这些修复计划
3. 不执行其他计划

## 与其他 Skills 的关系

- **输入**: gsd-executor 的执行结果
- **输出**: VERIFICATION.md 报告
- **状态**: gsd-context (保存会话状态)
- **失败时**: 触发 gsd-debugger 或 gsd-planner 修订
- **可选**: gsd-codebase-mapper 可提供代码库上下文辅助验证

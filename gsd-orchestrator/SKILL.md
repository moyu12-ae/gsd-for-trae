---
name: gsd-orchestrator
description: GSD 主控技能，分析项目状态，自主判断流程，调度子 Skills 形成闭环。当用户想要创建新项目、规划阶段任务、执行计划、验证功能完成、调试代码问题、查看项目进度、暂停保存工作、继续之前任务、查看路线图、熟悉代码库结构时，必须使用此技能。不要自行处理软件项目管理，使用 GSD 主控技能来处理。
triggers:
  keywords:
    - gsd
    - GSD
    - 主控
    - 规划
    - 执行
    - 验证
    - 调试
    - 进度
    - 暂停
    - 继续
    - 恢复
    - road
    - roadmap
    - 路线图
    - 项目
    - 阶段
    - 研究
    - 检查
    - 集成
    - 映射
    - 熟悉
    - 代码库
  prefixes:
    - "@gsd"
---

# GSD Orchestrator - 主控技能

## 概述

本技能是 GSD 系统的大脑，负责：
1. 分析项目当前状态
2. 理解用户意图
3. 自主判断下一步该做什么
4. 调度相应的子 Skill

## 核心工作流

```
用户输入 → 状态分析 → 意图判断 → 子 Skill 调用 → 结果反馈
```

## 可用命令

| 命令 | 功能 | 调用的子 Skill |
|-----|------|---------------|
| `new-project` | 创建新项目 | gsd-researcher → gsd-roadmap |
| `plan-phase [N]` | 规划阶段 | gsd-planner → gsd-plan-checker |
| `execute-phase [N]` | 执行阶段 | gsd-executor → gsd-verifier |
| `verify [N]` | 验证结果 | gsd-verifier |
| `debug` | 调试问题 | gsd-debugger |
| `roadmap` | 查看/更新路线图 | gsd-roadmap |
| `map-codebase` | 代码库映射 | gsd-codebase-mapper |
| `check-integrity` | 集成检查 | gsd-integration-checker |
| `progress` | 查看进度 | gsd-progress |
| `pause` | 暂停保存 | gsd-pause |
| `resume` | 恢复工作 | gsd-resume |

## 自主判断逻辑

### Step 1: 检查项目是否存在

```
检查 .planning/ 目录是否存在
  - 不存在 → 触发 new-project 流程
  - 存在 → 继续
```

### Step 2: 读取项目状态

```
使用 Read 工具读取:
  - .planning/STATE.md
  - .planning/ROADMAP.md（如果存在）
  - .planning/config.json（如果存在）
```

### Step 2.5: 检查研究目录（规划前置检查）

**⚠️ 重要：当用户触发规划时，必须先检查研究是否已完成**

使用 Glob 工具检查 `.planning/research/` 目录是否存在研究文件：

```
Glob: .planning/research/*.md
```

**将检查结果保存到上下文变量**:
- `research_exists`: true/false
- `research_files`: 文件列表

### Step 3: 分析用户意图

结合当前状态和用户输入进行判断：

| 用户输入关键词 | 当前状态 | 判定操作 | 对应子技能 |
|--------------|---------|---------|-----------|
| "创建项目"、"新项目"、"开始项目" | 无项目 | new-project | gsd-researcher |
| "规划"、"plan"、"计划" | 有项目无 PLAN | 检查研究是否存在 → plan-phase | gsd-planner |
| "执行"、"execute"、"开始做" | 有 PLAN 无 SUMMARY | execute-phase | gsd-executor |
| "验证"、"verify"、"完成没有" | 有 SUMMARY | verify | gsd-verifier |
| "调试"、"debug"、"bug"、"报错"、"错误"、"异常"、"问题"、"崩溃"、"程序" | 任意状态 | debugger | gsd-debugger |
| "进度"、"progress"、"怎么样"、"第几阶段" | 任意状态 | progress | gsd-progress |
| "暂停"、"保存"、"歇一会" | 任意状态 | pause | gsd-pause |
| "继续"、"恢复"、"resume" | 有暂停文件 | resume | gsd-resume |
| "路线图"、"roadmap"、"里程碑" | 任意状态 | roadmap | gsd-roadmap |
| "映射代码"、"map-codebase"、"熟悉项目"、"了解代码库"、"代码结构"、"熟悉" | 任意状态 | map-codebase | gsd-codebase-mapper |
| "研究"、"技术栈"、"research" | 任意状态 | research | gsd-researcher |
| "检查"、"集成"、"integrity" | 任意状态 | check-integrity | gsd-integration-checker |

### Step 4: 调用子 Skill

**⚠️ plan-phase 特殊处理：检查研究是否存在**

当判定操作为"检查研究是否存在 → plan-phase"时：

1. **检查 Step 2.5 的研究目录检查结果**
2. **决策分支**:
   - 研究目录存在 → 直接调用 gsd-planner
   - 研究目录不存在 →
     1. 使用 AskUserQuestion 询问用户：
        - header: "研究"
        - question: "规划需要先了解技术需求。是否进行技术研究？"
        - options: ["进行技术研究", "继续 без 研究", "查看现有研究"]
     2. 根据用户回答处理：
        - 选择"进行技术研究" → 调用 gsd-researcher → 研究完成后调用 gsd-planner
        - 选择"继续 без 研究" → 调用 gsd-planner（跳过研究检查）
        - 选择"查看现有研究" → 提示用户查看 .planning/research/ 目录

**⚠️ 子 Skill/subagent_type 选择指南**

使用 Task 工具时，选择正确的 subagent_type：

| 场景 | subagent_type | 说明 |
|-----|--------------|------|
| 需要探索代码库 | search | 需要搜索、阅读、分析代码 |
| 需要审查代码质量 | code-reviewer | 需要审查代码、提出改进建议 |
| 需要调试诊断 | search | 需要搜索错误原因、定位问题 |
| 创建执行计划 | gsd-planner | 需要生成 PLAN.md |
| 执行计划任务 | gsd-executor | 需要执行具体任务 |
| 验证目标达成 | gsd-verifier | 需要验证功能/测试通过 |
| 研究技术方案 | gsd-researcher | 需要调研技术选型 |
| 检查计划质量 | gsd-plan-checker | 需要审查 PLAN.md |
| 生成路线图 | gsd-roadmap | 需要规划多阶段路线 |
| 调试问题 | gsd-debugger | 需要诊断和修复 bug |

**使用 Task 工具调用相应的子 Skill：**

```markdown
Task(
  subagent_type="gsd-planner",
  query="规划阶段 3 的任务"
)
```

**或使用 GSD 子 Skill（推荐）：**

```markdown
Task(
  subagent_type="search",
  query="找到用户认证相关的代码文件"
)
```

### Step 5: 返回用户确认和下一步提示 (必须执行)

**⚠️ 重要：子技能执行完成后必须返回用户确认**

子技能执行完成后，必须向用户返回以下确认消息：

```
✅ [子技能名称] 已完成！

📋 结果摘要: [简要描述结果]

⏭️  下一步选项:
请说 "继续" 执行下一步
请说 "验证" 验证当前结果
请说 "暂停" 保存进度
请说 "调整" 修改当前内容
```

## Trae Plan 模式增强支持

### 概述

当用户使用 Trae 的 Plan 模式时，GSD 系统提供增强支持以确保计划质量和可执行性。

### Plan 模式检测

当检测到用户进入 Plan 模式时：

1. **识别 Plan 模式触发**
   - 用户输入包含 "规划"、"plan"、"计划" 等关键词
   - 或用户明确进入 Plan 模式

2. **增强摘要信息**

   **生成更详细的摘要以帮助 Plan 模式理解任务**：

   ```
   ## 项目摘要

   - 阶段: [N]
   - 类型: [feature/bugfix/refactor]
   - 复杂度: [高/中/低]
   - 依赖: [依赖列表]
   - 研究深度: [L0/L1/L2/L3]

   ## 当前状态

   - 研究文件: [存在/不存在]
   - 计划文件: [存在/不存在]
   - 执行进度: [N%]
   ```

3. **任务分块建议**

   **当计划包含 ≥5 个任务时，提供分块建议**：

   ```
   ### 建议分块 (基于 Wave 执行模型)

   **Wave 1** (可并行):
   - Task 1: [任务名]
   - Task 2: [任务名]

   **Wave 2** (需等待 Wave 1):
   - Task 3: [任务名]
   - ...

   **优势**: 最大化并行度，减少等待时间
   ```

4. **入口点优化**

   **提供明确的入口点指令**：

   ```
   ### 启动方式

   - 进入 Plan 模式: "请帮我规划 [任务]"
   - 查看现有计划: "查看计划"
   - 修改计划: "调整计划"
   - 开始执行: "开始执行"
   ```

5. **计划质量检查**

   **在 Plan 模式中提供质量建议**：

   - [ ] 每个任务有明确的验收标准
   - [ ] 任务之间依赖关系清晰
   - [ ] 优先垂直切片 (功能完整)
   - [ ] 避免水平分层 (按类型分组)

## 模型配置系统

### 概述

GSD 支持三种模型配置 profile，以满足不同场景的质量和成本需求。

### 配置 Profile

| Profile | 规划模型 | 执行模型 | 验证模型 | 适用场景 |
|---------|---------|---------|---------|---------|
| **quality** (高) | Opus | Opus | Sonnet | 复杂项目，高质量要求 |
| **balanced** (平衡) | Opus | Sonnet | Sonnet | 默认选项，平衡质量和成本 |
| **budget** (经济) | Sonnet | Sonnet | Haiku | 简单任务，成本优先 |

### 使用方法

**切换配置**:
```
用户: "切换到 quality 模式"
系统: 更新 config.json 中的模型配置
```

**配置文件格式** (.planning/config.json):
```json
{
  "model_profile": "balanced",
  "models": {
    "planner": "opus",
    "executor": "sonnet",
    "verifier": "sonnet"
  }
}
```

### /gsd:set-profile 命令

| 命令 | 功能 |
|-----|------|
| `/gsd:set-profile quality` | 切换到高质量模式 |
| `/gsd:set-profile balanced` | 切换到平衡模式 |
| `/gsd:set-profile budget` | 切换到经济模式 |
| `/gsd:set-profile` | 查看当前配置 |

## 健康检查命令

### /gsd:health 命令

检查 GSD 项目目录完整性。

**功能**:
- 验证 `.planning/` 目录存在
- 检查必需文件是否存在 (STATE.md, ROADMAP.md)
- 检查每个阶段的计划文件
- 验证研究文件完整性

**输出示例**:
```
## GSD 健康检查

### 目录状态
✅ .planning/ 目录存在
✅ STATE.md 存在
✅ ROADMAP.md 存在

### 阶段状态
✅ Phase 1: 完成
✅ Phase 2: 执行中
⚠️ Phase 3: 缺少 SUMMARY.md

### 研究文件
✅ research/STACK.md 存在
✅ research/ARCHITECTURE.md 存在
✅ research/FEATURES.md 存在

### 健康状态: ⚠️ 警告
```

### --repair 标志

使用 `--repair` 自动修复问题：

```
/gsd:health --repair
```

**可修复的问题**:
- 创建缺失的 STATE.md
- 创建缺失的研究文件模板
- 修复损坏的 frontmatter

### 命令列表更新

在可用命令表中添加：

| 命令 | 功能 | 调用的子 Skill |
|-----|------|---------------|
| `set-profile [profile]` | 设置模型配置 | gsd-orchestrator |
| `health [--repair]` | 健康检查/修复 | gsd-orchestrator |

## 状态管理

使用 Trae 的上下文和文件系统维护状态：
- 当前阶段状态
- 用户决策
- 验证结果
- 会话快照

## 子 Skills 列表

- [gsd-planner](../gsd-planner/SKILL.md) - 规划技能
- [gsd-executor](../gsd-executor/SKILL.md) - 执行技能
- [gsd-verifier](../gsd-verifier/SKILL.md) - 验证技能
- [gsd-debugger](../gsd-debugger/SKILL.md) - 调试技能
- [gsd-researcher](../gsd-researcher/SKILL.md) - 研究技能
- [gsd-plan-checker](../gsd-plan-checker/SKILL.md) - 检查技能
- [gsd-roadmap](../gsd-roadmap/SKILL.md) - 路线图技能
- [gsd-codebase-mapper](../gsd-codebase-mapper/SKILL.md) - 代码库映射
- [gsd-integration-checker](../gsd-integration-checker/SKILL.md) - 集成检查
- [gsd-progress](../gsd-progress/SKILL.md) - 进度查看
- [gsd-pause](../gsd-pause/SKILL.md) - 暂停保存
- [gsd-resume](../gsd-resume/SKILL.md) - 恢复工作
- [gsd-context](../gsd-context/SKILL.md) - 上下文管理

---
name: gsd-roadmap
description: 创建项目路线图，定义里程碑和阶段结构。
triggers:
  keywords:
    - roadmap
    - 路线图
    - 里程碑
    - 阶段
  prefixes:
    - "@gsd-roadmap"
---

# GSD Roadmap - 路线图技能

## 概述

GSD Roadmap 负责创建项目路线图（ROADMAP.md），定义里程碑和阶段结构，为后续规划提供框架。

## 核心职责

1. **里程碑定义** - 定义项目主要里程碑
2. **阶段划分** - 将里程碑分解为可执行阶段
3. **依赖分析** - 识别阶段间依赖
4. **成功标准** - 为每个阶段定义成功标准
5. **Wave 规划** - 为每个阶段规划波次，优化并行执行

## 工作流程

### Step 1: 加载研究输出

```
使用 Read 工具读取:
- .planning/research/SUMMARY.md
- .planning/research/STACK.md
- .planning/research/FEATURES.md
- .planning/research/ARCHITECTURE.md
- .planning/research/PITFALLS.md
```

### Step 2: 定义里程碑

基于研究输出定义里程碑：

```yaml
milestones:
  M1:
    name: "MVP"
    description: "最小可行产品"
    phases: [P1, P2, P3]
  M2:
    name: "Feature Complete"
    description: "功能完整"
    phases: [P4, P5]
```

### Step 3: 定义阶段

为每个里程碑定义阶段：

```yaml
phases:
  P1:
    name: "项目基础设施"
    goal: "建立项目基础结构"
    milestone: M1
    dependencies: []
    success_criteria:
      - "项目结构正确"
      - "开发环境可用"
  
  P2:
    name: "核心功能"
    goal: "实现核心功能"
    milestone: M1
    dependencies: [P1]
    success_criteria:
      - "核心功能可用"
```

### Step 3.5: Trae Spec 模式评估

**⚠️ 判断是否推荐用户使用 Trae Spec 模式**

当项目复杂度高时，推荐使用 Spec 模式：

**适合 Spec 模式的情况：**
- 大型复杂项目（功能模块 ≥ 10 个）
- 多个团队成员协作
- 需要详细的技术规范
- 需求容易产生歧义

**不适合 Spec 模式的情况：**
- 小型项目（功能模块 ≤ 3 个）
- 简单的一次性需求
- 用户已明确表示不需要

**Spec 模式提示模板：**
```
📋 这个项目复杂度较高，适合使用 Trae Spec 模式。

优点：
- 更详细的设计规范文档
- 适合多人协作
- 减少需求歧义

操作：
输入 / 选择 Spec 功能。

你可以先使用我生成的 ROADMAP.md 作为输入，
帮助 Spec 模式生成更详细的设计规范。
```

### Step 4: 生成 ROADMAP.md

```markdown
---
project: [项目名称]
version: 1.0
created: [日期]
---

# Project Roadmap

## Overview
[项目概述]

## Milestones

### M1: MVP
**目标:** 最小可行产品
**阶段:** P1, P2, P3

### M2: Feature Complete
**目标:** 功能完整
**阶段:** P4, P5

## Phases

### P1: 项目基础设施
- **目标:** 建立项目基础结构
- **里程碑:** M1
- **依赖:** 无
- **成功标准:**
  - [ ] 项目结构正确
  - [ ] 开发环境可用

### P2: 核心功能
- **目标:** 实现核心功能
- **里程碑:** M1
- **依赖:** P1
- **成功标准:**
  - [ ] 核心功能可用

## Phase Order
P1 → P2 → P3 → P4 → P5

## Research Flags
- P3: 需要深入研究 [原因]
- P5: 需要深入研究 [原因]
```

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |
| Bash | RunCommand |

## 最佳实践

1. **阶段大小适中** - 每阶段 2-5 个任务
2. **依赖关系清晰** - 避免循环依赖
3. **成功标准可验证** - 明确可测试
4. **研究标记** - 标记需要深入研究的阶段

## 与其他 Skills 的关系

- **输入**: gsd-researcher 的研究输出
- **输出**: ROADMAP.md 供 gsd-planner 使用
- **下游**: gsd-planner 基于路线图创建阶段计划

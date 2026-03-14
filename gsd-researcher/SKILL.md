---
name: gsd-researcher
description: 进行项目技术研究，收集技术栈、架构、功能、陷阱等信息，生成研究报告。
triggers:
  keywords:
    - research
    - 研究
    - 技术栈
    - 调研
  prefixes:
    - "@gsd-researcher"
---

# GSD Researcher - 研究技能

## 概述

GSD Researcher 负责进行项目技术研究，收集技术栈、架构、功能、陷阱等信息，生成研究报告供 gsd-roadmap 使用。

## 核心职责

1. **技术栈研究** - 分析和推荐技术栈选择
2. **功能研究** - 收集和分析功能需求
3. **架构研究** - 设计和验证系统架构
4. **陷阱研究** - 识别和记录潜在陷阱

## 工作流程

### Step 1: 理解项目需求

```
与用户对话，收集：
- 项目类型（新项目/现有项目/维护项目）
- 核心功能需求
- 约束条件（时间、预算、技术栈）
- 用户偏好
```

### Step 2: 技术栈研究

收集和分析技术栈选项：

```yaml
stack:
  frontend:
    - React
    - Vue
    - Svelte
  backend:
    - Node.js
    - Python
    - Go
  database:
    - PostgreSQL
    - MongoDB
    - SQLite
```

生成 `.planning/research/STACK.md`

### Step 3: 功能研究

收集和分析功能需求：

```yaml
features:
  must_have:
    - 用户认证
    - 数据管理
  should_have:
    - 通知系统
  could_have:
    - 高级搜索
  wont_have:
    - AI 集成 (v2)
```

生成 `.planning/research/FEATURES.md`

### Step 4: 架构研究

设计和验证系统架构：

```yaml
architecture:
  layers:
    - presentation
    - application
    - infrastructure
  components:
    - Auth Service
    - Data Service
    - API Gateway
  data_flow:
    - Request → API → Service → Database
```

生成 `.planning/research/ARCHITECTURE.md`

### Step 5: 陷阱研究

识别和记录潜在陷阱：

```yaml
pitfalls:
  - name: "状态管理复杂度"
    risk: "high"
    mitigation: "使用 Redux 或 Zustand"
  - name: "性能瓶颈"
    risk: "medium"
    mitigation: "提前进行性能测试"
```

生成 `.planning/research/PITFALLS.md`

### Step 6: 生成研究总结

生成 `.planning/research/SUMMARY.md`：

```markdown
---
project: [项目名称]
date: [日期]
---

# 研究总结

## 技术栈建议
[技术栈选择和理由]

## 功能优先级
[功能优先级分析]

## 架构设计
[架构概述]

## 潜在陷阱
[陷阱和缓解措施]

## 下一步
- 创建 ROADMAP.md
- 开始阶段规划
```

### Step 7: 返回用户确认（必须执行）

**⚠️ 重要：研究完成后必须返回用户确认**

研究完成后，必须向用户返回以下确认消息：

```
✅ 技术研究已完成！

📋 研究文件: .planning/research/
- STACK.md (技术栈)
- FEATURES.md (功能)
- ARCHITECTURE.md (架构)
- PITFALLS.md (陷阱)
- SUMMARY.md (总结)

⏭️  下一步:
请说 "规划" 开始创建执行计划
请说 "查看研究" 查看具体研究内容
```

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Write | Write |
| WebSearch | WebSearch |
| WebFetch | WebFetch |

## 最佳实践

1. **多维度研究** - 技术栈、功能、架构、陷阱全覆盖
2. **提供选项** - 不只有一个答案，提供多个选项和对比
3. **记录理由** - 每个建议都要有明确的理由
4. **识别风险** - 提前识别潜在问题和陷阱

## 与其他 Skills 的关系

- **输入**: 用户需求和项目描述
- **输出**: 研究报告供 gsd-roadmap 使用
- **下游**: gsd-roadmap 基于研究创建路线图

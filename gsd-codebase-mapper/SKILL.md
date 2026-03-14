---
name: gsd-codebase-mapper
description: 分析和映射代码库结构，生成架构、技术栈、代码质量、技术债务等报告。支持验证现有结构、全新映射和增量更新三种模式。
triggers:
  keywords:
    - 代码库
    - 映射
    - 熟悉
    - codebase
    - 代码结构
    - 架构
  prefixes:
    - "@gsd-codebase-mapper"
---

# GSD Codebase Mapper - 代码库映射技能

## 概述

GSD Codebase Mapper 负责分析和映射代码库结构，生成架构、技术栈、代码质量、技术债务等报告。

## 核心职责

1. **架构分析** - 分析代码库架构
2. **技术栈识别** - 识别使用的技术栈
3. **代码质量评估** - 评估代码质量
4. **技术债务识别** - 识别技术债务
5. **报告生成** - 生成代码库映射报告

## 三种工作模式

### 模式 1: 验证现有结构（推荐首选）

**适用场景**: 接手一个可能已有 GSD 结构的项目

**操作步骤**:

1. **检查 .planning/codebase/ 目录是否存在**
   ```
   LS path="项目根目录/.planning/codebase"
   ```

2. **对照清单验证文件**
   - [ ] **STACK.md** - 技术栈文档
   - [ ] **ARCHITECTURE.md** - 架构文档
   - [ ] **STRUCTURE.md** - 目录结构文档
   - [ ] **CONVENTIONS.md** - 代码约定文档
   - [ ] **INTEGRATIONS.md** - 集成文档
   - [ ] **CONCERNS.md** - 关注点文档
   - [ ] **TESTING.md** - 测试文档

3. **快速浏览内容**
   读取每个文件的前几行，确认内容有意义

4. **报告结果**
   - 如果所有文件都存在且内容良好 → "项目已完全符合 GSD 结构"
   - 如果有缺失 → 列出缺失的文件，进入模式 3

---

### 模式 2: 全新映射（新项目）

**适用场景**: 项目没有 .planning/codebase/ 目录

**操作步骤**:

#### Step 1: 扫描代码库

```
使用 Glob 和 Grep 工具扫描:
- 项目根目录结构
- 配置文件 (package.json, requirements.txt, pyproject.toml, etc.)
- 源代码目录
- 测试目录
- 文档
```

#### Step 2: 技术栈识别

识别使用的技术：

```yaml
tech_stack:
  frontend:
    - React
    - TypeScript
    - Tailwind CSS
  backend:
    - Node.js
    - Express
    - PostgreSQL
  tools:
    - Git
    - Docker
    - Jest
```

生成 `.planning/codebase/STACK.md`

#### Step 3: 架构分析

分析代码库架构：

```yaml
architecture:
  layers:
    - src/
      - components/
      - hooks/
      - services/
      - utils/
    - tests/
    - docs/
  patterns:
    - Component-based
    - Hooks
    - Service layer
  dependencies:
    - High: React, TypeScript
    - Medium: Tailwind, Express
    - Low: 其他工具
```

生成 `.planning/codebase/ARCHITECTURE.md`

#### Step 4: 目录结构分析

生成 `.planning/codebase/STRUCTURE.md`，详细列出源代码目录结构。

#### Step 5: 代码约定分析

生成 `.planning/codebase/CONVENTIONS.md`，分析命名规范、代码风格、设计模式。

#### Step 6: 集成分析

生成 `.planning/codebase/INTEGRATIONS.md`，识别 API、数据库、认证方式。

#### Step 7: 关注点分析

生成 `.planning/codebase/CONCERNS.md`，识别技术债务、安全考虑、性能问题。

#### Step 8: 测试分析

生成 `.planning/codebase/TESTING.md`，分析测试框架、结构、命令。

---

### 模式 3: 增量更新

**适用场景**: 项目已有部分 codebase 文件，但不完整

**操作步骤**:

1. **检查现有文件**
   列出 .planning/codebase/ 中已存在的文件

2. **识别缺失文件**
   对照 7 个必需文件清单，找出缺失的

3. **只生成缺失的文件**
   按照模式 2 的步骤，只生成缺失的文件

4. **可选: 更新过时文件**
   如果某些文件内容明显过时，可以更新

---

## 快速决策流程

```
用户说"熟悉项目"或"了解代码库"
    ↓
检查 .planning/codebase/ 是否存在？
    ↓
    ├─ 不存在 → 模式 2: 全新映射
    │
    └─ 存在 → 检查是否完整？
              ↓
              ├─ 完整 → 报告"已符合 GSD 结构"
              │
              └─ 不完整 → 模式 3: 增量更新
```

## 工具映射

| GSD 工具 | Trae 实现 |
|---------|----------|
| Read | Read |
| Glob | Glob |
| Grep | Grep |

## 最佳实践

1. **总是先验证**: 不要直接开始生成，先检查是否已有结构
2. **使用现有文件**: 如果项目已有合理的 codebase 文件，不要覆盖
3. **保持一致**: 生成的文件风格要与现有文件保持一致
4. **内容要具体**: 不要泛泛而谈，要基于实际代码分析
5. **中英文对照**: 如果项目有中英文文档，codebase 文件也可以双语

## 与其他 Skills 的关系

- **输入**: 代码库文件
- **输出**: 代码库映射报告
- **辅助**: 为 gsd-planner、gsd-executor、gsd-verifier 提供上下文

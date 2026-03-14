# GSD Codebase Mapper 域参考

## 探索域定义

### 1. tech-stack (技术栈)

**输出文件:** `.gsd/codebase/tech-stack.md`

**探索内容:**
- 语言和版本
- 框架和库
- 构建工具
- 开发工具

**模板:**
```markdown
# Tech Stack Analysis

## Languages
| Language | Version | Usage |
|----------|---------|-------|
| TypeScript | 5.x | Main language |
| JavaScript | ES2022 | Config files |

## Frameworks
| Framework | Version | Purpose |
|-----------|---------|---------|
| React | 18.x | Frontend |
| Express | 4.x | Backend |

## Build Tools
- Package Manager: npm/pnpm/yarn
- Bundler: webpack/vite/esbuild
- Task Runner: npm scripts

## Development Tools
- Linter: ESLint
- Formatter: Prettier
- Testing: Jest/Vitest
```

### 2. architecture (架构)

**输出文件:** `.gsd/codebase/architecture.md`

**探索内容:**
- 目录结构
- 模块划分
- 依赖关系
- 数据流

**模板:**
```markdown
# Architecture Analysis

## Directory Structure
```
project/
├── src/
│   ├── components/    # UI 组件
│   ├── services/      # 业务逻辑
│   ├── models/        # 数据模型
│   └── utils/         # 工具函数
├── tests/
└── docs/
```

## Module Dependencies
[模块依赖图]

## Data Flow
[数据流描述]

## Key Patterns
- Pattern 1: [描述]
- Pattern 2: [描述]
```

### 3. code-quality (代码质量)

**输出文件:** `.gsd/codebase/code-quality.md`

**探索内容:**
- 代码风格一致性
- 测试覆盖率
- 类型安全
- 文档完整性

### 4. tech-debt (技术债务)

**输出文件:** `.gsd/codebase/tech-debt.md`

**探索内容:**
- TODO/FIXME 数量和分布
- 过时依赖
- 已知问题
- 改进建议

## 输出规范

1. **直接写入文件** - 不返回大量内容
2. **结构化格式** - 使用表格和列表
3. **可操作建议** - 提供具体改进建议
4. **引用路径** - 方便其他 Skills 引用

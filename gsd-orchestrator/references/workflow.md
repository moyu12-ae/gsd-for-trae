# GSD Orchestrator 工作流

## 完整工作流程

### Phase 1: 新项目初始化

```
用户请求 → 解析意图 → 创建 .planning/ 目录结构 → 调用 gsd-researcher → 创建 ROADMAP.md
```

**步骤：**

1. **解析用户请求**
   - 识别项目类型（新项目/现有项目/维护项目）
   - 提取关键需求
   - 识别约束条件

2. **创建目录结构**
   ```bash
   mkdir -p .planning/phases
   ```

3. **调用研究技能**
   - 加载 gsd-researcher skill
   - 收集技术栈信息
   - 生成 RESEARCH.md

4. **创建路线图**
   - 加载 gsd-roadmap skill
   - 分解项目阶段
   - 创建 ROADMAP.md

### Phase 2: 阶段规划

```
阶段请求 → 读取 ROADMAP → 调用 gsd-planner → 调用 gsd-plan-checker → 生成 PLAN.md
```

**步骤：**

1. **加载阶段信息**
   - 从 ROADMAP.md 读取阶段目标
   - 从 STATE.md 读取当前位置

2. **调用规划技能**
   - 加载 gsd-planner skill
   - 分解任务
   - 生成 PLAN.md

3. **验证计划质量**
   - 加载 gsd-plan-checker skill
   - 运行完整性检查
   - 如需修订，循环回规划

### Phase 3: 计划执行

```
执行请求 → 读取 PLAN.md → 调用 gsd-executor → 处理偏差 → 生成 SUMMARY.md
```

**步骤：**

1. **加载计划**
   - 解析 PLAN.md frontmatter
   - 确定执行模式

2. **执行任务**
   - 按波次执行
   - 每任务验证
   - 处理偏差（遵循规则）

3. **生成摘要**
   - 记录完成状态
   - 记录偏差
   - 更新 STATE.md

### Phase 4: 结果验证

```
验证请求 → 读取阶段目标 → 调用 gsd-verifier → 目标反向验证 → 生成 VERIFICATION.md
```

**步骤：**

1. **建立 Must-Haves**
   - 从 PLAN.md 或 ROADMAP.md 提取
   - 识别 truths、artifacts、key_links

2. **三级验证**
   - 存在性验证
   - 实质性验证
   - 连接性验证

3. **生成报告**
   - 记录通过/失败状态
   - 识别差距
   - 提供修复建议

### Phase 5: 问题调试

```
调试请求 → 收集症状 → 调用 gsd-debugger → 假设测试 → 找到根因
```

**步骤：**

1. **收集证据**
   - 用户描述症状
   - 收集错误日志
   - 识别相关代码

2. **形成假设**
   - 生成多个假设
   - 按可能性排序

3. **测试假设**
   - 设计实验
   - 验证/反驳假设

4. **修复问题**
   - 实现最小修复
   - 添加回归测试

## 状态机

```
[初始] → [研究] → [规划] → [执行] → [验证]
                       ↑           ↓
                       └── [调试] ←┘
```

## 文件引用规范

### 规划阶段

```
读取: .planning/ROADMAP.md, .planning/CONTEXT.md, .planning/STATE.md
写入: .planning/phases/XX-name/XX-NN-PLAN.md
```

### 执行阶段

```
读取: .planning/phases/XX-name/XX-NN-PLAN.md
写入: 项目文件, .planning/STATE.md, .planning/phases/XX-name/XX-NN-SUMMARY.md
```

### 验证阶段

```
读取: .planning/phases/XX-name/XX-NN-PLAN.md, 项目文件
写入: .planning/phases/XX-name/XX-NN-VERIFICATION.md
```

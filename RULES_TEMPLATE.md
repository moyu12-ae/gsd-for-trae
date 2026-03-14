# GSD 工作流门控与标准化 Rules

> 此文件包含 GSD 工作流的门控规则和执行标准，聚焦于"什么时候该做什么"以及"做什么的时候应该是什么标准"。

---

## 核心原则

1. **门控优先** - 每个阶段都有明确的进入和退出条件
2. **状态驱动** - 根据 .planning/ 目录的状态决定下一步
3. **标准化执行** - 每个环节都有固定的检查清单
4. **条件触发** - 满足条件时自动触发相应动作

---

## 工作流门控规则

### 1. 项目初始化门控

```
# 新项目初始化门控

## 进入条件
- 当前目录不存在 .planning/ 目录
- 用户输入包含"创建项目"、"新项目"、"开始项目"等关键词

## 必须执行的步骤
1. 检查是否存在研究目录 → 如不存在，先触发研究
2. 研究完成后，生成 ROADMAP.md
3. 创建 .planning/STATE.md 初始化状态
4. 创建 .planning/config.json 配置文件

## 退出条件
- .planning/ 目录存在且结构完整
- ROADMAP.md 已生成
- STATE.md 包含初始状态
```

### 2. 规划阶段门控

```
# 规划阶段门控

## 进入条件
- .planning/ 目录存在
- .planning/ROADMAP.md 存在
- 当前阶段没有 PLAN.md（或用户明确要求重新规划）
- ⚠️ 必须：.planning/research/ 目录存在且有研究文件

## 必须执行的检查清单
- [ ] 研究文件存在检查
- [ ] ROADMAP.md 当前阶段解析
- [ ] CONTEXT.md 用户决策读取
- [ ] 文件结构规划完成
- [ ] 任务分解到 2-5 分钟粒度
- [ ] 依赖关系和波次分配
- [ ] 目标回溯验证
- [ ] 计划审查（最多 5 次迭代）

## 输出标准
- PLAN.md 必须包含 frontmatter
- PLAN.md 必须包含 File Structure 表格
- PLAN.md 每个任务必须有 Files、Steps、Verification
- 必须同时生成 DESIGN.md

## 退出条件
- PLAN.md 已写入 .planning/phases/XX/
- DESIGN.md 已生成
- 计划审查已通过
```

### 3. 执行阶段门控

```
# 执行阶段门控

## 进入条件
- .planning/phases/XX/PLAN.md 存在
- 用户输入包含"执行"、"execute"、"开始做"等关键词
- 当前阶段没有 SUMMARY.md（或用户明确要求重新执行）

## 必须执行的检查清单
- [ ] PLAN.md 解析完成
- [ ] 波次计算完成
- [ ] TodoWrite 初始化
- [ ] Git 分支策略应用
- [ ] 每个任务执行后两阶段审查
- [ ] 偏差规则自动应用
- [ ] 每个任务一个原子提交

## 执行标准
- 每个任务必须：先写失败测试 → 实现 → 验证 → 提交
- 两阶段审查：Spec 合规性 + 代码质量
- 偏差规则：Rule 1-3 自动处理，Rule 4 必须询问
- 检查点处必须暂停等待用户输入

## 输出标准
- SUMMARY.md 必须包含 frontmatter
- SUMMARY.md 必须列出所有完成任务
- SUMMARY.md 必须记录所有偏差处理
- SESSION.md 必须更新会话日志

## 退出条件
- SUMMARY.md 已写入
- STATE.md 已更新
- 所有任务已提交
```

### 4. 验证阶段门控

```
# 验证阶段门控

## 进入条件
- .planning/phases/XX/SUMMARY.md 存在
- 用户输入包含"验证"、"verify"、"完成没有"等关键词

## 必须执行的检查清单
- [ ] 目标回溯验证：从 ROADMAP.md 目标反推
- [ ] PLAN.md 验证标准逐项检查
- [ ] 所有测试运行
- [ ] 构建/编译检查
- [ ] Lint/TypeScript 检查
- [ ] 端到端功能验证

## 验证标准
- 所有 must_haves 必须满足
- 所有验证命令必须通过
- 没有阻断性问题
- 文档已更新

## 退出条件
- 验证报告已生成
- 用户确认验证结果
```

### 5. 调试阶段门控

```
# 调试阶段门控

## 进入条件
- 用户输入包含"调试"、"debug"、"bug"、"报错"、"错误"、"异常"等关键词
- 或执行/验证阶段发现问题

## 必须执行的检查清单
- [ ] 问题复现
- [ ] 常见问题排查（依赖、配置、环境）
- [ ] 根因追踪
- [ ] 修复方案评估
- [ ] 回归测试

## 调试标准
- 先复现问题再修复
- 使用深度防御方法
- 记录调查过程
- 修复后添加回归测试

## 退出条件
- 问题已修复
- 验证通过
- 调试记录已保存
```

---

## 条件触发规则

### 自动触发研究

```
# 自动触发研究的条件

WHEN:
  - 用户要求规划，且 .planning/research/ 目录不存在
  - 用户要求规划，且研究文件不完整
  - 任务涉及新技术选型（L2/L3 级别）

THEN:
  - 停止规划流程
  - 提示用户："⚠️ 规划需要先了解技术需求"
  - 询问是否进行技术研究
  - 研究完成后再继续规划
```

### 自动触发验证

```
# 自动触发验证的条件

WHEN:
  - 执行阶段完成，SUMMARY.md 已生成
  - 用户说"继续"但下一阶段需要验证前置

THEN:
  - 自动提示："执行已完成，是否进行验证？"
  - 提供选项："验证" / "跳过验证" / "暂停"
```

### 自动触发调试

```
# 自动触发调试的条件

WHEN:
  - 执行任务时遇到错误
  - 验证阶段发现问题
  - 用户明确报告问题

THEN:
  - 自动切换到调试模式
  - 开始问题调查流程
```

---

## 状态检查标准

### .planning/ 目录结构检查

```
# .planning/ 目录完整性检查

## 必须存在的文件
- [ ] .planning/STATE.md
- [ ] .planning/ROADMAP.md
- [ ] .planning/config.json

## 根据阶段可能存在的文件
- [ ] .planning/research/ 目录（研究阶段后）
- [ ] .planning/phases/XX/PLAN.md（规划阶段后）
- [ ] .planning/phases/XX/SUMMARY.md（执行阶段后）
- [ ] .planning/designs/ 目录（规划阶段后）
- [ ] .planning/SESSION.md（可选）
```

### 阶段状态检查

```
# 阶段状态判断标准

## 判断当前处于哪个阶段
- 无 .planning/ → 初始化阶段
- 有 .planning/ 无 ROADMAP.md → 研究阶段
- 有 ROADMAP.md 无 PLAN.md → 规划阶段
- 有 PLAN.md 无 SUMMARY.md → 执行阶段
- 有 SUMMARY.md → 验证/完成阶段

## 判断阶段是否完成
- 研究完成：.planning/research/ 有 STACK.md、ARCHITECTURE.md、FEATURES.md
- 规划完成：.planning/phases/XX/PLAN.md 存在且已审查
- 执行完成：.planning/phases/XX/SUMMARY.md 存在
- 验证完成：验证报告存在且用户确认
```

---

## 执行标准清单

### Planner 执行标准

```
# Planner 必须满足的标准

## 任务分解标准
- [ ] 每个任务 2-5 分钟可完成
- [ ] 每个任务只做一件事
- [ ] 每个任务有明确的 Files 列表
- [ ] 每个任务有可执行的 Steps
- [ ] 每个任务有验证命令

## 文件结构标准
- [ ] File Structure 表格包含 File、Responsibility、Public API
- [ ] 每个文件职责单一
- [ ] 文件边界清晰

## 验证标准
- [ ] 目标回溯验证完成
- [ ] 每个任务有验证标准
- [ ] 验证命令可自动化执行
```

### Executor 执行标准

```
# Executor 必须满足的标准

## 提交标准
- [ ] 每个任务一个原子提交
- [ ] 提交信息格式："[Phase N] Task M: 描述"
- [ ] 提交前运行 lint/type-check

## 审查标准
- [ ] 每个任务后两阶段审查
- [ ] Stage 1: Spec 合规性检查
- [ ] Stage 2: 代码质量检查
- [ ] 问题修复后重新审查（最多 5 次）

## 偏差处理标准
- [ ] Rule 1-3 自动修复，无需询问
- [ ] Rule 4 必须使用 AskUserQuestion 确认
- [ ] 所有偏差记录到 SUMMARY.md
```

### Verifier 执行标准

```
# Verifier 必须满足的标准

## 验证标准
- [ ] 从 ROADMAP.md 目标反推验证
- [ ] 逐项检查 PLAN.md 的验证标准
- [ ] 运行所有相关测试
- [ ] 检查构建/编译
- [ ] 端到端功能验证

## 输出标准
- [ ] 生成验证报告
- [ ] 明确标记通过/失败项
- [ ] 提供修复建议（如需要）
```

---

## 使用方法

### 快速配置

将以下内容复制到 `.trae/rules/project_rules.md`：

```
# GSD 工作流门控规则

## 门控原则
- Always check .planning/ directory structure before proceeding.
- Do NOT start planning without research files in .planning/research/.
- Do NOT start execution without a valid PLAN.md.
- Each phase must complete its checklist before moving to next.

## 条件触发
- When user asks to plan and research is missing: stop and ask to do research first.
- When execution completes: automatically suggest verification.
- When errors occur during execution: switch to debugging mode.

## 标准化执行
- Follow the checklists for each phase strictly.
- Each task must have verification criteria.
- Two-stage review is mandatory after each task.
- Document all deviations in SUMMARY.md.
```

### 完整配置

如需完整门控规则，复制本文件内容到 `.trae/rules/project_rules.md` 并根据项目调整。

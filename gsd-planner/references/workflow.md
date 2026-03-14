# GSD Planner 工作流参考

## 规划流程

### 1. 输入解析
从 CONTEXT.md 获取用户锁定的决策，作为规划依据。

### 2. 目标回溯验证
从阶段目标出发，反推必须完成的工作：
```
目标 → 必须存在什么？ → 需要什么任务？
```

### 3. 任务分解规则
- 每阶段 2-3 个任务
- 每个任务可独立完成和验证
- 任务间依赖关系明确

### 4. 依赖图构建
```
任务 A (无依赖) ──→ 任务 B (依赖 A)
     │
     └──────────→ 任务 C (依赖 A)
```

### 5. 波次分配
- Wave 1: 无依赖的任务（可并行）
- Wave 2: 依赖 Wave 1 的任务
- Wave 3: 依赖 Wave 2 的任务

## PLAN.md 结构要求

```yaml
---
phase: XX-name
plan: NN
type: execute
wave: N
depends_on: []
files_modified: []
autonomous: true
requirements: []
must_haves:
  truths: []
  artifacts: []
  key_links: []
---

&lt;objective&gt;
描述计划目标
&lt;/objective&gt;

&lt;context&gt;
引用相关文件
&lt;/context&gt;

&lt;tasks&gt;
&lt;task type="auto"&gt;
  &lt;name&gt;任务名称&lt;/name&gt;
  &lt;files&gt;涉及的文件&lt;/files&gt;
  &lt;action&gt;具体操作&lt;/action&gt;
  &lt;verify&gt;验证方法&lt;/verify&gt;
  &lt;done&gt;完成标准&lt;/done&gt;
&lt;/task&gt;
&lt;/tasks&gt;

&lt;verification&gt;
- [ ] 验证项 1
- [ ] 验证项 2
&lt;/verification&gt;

&lt;success_criteria&gt;
成功标准
&lt;/success_criteria&gt;
```

## 修订模式

当计划需要修订时：
1. 读取当前 PLAN.md 和 SUMMARY.md
2. 分析偏差原因
3. 更新任务列表
4. 重新计算波次

## Gap 闭包模式

当验证发现问题时：
1. 从 VERIFICATION.md 读取 gaps
2. 为每个 gap 创建修复任务
3. 生成修复计划
4. 执行后重新验证

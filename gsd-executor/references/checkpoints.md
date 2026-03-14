# GSD Executor 检查点协议

## 检查点类型

### checkpoint:human-verify
需要人工验证已构建的内容。

```xml
&lt;task type="checkpoint:human-verify" gate="blocking"&gt;
  &lt;what-built&gt;[构建的内容] - 服务运行于 [URL]&lt;/what-built&gt;
  &lt;how-to-verify&gt;访问 [URL] 并验证: [仅视觉检查，无需 CLI 命令]&lt;/how-to-verify&gt;
  &lt;resume-signal&gt;输入 "approved" 或描述问题&lt;/resume-signal&gt;
&lt;/task&gt;
```

### checkpoint:decision
需要用户做出实现决策。

```xml
&lt;task type="checkpoint:decision" gate="blocking"&gt;
  &lt;decision&gt;[需要决策的内容]&lt;/decision&gt;
  &lt;context&gt;[为什么这个决策重要]&lt;/context&gt;
  &lt;options&gt;
    &lt;option id="option-a"&gt;
      &lt;name&gt;[选项名称]&lt;/name&gt;
      &lt;pros&gt;[优点]&lt;/pros&gt;
      &lt;cons&gt;[缺点]&lt;/cons&gt;
    &lt;/option&gt;
    &lt;option id="option-b"&gt;
      &lt;name&gt;[选项名称]&lt;/name&gt;
      &lt;pros&gt;[优点]&lt;/pros&gt;
      &lt;cons&gt;[缺点]&lt;/cons&gt;
    &lt;/option&gt;
  &lt;/options&gt;
  &lt;resume-signal&gt;选择: option-a 或 option-b&lt;/resume-signal&gt;
&lt;/task&gt;
```

### checkpoint:human-action
需要用户执行无法自动化的操作。

```xml
&lt;task type="checkpoint:human-action" gate="blocking"&gt;
  &lt;action-needed&gt;
    1. [第一步]
    2. [第二步]
    3. [第三步]
  &lt;/action-needed&gt;
  &lt;why-required&gt;[为什么需要人工操作]&lt;/why-required&gt;
  &lt;resume-signal&gt;完成上述步骤后输入 "done"&lt;/resume-signal&gt;
&lt;/task&gt;
```

## 检查点处理流程

1. **遇到检查点时**:
   - 暂停当前任务执行
   - 输出检查点信息给用户
   - 等待用户响应

2. **用户响应后**:
   - 解析用户输入
   - 继续执行后续任务
   - 记录决策到 CONTEXT.md

3. **检查点恢复**:
   - 从检查点任务后的第一个任务继续
   - 保持之前任务的状态

## 检查点输出格式

```
╔══════════════════════════════════════════════════════════════╗
║ CHECKPOINT: [类型]
╠══════════════════════════════════════════════════════════════╣
║ [检查点内容]
╠══════════════════════════════════════════════════════════════╣
║ 恢复信号: [用户应输入的内容]
╚══════════════════════════════════════════════════════════════╝
```

## Gate 类型

| Gate | 行为 |
|------|------|
| `blocking` | 必须等待用户响应才能继续 |
| `optional` | 用户可以选择跳过 |

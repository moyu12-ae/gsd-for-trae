# STATE.md 模板

---
project: "{项目名称}"
current_phase: P{n}
current_status: planning | executing | verifying | debugging | complete
last_updated: "{日期}"
---

# Project State

## Current Phase
- **Phase:** P{n}
- **Status:** {status}
- **Started:** {开始日期}
- **Plan File:** PLAN.md (如果存在)

## Completed Phases
- P1: ✓ VERIFIED - {阶段名称}
- P2: ✓ VERIFIED - {阶段名称}

## Active Tasks
| Task | Status | Started | Notes |
|------|--------|---------|-------|
| T1 | in_progress | {日期} | {备注} |
| T2 | pending | - | - |

## Blockers
&lt;!-- 如果有阻塞项列出，否则留空 --&gt;
- {阻塞项描述} (since {日期})

## Recent Actions
1. [{日期}] {动作描述}
2. [{日期}] {动作描述}
3. [{日期}] {动作描述}

## Statistics
- **Total Phases:** {n}
- **Completed Phases:** {n}
- **Total Tasks:** {n}
- **Completed Tasks:** {n}
- **Time Spent:** {时间}

## Next Steps
1. {下一步 1}
2. {下一步 2}

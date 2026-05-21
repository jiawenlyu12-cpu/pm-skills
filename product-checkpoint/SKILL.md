---
name: product-checkpoint
description: "每个产品的 7-phase 流程检查清单。维护产品库（一产品一份 JSON 状态），主动调用做 phase self-check / 推进 / 新建 / 切换 active；被动在产品话题中 surface 当前 phase 风险。触发词：产品检查 / phase check / 产品自检 / 新产品立项 / 切到产品 X / 产品库 / <name> 进度。"
---

# Product Checkpoint · 多产品 7-Phase 跟踪

> 用户自己定的产品流程方法论。每个产品必须经过 7 个 phase。这个 skill 维护多产品状态、问对的检查问题、提醒漏掉的关键产出物。

## 何时激活

**主动触发词**（用户输入即调用）：
- `产品检查` / `产品自检` / `phase check`
- `<product-name> 进度`
- `新产品立项 <name>` / `新建产品 <name>`
- `切到 <name>` / `切换产品 <name>` / `active product`
- `产品库` / `所有产品`
- `推进到下一阶段` / `phase up`

**被动 surface**（不要在每次对话都触发，仅在以下情况）：
- 用户在讨论某个**已在产品库里的产品**的具体决策（feature 设计、prompt、定价、用户调研结果……）
- 用户描述"做一个新产品 / 想做一个 X"——主动问"要不要走一遍 P0 验证？"
- 用户描述某个 fail case / 卡点——主动定位到对应 phase 的 gotcha

surface 方式：**1 句话** + 给出主动触发词。不要把整个清单塞过去。

例：`你刚提到这个产品还没跑 eval——这是 P3 的核心产出物，没基线后续调优都是体感。打 "<name> 进度" 走一遍 P3 self-check。`

## 状态目录

```
~/.claude/product-checkpoint/
├── _active.json          # 当前 active 产品 slug + 全局元信息
├── <slug-1>.json         # 一产品一份
├── <slug-2>.json
└── ...
```

`_active.json`（初始为空，第一次用 `新产品立项 <name>` 后填充）：
```json
{ "active_slug": "myapp", "all_products": ["myapp"], "last_updated": "YYYY-MM-DD" }
```

## 产品状态文件 schema（`<slug>.json`）

```json
{
  "name": "MyApp",
  "slug": "myapp",
  "domain": "myapp.example.com",
  "kind": "ai-product",
  "one_liner": "一句话描述",
  "created_at": "YYYY-MM-DD",
  "last_reviewed": "YYYY-MM-DD",
  "current_phase": 4,
  "phases": {
    "p0_problem_validation": { "status": "done|in_progress|pending|skipped", "notes": "...", "evidence": ["..."] },
    "p1_one_liner_brd": { ... },
    "p2_prototype": { ... },
    "p3_evals": { ... },
    "p4_mvp_engineering": { ... },
    "p5_first_users": { ... },
    "p6_iteration_loop": { ... },
    "p7_unit_economics": { ... }
  },
  "fail_cases": [
    { "date": "YYYY-MM-DD", "case": "...", "phase": "p3", "fix": "..." }
  ],
  "evals": { "set_size": 0, "baseline_score": null, "current_score": null, "rubric": null },
  "metrics": {
    "users": null,
    "cost_per_inference": null,
    "p95_latency_ms": null,
    "prompt_cache_enabled": null
  },
  "open_questions": ["..."]
}
```

`status` 取值：`done` / `in_progress` / `pending` / `skipped`（用户明确跳过，需说明 reason）

## 7 个 Phase（用户的产品方法论）

> 详细 self-check 问题在 `references/phases.md`。Skill 启动时按需 read 那个文件。

| # | Phase | 一句话 | 核心产出物 |
|---|---|---|---|
| 0 | 立项判断 | Mom Test 风格访谈 5-10 个真实用户，挖最近一次他们怎么解决这个问题 | 问题 brief 1-2 页 + AI fit 自检 |
| 1 | 一句话立目标 | PR/FAQ 倒推 + 决定 "输出层 vs 行动层" | 1 段话 BRD |
| 2 | 原型（不写产品代码）| Playground 拼 prompt 跑 20 个真实样例 | prompt 雏形 + 样例对照表 + 成本估算 |
| 3 | 评测体系 | 50+ golden test set + 评测脚本 + baseline | `evals/` 目录 + 基线分数 |
| 4 | MVP 工程 | 模型选型 + prompt cache + telemetry day 0 | 能跑的 MVP + log |
| 5 | 第一波真实用户 | 坐在 5-10 个用户旁边看 30 分钟 | 录像 + 观察笔记 + fail cases |
| 6 | 调优循环 | 每周 fail → eval → prompt → 上线 A/B | 质量分 / 单位成本 / P95 三角持续跟踪 |
| 7 | 商业化 / Unit econ | 按 cohort 算 LTV / CAC / 单次 inference 成本 | 定价模型 + 回本点 |

**AI 产品独有差异**（非 AI 产品可忽略——只在 `kind=ai-product` 时强制检查）：
- P3 必须有 eval set，否则全部产品决策都是体感
- 边际成本 ≠ 0：API 费每用户每次都付
- 模型本身不是壁垒；eval + 工程 + 场景 know-how 才是
- prompt 改一行就能上线，但回归风险高 → 必须有 eval gate

## 主动触发流程

### 1. `产品检查` / `产品自检` (无产品名)
- Read `_active.json` → 拿到 active_slug
- Read `<slug>.json`
- 输出当前 phase 状态卡片：
  - 头部：name / slug / current_phase / last_reviewed
  - 7 phase 进度条（✓ done / 🔄 in_progress / ⬜ pending / ⏭️ skipped）
  - 当前 phase 的 self-check 问题（从 `references/phases.md` 取 3-5 个）
  - fail_cases 数 + open_questions 数
- 等用户回答 self-check 问题，回答完更新 JSON 并写 `last_reviewed`

### 2. `<name> 进度`
- 同上，但 active_slug 强制设为该 name 对应的 slug
- 如果 `<name>.json` 不存在 → 提示用户用 `新产品立项 <name>` 先初始化

### 3. `新产品立项 <name>` / `新建产品 <name>`
- 创建 `<slug>.json` 用默认模板
- 询问 4 问初始化：
  1. 一句话定位（who + what + AI 在哪一层）
  2. 是不是 AI 产品（`kind`）
  3. 现在自评在哪个 phase
  4. 第一个最大未解的问题
- 立即跑 P0 self-check
- 更新 `_active.json.all_products` 和 `active_slug`

### 4. `切到 <name>` / `切换产品 <name>`
- 更新 `_active.json.active_slug`
- 显示该产品当前状态摘要

### 5. `产品库` / `所有产品`
- 列出 `_active.json.all_products`，每个一行：name / current_phase / last_reviewed / blocker

### 6. `推进到下一阶段` / `phase up`
- 检查当前 phase 的产出物 checklist 是否全 `done`
- 不全 → 拒绝推进，列缺失项
- 全 done → current_phase += 1，跑下一 phase 的 self-check

## 被动 surface 规则

**只在以下场景主动出**（其他时候保持沉默）：
1. 用户在讨论已存在的产品的具体决策 → 用 1 句话点出该决策对应的 phase + 风险，并给出主动触发词
2. 用户说"想做一个 X" / "新点子" → 1 句话："要走一遍 P0 验证吗？打 `新产品立项 X`"
3. 用户描述卡点 / fail case → 定位到 phase 的 gotcha，1 句话提示

**禁止**：在跟产品无关的对话（pitchkit 渲染、写代码、查 PM 新闻等）做 surface。

## 失败兜底

- `_active.json` 不存在 → 初始化空 active，提示用户用 `新产品立项 <name>`
- `<slug>.json` 损坏 → 备份成 `<slug>.json.broken-<timestamp>`，提示用户
- 用户问"为什么我这个 phase 失败" → 读 fail_cases，跨 phase 模式分析

## 输出风格

简洁、可读、信息密度高。**不要**输出完整 7 phase 文档——只输出当前 phase + 必要上下文。
卡片化文本即可，**不需要**生成 HTML（用户的产品库不是日报，不需要可视化页面）。

## 跟其他 skill 的边界

- `daily-pm-study`：那是每日学习闭环；这个 skill 是产品执行跟踪。不要混。
- `prd`：要写完整 PRD 时用 prd skill；这里只做 phase 跟踪。
- `pitchkit`：做宣传物料；不影响 phase 状态。

---

## 维护说明

这套流程的来源 / 详细论述见 `references/phases.md`。如果用户调整了流程（增加 phase、改顺序），同步更新那个文件 + 本文件 phase 表。

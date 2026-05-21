# 7-Phase 详细定义 · Self-Check 问题库

> 每个 phase 列：goal / outputs / self_check 问题 / 常见 gotcha / AI 产品额外项。
> Skill 主流程根据 current_phase 取对应 self-check 问题（默认抽 3-5 个）。

---

## P0 · 立项判断

**Goal**：在写第一行代码之前，确认这事 AI 真的比现在的方案省钱/省时/更准。

**Outputs**（产出物 checklist）：
- [ ] 1-2 页问题 brief
- [ ] 5-10 个真实用户访谈记录（Mom Test 风格）
- [ ] AI fit 自检结论（明确为什么不是 if/else / 搜索 / 规则）

**Self-check 问题**：
1. 你跟几个**真实**潜在用户聊过？最近一次聊是什么时候？
2. 他们最近一次遇到这个问题时是怎么解决的？花了多久、付了多少钱？
3. 这事用 LLM 比 if/else / 规则 / 搜索 真的更省/更准吗？说出具体维度。
4. 有没有用户在你描述 idea 时直接夸你"好棒"——如果有，那一个数据点要扔掉。
5. 你能用一句话说出"为什么是现在做" why now ？

**Gotchas**：
- 用 confirmation-bias 的问法（"你觉得这个 idea 怎么样"）
- 把"我自己也有这个问题"当成市场验证
- 强行用 AI 包装一个本该用规则解决的问题

**AI 产品额外**：跑一次"AI fit 4 问"——主观判断 / 自然语言 / 模糊到精确 / 跨格式翻译，至少命中一个才适合用 LLM。

---

## P1 · 一句话立目标

**Goal**：写出 PR/FAQ 或 1 页 BRD，确定输出层 vs 行动层定位。

**Outputs**：
- [ ] 1 段话回答 who / what / AI 做什么 / 比现状 10× 在哪
- [ ] 明确"输出层"（推荐/写作）or "行动层"（操作 / 替代人）
- [ ] 一句话 elevator pitch（< 20 字）

**Self-check**：
1. 不看 BRD，5 秒说出你的产品定位。说出来卡了吗？
2. AI 在你产品里做的是 "给建议" 还是 "动手做"？哪一档更贵 / 更慢 / 用户更省心？
3. 跟现有方案比，你这件事在哪个维度 10×？（不是 1.5×）
4. PR/FAQ 写了吗？如果发新闻稿，标题是什么？
5. 你的"输出层 vs 行动层"定位会不会未来漂移？如果会，先定个上限。

**Gotchas**：
- 贪心定位（"我们是工具 + 平台 + agent + 社区"）—— 必须砍到一个
- 没决定输出层 vs 行动层 —— 后面所有功能都会迷茫
- 跟现状比只是 1.5×（这种产品很难活）

---

## P2 · 原型（不写产品代码）

**Goal**：用 Playground 拼 prompt + 几行胶水验证核心能力。

**Outputs**：
- [ ] Prompt 雏形（200-1000 字）
- [ ] 20+ 真实样例的 "输入 → 期待 → 实际" 对照表
- [ ] 每次 inference 的 token / 成本估算

**Self-check**：
1. 你的 prompt 跑过多少个真实样例？少于 20 个不算。
2. 这 20 个里有多少是"好"的？标准是什么？（不是你自己看着顺眼）
3. 单次 inference 的 token 数你算过吗？换算成钱是多少？
4. 在 Claude vs GPT vs 国内模型上跑过同一份 prompt 对比吗？
5. 这一阶段成本超过 ¥100 吗？（超过说明在过度工程，回到 prompt）

**Gotchas**：
- 跳过这步直接做 MVP —— 90% AI 创业死在这
- 在 prompt 还出 70 分时就开始写产品代码
- 只用 hand-picked good cases 自我欺骗

---

## P3 · 评测体系（写代码前必须）

**Goal**：在写第一行产品代码前定好"什么叫输出对"。

**Outputs**：
- [ ] `evals/` 目录 + 50+ test cases
- [ ] 评测脚本（auto judge or rubric or LLM-as-judge）
- [ ] 基线分数 baseline_score
- [ ] 一份 rubric（评分标准明文）

**Self-check**：
1. 你的 eval set 有多少 case？50 够，10 不够。
2. "好" 的标准你能用 rubric 写下来吗？还是凭感觉？
3. eval 跑一次几分钟？多久会成为你的瓶颈？
4. 用户每提一个 fail case，你会立刻加进 eval set 吗？
5. 改 prompt 一个字后，eval 分数是涨是跌——你能知道吗？

**Gotchas**：
- 觉得 eval 是"工程税"跳过 —— 没 eval 就没有产品
- eval set 长期不增长（3 个月后还是初始 50 个）
- 只看平均分不看 worst case 5%

**AI 产品强制**：这一 phase 跳过 = `kind=ai-product` 的产品不允许声明"完工"，因为没法量化质量。

---

## P4 · MVP 工程

**Goal**：把 prompt 包成能用的东西，**day 0 必须有 telemetry**。

> 埋点设计原则、客户端 vs 服务端、event sourcing 等核心概念见 [`telemetry.md`](./telemetry.md)。

**Outputs**：
- [ ] 能跑的 MVP（极简前端 OK）
- [ ] Telemetry：每次输入/输出/token/延迟/feedback log
- [ ] AI 产品 generation 事件必带 `prompt_version` + `model_version`（详见 telemetry.md）
- [ ] 关键业务事件埋在服务端（不是客户端，详见 telemetry.md "可信度差异"）
- [ ] events 表 append-only，状态字段从事件序列推导（详见 telemetry.md "event sourcing"）
- [ ] Prompt cache 启用（中文用户特别重要，省 80% 系统 prompt 钱）
- [ ] 模型选型决策记录（why this model）

**Self-check**：
1. 你 log 了用户的输入和输出吗？没有的话就是飞行没仪表盘。
2. Prompt cache 开了吗？（中文产品不开亏 50%+ token 钱）
3. 你的 P95 延迟知道吗？用户能接受吗？
4. MVP 复杂度是不是传统 SaaS 的 1/3？超过了说明在过度工程。
5. 出错时你能快速降级到便宜模型 / 规则兜底吗？

**Gotchas**：
- 上线了没 telemetry —— 等于飞行没仪表
- 选了最贵的模型但没跑过便宜模型的 eval 对比
- 没开 prompt cache

---

## P5 · 第一波真实用户

**Goal**：5-10 人，**坐在旁边看 30 分钟**，不是问卷。

**Outputs**：
- [ ] 5+ 段录屏 / 屏录
- [ ] 每段 3 个观察笔记：卡哪 / 眼睛亮时 / 退出前最后做什么
- [ ] fail cases list（这一批立刻进 eval set）

**Self-check**：
1. 你看着用户用了多少分钟？低于 30 分钟不算"看"。
2. 用户说的"喜欢"和用户的实际行为一致吗？不一致时你信哪个？
3. 用户卡在哪？是 prompt 不准还是 UX 不直觉？
4. 哪个时刻用户"眼睛亮了"——把那个瞬间做大。
5. 退出前用户最后做了什么？那个是你产品的"出口"。

**Gotchas**：
- 只发问卷不看人 —— 用户说的 ≠ 用户做的
- 找朋友测 —— 客观度归零
- 只看"喜欢"用户，不看放弃用户

---

## P6 · 调优循环

**Goal**：常态化每周 cycle，质量分 / 单位成本 / P95 三角持续跟踪。

**Outputs**（持续）：
- [ ] 每周 fail case → eval set 增长
- [ ] 每周 prompt / 模型 / tool 改进 1-2 项
- [ ] 每周 A/B 上线 + 用户行为对比
- [ ] 质量分 / 单位成本 / P95 趋势图（哪怕只是 spreadsheet）

**Self-check**：
1. 上周加了多少 case 到 eval set？少于 5 个 = eval 在腐烂。
2. 改一个 prompt 后你立刻跑 eval 看回归吗？还是上线了再说？
3. 单位成本这个月是涨是跌？为什么？
4. P95 延迟是涨是跌？为什么？
5. 你这周修的是真用户 fail，还是你自己觉得"该优化"的？

**Gotchas**：
- 只调 prompt 不更新 eval set
- 优化"看起来该优化"的，不是用户真卡的
- 三角失衡：只看质量分不看成本/延迟，结果是好但赔本

---

## P7 · 商业化 / Unit Economics

**Goal**：清楚每个用户给你赚多少、花你多少。

**Outputs**：
- [ ] 按 cohort 的 LTV / CAC
- [ ] 单次 inference 成本表（按 feature 分）
- [ ] 定价模型：seat / usage / outcome 选哪个
- [ ] 重度用户 limit / 阶梯定价 / 模型降级策略

**Self-check**：
1. 你的最重度 5% 用户在赚你钱还是赔你钱？
2. 抄 SaaS 定价（$X/月不限量）吗？AI 产品这么定价大概率被打爆。
3. 重度用户用爆时你有降级 / 限流策略吗？
4. CAC 回本周期多长？API 边际成本占多少？
5. 有没有可能用 outcome-based 定价（按结果收费）？

**Gotchas**：
- 抄 SaaS 套餐（$10/月不限量）→ 被 AI 重度用户用爆
- 不区分 cohort 看 LTV
- 推理成本占毛利 30%+ 还在烧增长

**AI 产品独有的 4 个差异**：
1. **质量验证** 是统计分布不是二元
2. **边际成本** 不为 0（API 费）
3. **核心壁垒** = eval + 工程 + 场景 know-how，不是模型
4. **迭代节奏** 改一行就能上线，但回归风险高 → 必须 eval gate

---

## 跨 Phase 通用 self-check

如果用户问 "总体看我这产品怎么样"：
1. 7 phase 里跳过最早是哪个？回去补。
2. 你最近 4 周时间花在哪个 phase？跟 current_phase 一致吗？
3. fail_cases 有多少进了 eval set 不是只挂在 list 上？
4. open_questions 里有几个已经超过 4 周没动？那些是你的真盲点。

---

## 反模式（红旗）

- current_phase 卡 4 周以上没动 → 真问题没被识别
- skipped 多于 done → 在自我欺骗
- 没 fail_cases → 没在跟真实用户接触
- 所有 phase 同时 in_progress → 没在 sequential 推进

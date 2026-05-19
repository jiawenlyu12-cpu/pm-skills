# Daily PM Study · 每日产品经理学习

> 一份 35–55 分钟可完成的每日产品经理输入闭环：读书 1 章 + 资讯 + 新品 + 投资动向。
> 输出 HTML 日报到 `~/ai-news/pm-study/<date>.html`。

## 何时激活这个 Skill

当用户说以下任一触发词，立刻执行这个 skill 的完整 4 步流程：

- "今日学习"、"今日 PM 学习"、"PM 学习"、"开始今日学习"
- "daily pm study"、"pm daily"
- "今日产品功课"、"今天学习日报"

**不要**在用户只说"AI 资讯"或"今日新闻"时激活——那两个对应 `daily-ai-news` skill。这个 skill 专门是 PM 的多板块学习日报。

## 核心约束（不可妥协）

1. **总时长硬上限 60 分钟**——每个板块顶部必须显示预估时长，超过这个预算的内容必须砍掉
2. **一次只攻 1 本书**——书是串行不是并发。AI 不替用户读全文，输出"导读 + 反思框架"
3. **状态可持续**——读 `~/ai-news/pm-study/progress.json` 决定今天推第几章、推完后写回
4. **资讯不重复**——已经在 progress.json `seen_news` 里出现过的新闻不再推荐
5. **HTML 输出风格**——延续用户已有日报的样式（rose 色系 + midnight 深背景 + 卡片化）

## 4 步工作流

### Step 0：前置读取

```bash
# 读今天的日期和 ISO week（决定周一加 bonus 内容）
date '+%Y-%m-%d %u %V'  # → 2026-05-19 2 20  (周二，第 20 周)

# 读学习进度
cat ~/ai-news/pm-study/progress.json
```

`progress.json` 不存在 → 用 `references/books-curriculum.md` 推荐的起始书目（《俞军产品方法论》）初始化它。

### Step 1：📖 今日读章（15–20 分钟预算）

输入：`progress.json` 里的 `current_book` 和 `current_chapter`。
查 `references/books-curriculum.md` 拿到这本书的章节主题列表。

输出一段卡片，包含：
- **本章标题 + 主题一句话**（来自 curriculum）
- **3 个观察点**：在读这章时着重思考的角度（AI 自己生成，基于章节主题）
- **1 个本日 takeaway 反思题**：读完这章问自己的一个具体问题
- **当前进度条**：第 X 章 / 共 Y 章 = Z%

> **个性化**：如果 `progress.json.user_preferences.focus_areas` 非空，观察点和反思题可以适度对照这些领域举例（如 focus_areas 包含 "B2B SaaS" 则给 B2B 例子）。focus_areas 为空时使用**中立通用**的例子，**不要替用户编造场景或项目名**。

完成后**自动更新** `progress.json`：
- 若用户在 yesterday 没看 → 不推进章节，保持 current_chapter，但在卡片加一行"昨日未读 → 今日补"
- 若用户上次完成 → current_chapter +1
- 若 current_chapter > 本书总章数 → 自动切到 `next_books` 第一本，把刚读完的写进 `completed_books`

判定"用户是否完成"的简化规则：**每次触发 skill 默认视为前一天读完了**（用户自己负责诚实）。卡片底部留一行可勾选的"☑ 我今天没读，明天给我同一章"——这只是提示语，不需要真实交互。

### Step 2：📰 产品 + AI 资讯（10–15 分钟预算）

数据源（按优先级）：
1. WebSearch `latest AI product manager news <today>`（限 24h 内）
2. 直接 WebFetch：
   - `https://lennysnewsletter.com`（PM 第一刊）
   - `https://stratechery.com`（战略分析）
   - `https://36kr.com`（中文综合）
3. 复用 `daily-ai-news` skill 的 AI 资讯抓取逻辑

输出 5–8 条新闻，每条：
- 标题 + 链接 + 来源
- **PM 视角解读**：1 句话回答"这条对 PM 工作有什么启示"——不是新闻摘要，是判断
- 去重：跳过 `progress.json.seen_news` 里出现过的 URL；推完后把新 URL 写回

### Step 3：🚀 新品追踪（5–8 分钟预算）

**每天都做**：
- WebFetch `https://www.producthunt.com/` → 抓今日 Top 3 AI 类产品
- 每个：名字 / tagline / upvotes / URL / 1 句解读

**仅周一额外做**：
- WebFetch `https://www.producthunt.com/leaderboard/weekly/<year>/<week-1>` → 上周 Top 5 AI 产品
- 加一段"跨产品模式"：本周有什么新趋势/类目集中？

### Step 4：📊 财报 / 投资动向（5–10 分钟预算）

**每天都做**：
- WebSearch `AI startup funding deal <today>` + `VC investment AI <today>`
- 输出 1–2 条最大融资 / 并购 / IPO 进展

**仅周一额外做**：
- 列出本周公开的财报日历（哪天哪家发，参考 finance.yahoo.com 的 earnings calendar）
- 上周已发布的关键财报中，挑 1 家做 1 段"PM 视角拆解"（若 `user_preferences.focus_areas` 非空则优先匹配赛道，否则挑当周最有信号的一家）

### Step 5：写 HTML + 更新状态

#### 5.1 写日报到 `~/ai-news/pm-study/<YYYY-MM-DD>.html`

HTML 结构必须包含：
- 顶部 header：日期 / "Day N" / 本周第几天 / 当前书目和进度
- 4 个 section，每个顶部用 `<span class="time-badge">⏱️ 15–20 分钟</span>` 标时间预算
- 底部"今日反思"区：留一段空白引导用户截图后手写笔记
- 底部 footer：本日 4 板块总时长预算

#### 5.2 更新 `progress.json`

```json
{
  "current_book": "俞军产品方法论",
  "current_book_total_chapters": 14,
  "current_chapter": 3,        // 推完今天的章后 +1
  "started_at": "2026-05-19",
  "last_run_date": "2026-05-19",
  "completed_books": [],
  "next_books": ["The Mom Test", "Inspired", "Continuous Discovery Habits", "Hooked", "Working Backwards", "Empowered", "The Hard Thing About Hard Things"],
  "seen_news": ["url1", "url2"],   // 累计去重池
  "day_count": 1,
  "user_preferences": {
    "focus_areas": []          // 可选：用户关心的赛道（如 ["AI agent", "B2B SaaS"]），非空则内容轻度倾斜；为空则完全中立
  }
}
```

#### 5.3 自动打开

```bash
open ~/ai-news/pm-study/<YYYY-MM-DD>.html
```

## HTML 样式约定

- 主色：`#c15f3c`（rose-accent）
- 深色模式自动支持（用 `@media (prefers-color-scheme: dark)`）
- 卡片化布局：每个新闻/产品/章节都是一个 `.story` 或 `.card`
- 时间徽章：`<span class="time-badge">⏱️ X–Y 分钟</span>`
- 中文字体：`PingFang SC` / `Source Han Serif SC`
- 4 个 section 分别用不同色边框（建议：book 紫 / news 蓝 / ph 绿 / vc 橙）

## 失败兜底

- **WebFetch 失败**：跳过该数据源，记录在日报底部"⚠️ 数据源失败"段
- **progress.json 损坏**：备份成 `progress.json.broken-<timestamp>`，新建一份默认的
- **章节超出书的总章数**：自动切换到 next_books 的第一本，写日志

## 用户自定义入口

如果用户说："换书"、"读完了这本"、"跳到 Inspired"、"给我看进度"——

- "给我看进度" → 直接 `cat ~/ai-news/pm-study/progress.json`，格式化输出
- "换书 / 跳到 X" → 修改 progress.json 的 current_book + current_chapter=1
- "读完了这本" → 把 current_book 推进 completed_books，自动切下一本

## 相关 skill

- `daily-ai-news`：本 skill 的资讯段是其精简版 + PM 视角，不要重复触发
- `market-research`：用户对某个新品 / 财报想深挖时，suggest 他用这个 skill 而不是堆进每日学习

---

## 🌐 分享须知（给打包发布的人）

这个 skill 设计为**任何 PM 都能直接用**。分享时：

**必须分享**：
- `SKILL.md`（本文件）
- `references/books-curriculum.md`
- `references/sources.md`

**不要分享**：
- `~/ai-news/pm-study/progress.json` — 这是接收者的个人学习状态，每个人独立
- `~/ai-news/pm-study/<date>.html` — 这是接收者每天的产出，跟 skill 无关

**首次使用时 skill 会做什么**：
- 如果 `~/ai-news/pm-study/progress.json` 不存在，自动创建一份，从《俞军产品方法论》第 1 章起步
- `user_preferences.focus_areas` 默认为空数组，所有人初始体验完全中立——想要个性化就自己往里加领域名

**约定**：本文件、curriculum、sources 三个文件**不允许出现任何具体公司名、个人项目名、用户身份描述**。所有个性化必须经由 progress.json.user_preferences 注入。

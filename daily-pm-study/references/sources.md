# Daily PM Study · 数据源清单

> 这份文件告诉 skill：每个板块从哪抓数据、怎么过滤、什么角度解读。

---

## 内容倾斜（可选）

Skill 在 `~/ai-news/pm-study/progress.json` 里读 `user_preferences.focus_areas` 数组。例如：

```json
"user_preferences": {
  "focus_areas": ["AI agent", "B2B SaaS", "education"]
}
```

- 数组非空：4 板块筛选时优先匹配 focus_areas 的内容；若某天 4 板块里完全没有相关信号，**至少补 1 条相关的**，避免和用户实际工作脱节
- 数组为空 / 字段不存在：**完全不倾斜**，按各板块默认规则选最重要的内容即可——这是默认状态

**重要**：不要把任何具体公司名、个人项目名、用户身份写进这份文档或 SKILL.md。所有个性化必须经由 `progress.json.user_preferences` 注入。

---

## 板块 2：📰 产品 + AI 资讯（10–15 分钟，5–8 条）

### 抓取顺序

1. **WebSearch**（首选，覆盖最广）：
   - `latest AI product launch <YYYY-MM-DD>`
   - `product manager industry news <YYYY-MM-DD>`
   - `AI agent enterprise news <YYYY-MM-DD>`

2. **WebFetch**（补深度）：
   - `https://www.lennysnewsletter.com/` — PM 第一刊
   - `https://stratechery.com/` — Ben Thompson 战略分析（免费部分）
   - `https://36kr.com/` — 中文综合
   - `https://www.latepost.com/` — 中文深度（每周更新慢，周一周二看较新）

### 过滤规则

- 只看 **最近 48 小时**（除非是大事件错过的补漏）
- 跳过 `progress.json.seen_news` 已有的 URL
- 优先选有**具体数字 / 公司名 / 时间**的，跳过纯观点文
- 中英文平衡：5–8 条里至少 2 条中文、2 条英文

### 输出格式（每条）

```
[标题] · [来源] · [发布时间]
{1 句话陈述事实}
🔎 PM 视角：{不复述新闻，而是回答"这条对 PM 工作的启示"——
            判断、可借鉴的模式、值得跟踪的指标}
```

**关键**：PM 视角必须是判断不是摘要。例如"OpenAI 推出 X 功能"——错误写法"它能做 Y"，正确写法"它把 Z 类工具的差异化压缩到了零，做这类工具的产品必须考虑加 W"。

---

## 板块 3：🚀 新品追踪（5–8 分钟）

### 每天必做

WebFetch `https://www.producthunt.com/` 抓今日 Top 10——筛出 **AI 相关 Top 3**。

每个产品输出：
- 名称 + tagline + upvotes + URL
- 1 句话："它解决了什么 + 与已有方案的差异点"
- 标签：哪个细分类目（AI agent / writer / dev tools / 消费类 / 等）

### 仅周一额外做

WebFetch `https://www.producthunt.com/leaderboard/weekly/<year>/<ISO-week-1>` 抓上周周榜，挑 AI 相关 Top 5。

加一段 **本周新品模式**：
- 类目分布（多少 agent / 多少 writer / 多少消费）
- 重复出现的关键词（"memory" / "vertical" / "local-first" 等）
- 1 个反常识发现（如"本周没有任何视频生成类上榜"或"消费类突然涨"）

### 失败兜底

如果 weekly leaderboard URL 404，fallback：
- WebSearch `Product Hunt top AI launches this week May 2026`
- 或者直接用首页 + 注明"本周榜单未拿到，下面是今日 + 昨日的整合"

---

## 板块 4：📊 财报 / 投资动向（5–10 分钟）

### 每天必做

WebSearch（任选 2 个组合）：
- `AI startup funding announcement <YYYY-MM-DD>`
- `VC investment deal AI Series <today>`
- `AI company acquisition <today>`

挑 1–2 条最有意义的（避免堆量）：
- 优先：金额 ≥ $50M 的 / 知名 VC 入局的 / 与 `user_preferences.focus_areas` 相关的
- 跳过：种子轮 < $5M 且无名 VC / 纯宣传稿性质

输出格式：
```
[公司] [轮次] [金额] · [领投方]
做什么的（1 句）
💡 信号：{这笔钱反映了什么趋势？谁现在愿意为什么买单？}
```

### 仅周一额外做

#### A. 本周财报日历

WebFetch `https://finance.yahoo.com/calendar/earnings?day=YYYY-MM-DD`（本周一）抓本周要发财报的公司清单。

筛出"PM 必关注的基础盘 + user_preferences.focus_areas 相关公司"：
- **基础盘 4 家**（每季度必看，所有 PM 通用）：Apple / Microsoft / 腾讯 / Spotify
- **AI 平台层**：Nvidia / Salesforce / ServiceNow / Databricks（已上市）
- **focus_areas 相关行业**：根据 focus_areas 内容动态挑选该赛道头部 2–3 家。例如 focus_areas=["education"] 就挑 Duolingo / Coursera / 新东方等

输出表格：
| 日期 | 公司 | 季度 | PM 视角关注点 |
|---|---|---|---|

#### B. 上周财报拆解（挑 1 家）

从上周已发布的财报里挑 1 家做 1 段"PM 视角拆解"：
- 这家本季度的关键产品决策是什么
- 哪个指标超预期 / 低于预期
- 给同类产品 PM 的启示

数据源：
- 公司 IR 页面（`investor.<company>.com` 或 `<company>.com/investors`）
- Seeking Alpha 的 Earnings Call Transcript
- The Motley Fool 的 transcript

---

## 共用规则

### 时间戳处理

- 所有"今天"用 `date +%Y-%m-%d` 拿
- 所有"本周第 X 周"用 `date +%V`（ISO 周数）
- 北京时间换算注意：早上跑 skill 时美国还是昨天，所以"昨日财报"指的是 UTC 昨日

### 引用链接

每条新闻 / 产品 / 公告都要给完整 URL，方便用户点进去深读。

### Markdown vs HTML

skill 最终输出是 HTML，但抓取阶段 AI 可以先用 Markdown 整理思路，最后再渲染成 HTML 卡片。

---

## 不要做的事

- ❌ 不要堆量（>8 条新闻、>5 个产品、>3 条投资）——超过了用户读不完
- ❌ 不要罗列摘要——每条必须有 PM 视角解读
- ❌ 不要用过去 1 周以上的"旧闻"——日报就是日报，过期了换一条
- ❌ 不要在板块 2、3、4 里重复同一条新闻（比如 PH 上的产品又在新闻里写一遍）——板块互补不重叠

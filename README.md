# PM Skills · for Claude Code / Cursor / Codex / Cline 等

一组面向**产品经理**的 agent skill 集合，让你的 AI 助手成为日常学习与思考的搭档。

---

## 当前包含的 skills

### 📚 daily-pm-study · 每日产品经理学习

一份 35–55 分钟可完成的 PM 输入闭环：**读书 1 章 + 资讯 + 新品 + 投资动向**。

- 8 本必读书目，按"一天 1 章"节奏推进，~4 个月读完
- 4 个板块每天定时定量，硬上限 60 分钟
- 资讯带 PM 视角解读（不是新闻摘要，是判断）
- 自动维护学习进度 + 累计去重池
- 输出 HTML 日报，方便浏览器查看 / 截图归档
- 个性化经由 `progress.json.user_preferences` 注入，**默认完全中立**

📂 详细文档：[`daily-pm-study/SKILL.md`](daily-pm-study/SKILL.md)

---

### 🎯 product-checkpoint · 多产品 7-Phase 流程跟踪

每个产品都按统一的 7-phase 流程推进，**不再凭感觉做产品**。

- **7 个 phase**：立项判断 → 一句话 BRD → 原型 → 评测体系 → MVP 工程 → 首批真实用户 → 调优循环 → Unit Economics
- **多产品库**：一产品一份 JSON 状态文件（phase / fail_cases / evals / metrics / open_questions）
- **每个 phase 配 self-check 问题库**（4-6 个），强制你回答完才能推进
- **AI 产品独有强制项**：P3 eval 不允许 skip，否则全部产品决策都没有基线
- **主动触发**：`产品检查` / `<name> 进度` / `新产品立项 <name>` / `产品库` / `推进到下一阶段`
- **被动 surface**：聊产品话题时 1 句话点出当前 phase 风险（不噪音）
- **状态文件位置**：`~/.claude/product-checkpoint/`（跨项目共享，不随仓库分发）

📂 详细文档：[`product-checkpoint/SKILL.md`](product-checkpoint/SKILL.md) · 7-phase 详细问题库：[`product-checkpoint/references/phases.md`](product-checkpoint/references/phases.md)

---

## 安装方式

### 方式 A · 用 skills CLI（推荐）

```bash
# 单装某一个
npx skills add jiawenlyu12-cpu/pm-skills@daily-pm-study -g -y
npx skills add jiawenlyu12-cpu/pm-skills@product-checkpoint -g -y
```

`-g` 全局安装，`-y` 跳过确认。装完后在 Claude Code 里用各自的触发词即可。

### 方式 B · 手动 clone

```bash
git clone https://github.com/jiawenlyu12-cpu/pm-skills.git
mkdir -p ~/.claude/skills
cp -R pm-skills/daily-pm-study ~/.claude/skills/
cp -R pm-skills/product-checkpoint ~/.claude/skills/
```

下次新开 Claude Code 会话即可使用。

---

## 触发词

装好之后，对 Claude Code 说以下短语即可激活对应 skill：

### daily-pm-study
- "今日 PM 学习"
- "PM 学习"
- "今日产品功课"
- "daily pm study"

### product-checkpoint
- "产品检查" / "产品自检" / "phase check"
- "新产品立项 \<name\>" — 创建新产品并跑 P0 self-check
- "\<name\> 进度" — 查看 / 推进指定产品
- "切到 \<name\>" — 切换 active 产品
- "产品库" / "所有产品" — 列总览
- "推进到下一阶段" / "phase up"

---

## 仓库结构

```
pm-skills/
├── daily-pm-study/
│   ├── SKILL.md                    # 主流程
│   └── references/
│       ├── books-curriculum.md     # 8 本书 × 127 章大纲
│       └── sources.md              # 数据源 + 过滤规则
├── product-checkpoint/
│   ├── SKILL.md                    # 7-phase 流程入口 + 触发词
│   └── references/
│       └── phases.md               # 每个 phase 的 self-check 问题库 + gotchas
├── README.md                       # 本文件
└── LICENSE                         # MIT
```

每个 skill 的工作目录（`daily-pm-study` 用 `~/ai-news/pm-study/`，`product-checkpoint` 用 `~/.claude/product-checkpoint/`）由 skill 在首次触发时自动创建，**不随仓库分发**。

---

## 想要加入或反馈

- 发现书目章节信息错误 / 建议数据源调整 → 提 Issue 或 PR
- 7-phase 流程想增减 phase / 调整 self-check 问题库 → 提 Issue 或 PR
- 想加新 skill（如 `weekly-pm-review`、`decision-log`）→ 欢迎 PR

---

## License

[MIT](LICENSE) © 2026 jiawenlyu12-cpu

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

## 安装方式

### 方式 A · 用 skills CLI（推荐）

```bash
npx skills add jiawenlyu12-cpu/pm-skills@daily-pm-study -g -y
```

`-g` 全局安装，`-y` 跳过确认。装完后在 Claude Code 里说"今日 PM 学习"即可触发。

### 方式 B · 手动 clone

```bash
git clone https://github.com/jiawenlyu12-cpu/pm-skills.git
mkdir -p ~/.claude/skills
cp -R pm-skills/daily-pm-study ~/.claude/skills/
```

下次新开 Claude Code 会话即可使用。

---

## 触发词

装好之后，对 Claude Code 说以下任一短语就会激活 `daily-pm-study`：

- "今日 PM 学习"
- "PM 学习"
- "今日产品功课"
- "daily pm study"

---

## 仓库结构

```
pm-skills/
├── daily-pm-study/
│   ├── SKILL.md                    # 主流程，171 行
│   └── references/
│       ├── books-curriculum.md     # 8 本书 × 127 章大纲
│       └── sources.md              # 数据源 + 过滤规则
├── README.md                       # 本文件
└── LICENSE                         # MIT
```

每个 skill 的工作目录（`~/ai-news/pm-study/` 下的 `progress.json` + HTML 日报）由 skill 在首次触发时自动创建，**不随仓库分发**。

---

## 想要加入或反馈

- 发现书目章节信息错误 / 建议数据源调整 → 提 Issue 或 PR
- 想加新 skill（如 `weekly-pm-review`、`product-decision-helper`）→ 欢迎 PR

---

## License

[MIT](LICENSE) © 2026 jiawenlyu12-cpu

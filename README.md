# slop-gauge: 中文 AI 痕迹确定性量表

一句话：**同一段文字永远同一个分数**的 de-AI 机器读数——量化中文文本的 AI 写作痕迹，改写前后给同一张表上的数字变化。

## 安装

**方式一 · skills 生态**（[skills.sh](https://skills.sh) / 市场用户）：

```bash
npx skills add Chendestiny/slop-gauge
```

> 整个仓库就是一个 skill bundle（根目录 SKILL.md + scripts + 词表），安装时整目录拷入 skills 目录。

**方式二 · de-ai 流水线组件**：

装 [de-ai-skills](https://github.com/Chendestiny/de-ai-skills) 后它作为双道门禁的机械臂自动就位（`~/.agents/skills/slop-gauge`）。

```bash
python ~/.agents/skills/slop-gauge/scripts/slop_gauge.py --help   # bash / zsh
```

```powershell
python "$env:USERPROFILE\.agents\skills\slop-gauge\scripts\slop_gauge.py" --help   # Windows
```

> `~` 在 PowerShell 里不展开，写成 `~/...` 会得到 `can't open file`；Windows 侧用 `$env:USERPROFILE` 或绝对路径。

## 它是什么

纯 stdlib（零依赖零联网）确定性量表，五类硬指标：

- **词汇**：AI 高频词密度/千字（词源照抄 humanizer-zh + RobinZorro86 Pattern34/37，致谢列在词表头）
- **标点画像**：破折号/粗体/感叹号/弯引号密度
- **句长突发性**：均值/标准差/变异系数——AI 句长均匀、人写忽长忽短
- **结构模式**：三段式序数链、"不是A而是B"否定排比、判断句三连、"从X到Y"虚假跨度
- **归因**："研究表明/专家表示"类无出处代言

三种场景档：`generic`（文章/汇报）/ `novel`（网文对话豁免）/ `ecommerce`（广告法极限词重罚）。

```bash
python scripts/slop_gauge.py --diff 原文.md 改后.md
```

## 与 stop-slop 的分工

stop-slop 改，slop-gauge 测——双道门禁的两端：LLM 出观感五维，机器出死数。两者都过才交付；检测器（朱雀等黑盒）**永远不是判据**。

## 许可证

MIT，见 [LICENSE](LICENSE)。词表致谢：humanizer-zh（op7418，MIT）、blader/humanizer、RobinZorro86/humanizer-zh-plus（MIT）。

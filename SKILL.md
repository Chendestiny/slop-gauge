---
name: slop-gauge
version: 1.0.1
display_name: 去AI味量表（slop-gauge）
display_name_en: Slop Gauge
description_zh: 中文文本 AI 痕迹确定性量表。纯标准库零依赖，测五类硬指标——AI 高频词密度、标点画像（破折号/粗体/感叹号）、句长突发性（变异系数）、三段式与否定/判断排比计数、模糊归因命中；支持单文件、目录批量、改写前后 diff 对账，输出 JSON 或人话报告。de-ai 双道门禁的机械侧，与 stop-slop 组队：stop-slop 改，slop-gauge 测。
description_en: Deterministic AI-slop meter for Chinese text. Pure-stdlib CLI scores five hard metric families - loansword density, punctuation profile, sentence-length CV, structural triads and contrasts, vague attributions - with single-file, batch and before/after diff modes, JSON or human-readable report. The mechanical arm of the de-ai dual QA gate, paired with stop-slop, which rewrites while this measures.
description: 中文 AI 痕迹确定性量化（词表/标点/节奏/结构/归因五类，可 diff 可批量），de-ai 双道门禁的机械侧。 Deterministic de-AI metrics scorer for Chinese text — stdlib-only, diff-able, batch-able.
allowed-tools:
  - Bash
  - Read
---

# slop-gauge: 中文 AI 痕迹确定性量表

测量，不判断，不改写。给同一段文本必出同一个分数；前后两版给同一张表上的数字变化。

血统：与 [stop-slop](https://github.com/hardikpandya/stop-slop)（LLM 五维观感评分）组队构成 de-ai 流水线的**双道门禁**——本工具出机器读数，stop-slop 出人味判断，两者都过才算质检合格。词源照抄不新造：[humanizer-zh](https://github.com/op7418/Humanizer-zh) §7 AI 词汇、[RobinZorro86/humanizer-zh-plus](https://github.com/RobinZorro86/humanizer-zh-plus) Pattern 34 四字格与套路结尾词。

## 何时用我

- 改写完成后，要"这台机器测的数据"而不是又一个 LLM 的感觉
- 批量扫描目录，找 AI 痕迹最重的段落和文件
- 改写前后对账（数字变化行，交付给用户看）

## 用法（全部零依赖零联网）

脚本装在 `~/.agents/skills/slop-gauge/scripts/slop_gauge.py`（或 `~/.dsh/skills/...`）。下面的相对路径写法要先 `cd` 进技能目录；在 agent 里直接调用请用绝对路径——注意 `~` 只有 bash/zsh 会展开，PowerShell 里要用 `$env:USERPROFILE`。

```bash
# 单文件
python scripts/slop_gauge.py 文章.md

# 目录批量（.md 与 .txt）
python scripts/slop_gauge.py --batch 目录

# 改写前后对比（核心场景）
python scripts/slop_gauge.py --diff 原文.md 改后.md

# 管道
cat 文章.md | python scripts/slop_gauge.py -
```

自检跑通：`python <上面的绝对路径> --help`；回归测试：技能目录下 `python -m unittest discover -s tests`（21 条，纯 stdlib，不需要 pytest）。

- `--profile generic | novel | ecommerce`（文章 / 网文对话豁免 / 带货广告法重罚）
- `--json` 机器可读；默认出人话报告

## 指标定义（v1.0.0，全部确定性）

| 族 | 指标 | 口径 |
|---|---|---|
| 词汇 | ai_density | AI 高频词命中数 / 千字，词表 data/ai_words_zh.txt（权重可加权） |
| 标点 | em/bold/exclaim/curly/ellipsis | 破折号密度超 1/千字起罚；novel 档对话引号内豁免 |
| 节奏 | mean / stdev / cv / max_equal_run | 句长变异系数 cv<0.25 起罚；连续 4 句±1 等长记一次；句数<3 不判 |
| 结构 | triads/negation_contrast/判断排比/序数链 | 正则计数，模式清单见脚本常数区 |
| 归因 | vague_attribution | "研究表明/专家表示"类无出处的权威代言 |
| 合成 | score 0-100 | 加权罚分见 PROFILES；≥55 机械参考线，门禁语义见 de-ai 路由 SKILL.md |

词表扩展：data/ai_words_zh.txt 一行一词+"空格 权重"，`-- adlaw` 段为广告法极限词（ecommerce 档重罚）。

## 边界

- 只测中文为主文本（中英混排 CJK 优先）；英文 prose 用 stop-slop
- 词表是固定清单——抓不住的痕迹测不到；它是测量仪不是玄学，90+ 分 ≠ 免检
- 数字只回答"有没有这些痕迹"，交付判断在 de-ai 双道门禁与人类评审
- 检测器（朱雀等黑盒）永远不是判据；本工具的数字是确定的，黑盒的是寄托

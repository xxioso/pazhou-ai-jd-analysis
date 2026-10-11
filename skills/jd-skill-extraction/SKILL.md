---
name: jd-skill-extraction
description: 把招聘岗位 JD 文本抽取成结构化技能标签，用于岗位需求统计与简历匹配。当用户提供岗位描述、招聘要求或一批 JD 文件并希望做需求分析时使用。
---

# JD 技能标签抽取

## 何时使用

- 需要把非结构化的岗位描述转成可统计的表格时
- 需要对比多个岗位的能力要求差异时
- 需要判断自己的简历与某个岗位的匹配度时

## 输出字段（固定 12 列）

字段定义见仓库 `docs/labeling-spec.md`，包括：id、company、title、location、level、days_per_week、duration、salary、skills_hard、skills_soft、is_entry、evidence。

## 标签口径

收敛为 20 类，同义词必须归并。完整对照表见 `docs/labeling-spec.md` 第二节。

- 编程类：Python / SQL / Excel
- AI 类：Prompt工程 / RAG / Agent / 模型评测 / 微调 / 数据标注规范 / 大模型应用
- 业务类：数据分析 / 竞品分析 / 需求文档 / 数据看板 / AB测试 / 用户增长 / 内容运营
- 其他：英语

## 执行步骤

1. 读取用户提供的 JD 文本（单条或批量）。
2. 对每条 JD，只抽取 JD 中**明确出现或明确隐含**的能力要求；不得凭岗位名称臆测。
3. 每条判断必须在 `evidence` 里给出 JD 原文依据；找不到依据的标签不输出。
4. 同义词按 `docs/labeling-spec.md` 的口径归并。
5. 输出为 Markdown 表格；批量时按公司名字母序排列。

## 质量检查（必须执行）

- 每条记录至少有 1 个 `skills_hard` 或 `skills_soft`，否则标记为 `待人工复核`
- `evidence` 为空的行数占比超过 10% 时，停止输出并提示用户检查输入质量
- 随机抽 20 条，与上一次运行结果比对，标签一致率低于 90% 时提示口径需要收敛

## 不要做的事

- 不要补全 JD 里没写的要求（"一般都会要求"这类推断禁止）
- 不要输出 JD 之外的公司背景信息
- 不要把实习补贴等薪资信息混入技能字段

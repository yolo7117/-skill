# Power BI DAX — Agent Skill

一个面向 AI 助手（Claude Code / WorkBuddy 等支持 [AgentSkills](https://skills.github.com) 规范的运行时）的 **Power BI DAX 专业技能**：写出在每个筛选上下文下都正确、在 VertiPaq 引擎上足够快的 DAX 度量值，并给出把慢报表诊断到"存储引擎 vs 公式引擎"级别的完整方法。

## 背景

本 Skill 基于 [SkillMedev/skills](https://github.com/SkillMedev/skills) 中的 `power-bi-dax` 初版 SKILL.md 补全而成：原项目只包含一个入口文件，本仓库补齐了它所需要的全部 **references（深度参考文档）、scripts（可运行工具）、assets（即用资产）**。

## 功能

- **正确性** — 筛选上下文 / 行上下文 / 上下文转换的完整心智模型，CALCULATE 求值顺序，变量语义，扩展表机制
- **时间智能** — 标记日期表三条件、YTD/QTD/MTD、PY/YoY/MoM/MAT、滚动窗口、财年与 4-4-5 日历、半累加度量、工作日智能
- **性能** — VertiPaq 压缩原理、存储/公式引擎拆分读数、模型端优化杠杆、度量改写模式对照表
- **避坑** — 12 类"静默算错"案例目录：总和对不上、平均百分比、RANKX 全 1 等，症状→根因→修法

## 目录结构

```
power-bi-dax/
├── SKILL.md                        # Skill 入口（工作流程 + 质量标准）
├── references/
│   ├── evaluation-context.md       #   求值上下文深度解析
│   ├── dax-function-reference.md   #   核心函数速查（含陷阱）
│   ├── time-intelligence.md        #   时间智能完整模式
│   ├── performance-vertipaq.md     #   性能诊断与优化
│   └── common-mistakes.md          #   常见静默出错案例
├── scripts/
│   ├── dax_lint.py                 #   DAX 静态检查器（8 条规则，纯标准库）
│   ├── test_dax_lint.py            #   lint 回归测试（19 用例）
│   └── generate_date_table.py      #   生成可标记日期表 DAX（支持财年/中英文）
└── assets/
    ├── core-measures.dax           #   现成度量库（20+，带业务定义）
    ├── measure-template.dax        #   新度量注释骨架
    └── date-table-sample.dax       #   日期表生成示例（2020–2035）
```

## 使用

把 `power-bi-dax/` 放入支持 AgentSkills 规范的技能目录：

- **Claude Code（项目级）**：`.claude/skills/power-bi-dax/`
- **Claude Code（全局）**：`~/.claude/skills/power-bi-dax/`
- **WorkBuddy（用户级）**：`~/.workbuddy/skills/power-bi-dax/`

当用户提出"为什么我的 DAX 度量值总计不对"、"写一个 YTD / 同比度量"、"报表很慢"、"CALCULATE 结果不符合预期"、"度量值 vs 计算列"等问题时自动触发。

## 工具脚本

```bash
# 交付前对度量文件做静态检查
python scripts/dax_lint.py measures.dax
python scripts/dax_lint.py measures.dax --format json   # 机器可读
python scripts/test_dax_lint.py                          # 运行回归测试

# 模型没有日期表？生成一个（勿手写）
python scripts/generate_date_table.py --start-year 2020 --end-year 2035
python scripts/generate_date_table.py --start-year 2020 --fiscal-start-month 7 --month-names en
```

两个脚本均只用 Python 标准库（3.6+），无任何 pip 依赖。

## 测试状态

`scripts/test_dax_lint.py`：19/19 通过（Python 3.13）。`assets/core-measures.dax` 通过 lint 自检零告警。

## License

MIT — 见 [LICENSE](LICENSE)。

> 注：SKILL.md 初版内容来自 SkillMedev/skills 项目（原仓库未标注许可证）。开源分发前建议向上游确认授权条款，或在仓库说明中保留对上游的引用。

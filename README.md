# multi-session-project（项目拆分组装法）

一个给 AI 编程助手使用的提示词技能（prompt skill）：用**总框架**约束整个项目（PRD 宪法 +
接口契约），把项目**拆分**成细分模块（任务包），每个模块有自己的**细分框架**（计划书）并
**预留上下游契约接口**，最后按契约**组装**。以**节省 token 为一级目标**（20 万 tokens
触发线是成本机制，与模型上下文长短无关），同时对抗长上下文的质量下降与信息丢失。

> English: A prompt skill for AI coding assistants. A master framework (PRD constitution +
> interface contract with schemas & validators) constrains the whole project; the project is
> split into session-sized task packages, each with its own sub-framework and reserved
> upstream/downstream contract interfaces; finally all packages are assembled against the
> contract. Token economy is a first-class goal (the 200k-token trigger is a cost mechanism,
> not a capacity one). Includes status gates, cross-document consistency analysis, handoff
> reports, requirement traceability, and doc-drift checks. The skill body is written in
> Chinese; see the workflow summary below.

## 适用场景 / When to use

满足任一即启用：
- 预计单会话累计上下文消耗超过 **20 万 tokens**（成本机制——模型上下文再长也要拆：
  上下文重读成本随轮次累积）。
- 功能超过 3 个模块、预计跨越多个会话（或多个子代理）。
- 主会话预计派生子代理 ≥ 4 个（编排复杂度维度）。

小项目、一次性脚本不要用——这套流程本身有成本。

## 核心方法 / The method at a glance

八步全流程（三条暗线全程贯穿：状态门进度总览 / 变更通道 / 文档即事实源）：

1. **目标确认** — 与用户确认目标卡，含糊处当场问清，不把歧义带进 PRD。
2. **总框架之 PRD** — 跨会话"宪法"：功能需求逐条编号、明确不做的范围、验收标准、变更记录。
3. **总框架之接口契约** — 组装标准：模块边界、数据 schema（含示例）、API 签名、
   契约冻结规则；schema 有代码级唯一权威来源并配校验器当"检具"。
4. **拆分** — 任务包以"一个会话能装下"为粒度；粒度平衡（接口尽量少）与易变隔离（会变的需求
   圈进少数模块）。
5. **细分框架** — 每包一份计划书，上下游接口只引用契约条目，不另起炉灶。
6. **一致性分析 + 最小验证** — 施工前 PRD↔契约↔拆解↔计划书强制对缝；先花最小成本试通
   最险的一段，不成立回退改总框架。
7. **实施与组装** — 四种执行载体（后台子代理 / 目标自动续轮 / 独立验证轮 / 真·新会话），
   交付自带契约级测试；集成会话按契约联调。
8. **总验收 + 复盘** — 按 PRD 验收标准逐条过（组装 ≠ 完工），踩坑与决策写回知识库，
   反哺下一个项目的总框架。

强化机制：**状态门**（草稿≠通过，状态推进只认证据）、**交接简报**（每包结束写交付报告，
含文档漂移检查，下一会话靠它接力不靠记忆）、**需求追踪**（需求编号→任务包→验收证据映射）。

## 文件结构 / Repository layout

```
multi-session-project/
├── SKILL.md      # 技能本体（含 frontmatter：name/description/whenToUse）
└── README.md     # 本文件
```

## 安装使用 / Installation

把 `SKILL.md` 放进你的 AI 编程助手的技能目录（例如 `~/.agents/skills/multi-session-project/`），
或按你所用工具的技能加载方式注册。技能为纯提示词类型，无任何代码依赖。

使用时直接告诉助手："用 multi-session-project 方法做这个项目"，或提到
"项目拆分组装法 / 多会话 / 拆任务 / 分部件开发"等关键词触发。

## 来源与致谢 / Credits

方法提炼自一个真实生产项目（市政管网算量与清单桌面软件，十余个任务包完整走通，
含"中途插入新需求"的升版实例）。v1.1 吸收前驱框架，v1.2（2026-08）按用户反馈
更名「项目拆分组装法」、将节省 token 升为一级目标：

- [github/spec-kit](https://github.com/github/spec-kit)：跨文档一致性分析（analyze）、
  收敛评估（converge）、澄清前置（clarify）。
- [felipefontoura/pi-sdd-kit](https://github.com/felipefontoura/pi-sdd-kit)：状态门
  （.status，草稿≠批准）、交接简报（handoff brief）、可追踪 ID、EARS 需求句式、
  steering 持久上下文、语言策略。
- 2026 波规格驱动工程思路：[AWS Kiro](https://kiro.dev)（The Plan Is the Product）、
  kspec / OpenSpec。

## 许可证 / License

MIT，详见 [LICENSE](LICENSE)。

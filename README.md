# multi-session-project（大型项目多会话开发法）

一个给 AI 编程助手使用的提示词技能（prompt skill）：把大型软件项目当汽车造——
先定整车图纸（PRD），再定零部件接口标准（接口契约），每个会话只造一个部件（任务包），
最后按契约总装。**对抗单会话上下文膨胀导致的质量下降与信息丢失。**

> English: A prompt skill for AI coding assistants that splits a large project across
> multiple sessions/subagents without quality decay — a PRD as the shared constitution,
> an interface contract as the assembly standard (with schemas + validators), session-sized
> task packages, status gates, handoff reports and requirement traceability. Inspired by
> GitHub spec-kit and pi-sdd-kit, distilled from a real production project. The skill body
> is written in Chinese; see the workflow summary below.

## 适用场景 / When to use

- 功能超过 3 个模块、预计跨越多个会话（或多个子代理）开发的项目。
- 单会话装不下、上下文一长质量就崩的项目。
- 小项目、一次性脚本不要用——这套流程本身有成本。

## 核心方法 / The method at a glance

1. **目标确认** — 与用户确认目标卡，含糊处当场问清，不把歧义带进 PRD。
2. **PRD（产品需求文档）** — 跨会话的"宪法"：功能需求逐条编号、明确不做的范围、
   验收标准、变更记录。改 PRD 必须经用户确认。
3. **接口契约** — 部件组装标准：模块边界、数据 schema（含示例）、API 签名、
   契约冻结规则；schema 有代码级唯一权威来源并配校验器当"检具"。
4. **会话任务拆解 + 任务包计划书** — 任务包以"一个会话能装下"为粒度；
   每包一份计划书，上下游接口只引用契约条目，不另起炉灶。
5. **分会话实施与总装** — 子代理（默认）或真·新会话逐包施工，交付自带契约级测试；
   集成会话按契约联调，按 PRD 验收标准逐条总验收。

强化机制：**状态门**（草稿≠通过，状态推进只认证据）、**跨文档一致性分析**
（施工前强制 PRD↔契约↔拆解↔计划书对缝）、**交接简报**（每包结束写交付报告，
下一会话靠它接力不靠记忆）、**需求追踪**（需求编号→任务包→验收证据映射）。

## 文件结构 / Repository layout

```
multi-session-project/
└── SKILL.md      # 技能本体（含 frontmatter：name/description/whenToUse）
```

## 安装使用 / Installation

把 `SKILL.md` 放进你的 AI 编程助手的技能目录（例如 `~/.agents/skills/multi-session-project/`），
或按你所用工具的技能加载方式注册。技能为纯提示词类型，无任何代码依赖。

使用时直接告诉助手："用 multi-session-project 方法做这个项目"，或提到
"多会话 / 拆任务 / 分部件开发"等关键词触发。

## 来源与致谢 / Credits

方法提炼自一个真实生产项目（市政管网算量与清单桌面软件，十余个任务包完整走通，
含"中途插入新需求"的升版实例）。v1.1 起吸收：

- [github/spec-kit](https://github.com/github/spec-kit)：跨文档一致性分析（analyze）、
  收敛评估（converge）、澄清前置（clarify）。
- [felipefontoura/pi-sdd-kit](https://github.com/felipefontoura/pi-sdd-kit)：状态门
  （.status，草稿≠批准）、交接简报（handoff brief）、可追踪 ID、EARS 需求句式、
  steering 持久上下文、语言策略。

## 许可证 / License

MIT，详见 [LICENSE](LICENSE)。

# AI Agent 的 Harness 工程：当"脚手架"成为核心竞争力

> 模型是发动机，Harness 是整车。最好的发动机没有方向盘和刹车，哪里也去不了。

## 一、从一个反直觉的事实说起

2026 年 2 月，OpenAI 披露了一组令人震惊的数据：一支 3-7 人的工程团队，用 5 个月时间构建了一个超过 **100 万行代码** 的生产级应用。其中 **零行代码由人类手写**，每一行都由 AI Agent 生成。产品拥有内部日常用户和外部 Alpha 测试者，能自主部署、自主修复 Bug——整个过程没有人类碰过代码编辑器。

工程师们做了什么？他们设计了 **Harness**——约束、反馈循环、文档结构、依赖规则、生命周期管理。Agent 写代码，人类设计让 Agent 可靠运行的环境。

同一时期，LangChain 的编码 Agent 通过 **零模型改动**，仅优化 Harness，在 Terminal Bench 2.0 上从 52.8% 跃升至 66.5%——从 Top 30 直接杀入 Top 5。Vercel 删除了 Agent 80% 的工具，反而获得了更好的结果。

这些案例指向一个正在被业界快速接受的结论：**模型是商品（commodity），Harness 才是护城河（moat）。**

---

## 二、什么是 Harness？

### 2.1 一个直觉性的比喻

"Harness"一词源自马具——缰绳、马鞍、马辔——将一匹强壮但不可预测的动物引导向正确方向的完整装备组合。这个比喻非常精准：

- **马** 是 AI 模型——强大、快速，但不知道该往哪里走
- **Harness** 是基础设施——约束、护栏、反馈循环，将模型的力量导向有生产力的方向
- **骑手** 是人类工程师——提供方向，但不需要亲自跑

没有 Harness 的 AI Agent，就像一匹纯种马在开阔的田野上奔跑——速度快、令人印象深刻，但对完成任何实际任务毫无用处。

### 2.2 正式定义

Martin Fowler（ThoughtWorks 首席技术顾问）给出了一个简洁的公式：

> **Agent = Model + Harness**

Harness 是"Agent 中除模型之外的一切"。它是一个系统的设计与实现，核心功能包括：

1. **约束（Constrain）**：限制 Agent 能做什么——架构边界、依赖规则、权限控制
2. **告知（Inform）**：告诉 Agent 应该做什么——上下文工程、文档、提示词
3. **验证（Verify）**：检查 Agent 是否做对了——测试、Lint、CI 校验
4. **纠正（Correct）**：当 Agent 犯错时纠正——反馈循环、自我修复机制

### 2.3 学术界的确认

2026 年，中国人民大学（RUCAIBox 团队）发表了系统综述论文《Agent Systems with Harness Engineering》，引用 502 篇参考文献，将 Harness 正式确立为 LLM Agent 系统中的一等公民概念。同期另一篇论文《Agent Harness for Large Language Model Agents: A Survey》也独立得出相同结论：

> **Harness——而非底层模型——是决定 Agent 可靠性的主要因素。**

---

## 三、三年的范式迁移：严谨从未消失，它只是在搬家

理解 Harness 的重要性，需要看清楚过去三年 AI 工程范式的完整演变。

### 3.1 时代一：提示词工程（Prompt Engineering，2022-2024）

2022 年 6 月 GitHub Copilot 上线，11 月 ChatGPT 发布。一夜之间，所有人都认为 AI 开发的核心问题是"怎么跟模型说话"。Andrej Karpathy 称之为"Software 3.0"——自然语言就是新的编程语言。

这个时代诞生了重要的学术成果：
- **Chain-of-Thought**（2022）：让模型"逐步思考"，GSM8K 数学准确率从 17.9% 跳到 58.1%
- **ReAct**（2022）：模型交替进行思考和行动——所有现代 Agent 的原型
- **Andrew Ng 的四大设计模式**（2024）：反思、工具使用、规划、多 Agent 协作

Andrew Ng 的关键发现至今仍有指导意义：**用 Agentic 工作流包装 GPT-3.5，在某些基准上超过 GPT-4 的零样本表现。** 不升级模型，改变模型周围的模式就能获得性能飞跃——这已经是 Harness 思想的萌芽。

**但这个时代撞到了墙。** 一个团队花了三周打磨 Agent 的提示词："遵循现有编码规范"、"写测试"、"不要留无用的 import"。指令越来越长，简单任务一切正常。项目一扩大就崩溃——Agent 开始忽略已有的工具函数，自己从头写。无论你在提示词里写多少遍"复用现有代码"，如果工具文件不在上下文窗口里，Agent 就不知道它存在。

**死亡原因：** 严谨不属于提示词文本，而属于提示词所消费的**上下文**。

### 3.2 时代二：上下文工程（Context Engineering，2025）

2025 年 6 月，Shopify CEO Tobi Lütke 在 X 上写道：

> "我更喜欢'上下文工程'这个词，而不是'提示词工程'。它更好地描述了核心技能——为 LLM 提供所有使任务可解的上下文的艺术。"

核心问题从"**我该说什么**"变成了"**我该提供什么信息**"。

Anthropic 将上下文策划分为四大策略：

| 策略 | 做什么 | 类比 |
|------|--------|------|
| **Write** | 编写清晰、结构化的系统提示词 | 写一份好的产品说明书 |
| **Select** | 只拉取任务相关的上下文，而非整个知识库 | 查阅相关章节，而不是通读全书 |
| **Compress** | 将 20 轮对话压缩为摘要 | 做读书笔记 |
| **Isolate** | 将子任务委托给独立的子 Agent | 请专门的顾问处理专业问题 |

Google ADK 实现了具体的"上下文栈"架构：将上下文窗口分为**稳定前缀**（系统提示词、Agent 身份、工具定义）和**可变后缀**（最新用户输入、工具输出），并通过 KV-cache 优化大幅降低成本——Claude Sonnet 上一次缓存命中可降低 **10 倍**成本。

Manus 团队重写了四次 Agent 框架后得出结论：**KV-cache 命中率是生产 Agent "唯一最重要的指标"。**

**但上下文工程也撞到了墙。** 三个根本问题无法通过上下文解决：

1. **单轮局限性**：上下文工程关注的是"这次 API 调用放什么"，但 Agent 是多轮链式决策，第 15 轮需要第 3 轮已被压缩掉的工具结果怎么办？
2. **没有错误恢复**：工具调用失败、模型幻觉、成本飙升——上下文再完美，运行时异常需要独立的机制处理
3. **安全盲区**：上下文工程解决"放什么进来"，但不解决"如何防止坏事发生"

**死亡原因：** 即使完美的上下文，当消费它的系统设计糟糕时也会失败。上下文是必要条件，不是充分条件。严谨需要再次搬家。

### 3.3 时代三：Harness 工程（Harness Engineering，2026 至今）

2026 年 2 月，Mitchell Hashimoto（Vagrant 和 Terraform 创始人、HashiCorp 联合创始人）发表文章《My AI Adoption Journey》，记录了他从 AI 怀疑论者到获得真实成果的历程。他得出的结论：

> **每当 Agent 犯错，就改变系统，让这个错误在结构上不可能再次发生。**

不是修复提示词，而是改变 Agent 周围的系统——规则、工具、约束、反馈循环。Harness 工程一词由此诞生。

接下来两周内，OpenAI、Martin Fowler、Wharton 教授 Ethan Mollick **独立发表**了相同的结论。没有事先协调——所有人都撞到了同一面墙。科学史称之为"多重发现"（multiple discovery）——这个想法的时代到了。

### 3.4 前沿：自然语言 Agent Harness（NLAH，2026）

2026 年 3 月，一项来自学术界的探索提出了一个更进一步的问题：**Harness 的逻辑是否可以用自然语言文档来表达，而非代码？**

传统 Harness 的逻辑深埋在控制器代码中——难以检查、难以比较、难以迁移。论文《Natural-Language Agent Harnesses》引入了两个新概念：

- **NLAH（Natural-Language Agent Harnesses）**：可编辑的自然语言文档，描述运行级 Harness 策略（agent 调用、交接、状态更新、验证门、产物契约）
- **IHR（Intelligent Harness Runtime）**：共享运行时，将这些文档解释为可执行的 agent 编排

在编码、终端使用和计算机使用基准测试中，IHR 执行的 NLAH 达到了与代码和提示词实现相当的任务结果，同时暴露了更短的静态 Harness 策略。模块消融实验进一步表明，显式的 Harness 模块是可分析的。

这意味着：**Agent Harness 可以从"模型周围的偶然胶水代码"转变为"科学的表示对象"**——可版本控制、可 diff、可跨运行时迁移。这为 Harness 工程的下一阶段打开了大门。

### 3.5 自进化 Harness：当 Harness 学会自我改进（2026）

2026 年 6 月，两篇论文将 Harness 工程推进到了一个新的高度：**不只是人类设计 Harness，而是让 Agent 自己改进自己的 Harness。**

《Self-Harness: Harnesses That Improve Themselves》提出了一个 **Weakness Mining → Proposal → Validation** 三阶段闭环：

1. **Weakness Mining（弱点挖掘）**：Agent 分析自身失败轨迹，自动识别 Harness 的系统性缺陷——不是"这次调错了参数"，而是"工具描述缺少错误处理指引"
2. **Proposal（提议改进）**：Agent 针对挖掘到的弱点，生成具体的 Harness 修改方案
3. **Validation（验证）**：在隔离环境中验证修改是否真正提升了成功率，只有验证通过的修改才合入

这个闭环完全自动化运行，无需人类工程师介入。在多个模型家族的 Terminal-Bench-2 上验证了有效性。

同期，《SIA: Self Improving AI with Harness & Weight Updates》更进一步：**同时更新 Harness 和模型权重。** 在法律分类、GPU 内核优化和 RNA 去噪三个任务上，联合更新（Harness + 权重）的表现**显著超越纯 Harness 迭代**——说明最好的改进策略是"内外兼修"。

这两篇论文与 AHE（Agentic Harness Engineering，第四章）一起，勾勒了一条清晰的轨迹：

```
人类设计 Harness → AI 辅助优化 Harness（AHE）→ AI 自主改进 Harness（Self-Harness）→ AI 同时优化 Harness + 模型（SIA）
```

Harness 工程正在从一门"手工工艺"变成一门"自动科学"。

### 3.6 Loop Engineering：当 Harness 装上时钟（2026.06）

2026 年 6 月 7 日，一个概念在硅谷 AI 圈集中爆发。三个人——代表两个阵营——**独立表达了完全相同的观点**。

OpenAI Codex 背后的 OpenClaw 创始人 **Peter Steinberger** 发了一条 500 万浏览的推文：

> "你不该再给编程 Agent 写提示词了。你应该设计循环来驱动它们。"

同一天，Anthropic Claude Code 负责人 **Boris Cherny** 在访谈中说：

> "我不再手动提示 Claude。我有一堆循环在运行，它们提示 Claude 并判断下一步。我的工作是写循环。"

Google Cloud AI 总监 **Addy Osmani** 随即将这个概念体系化，命名为 **Loop Engineering**，并给出了最精炼的定义：

> **Loop Engineering 是用系统替代你自己来提示 Agent。你设计系统，系统驱动 Agent。**

#### 与 Harness 的关系：多一层，非替代

Osmani 在文章中明确定位了 Loop 与 Harness 的关系：

> "Loop engineering sits one floor above the harness. The harness but it runs on a timer, it spawns little helpers, and it feeds itself."

**Loop 不是替代 Harness，而是 Harness 装上时钟。** 四层演进并非互相替代，而是逐层叠加：

```
Loop Engineering      ← 何时运行？运行多久？谁来决定下一步？（2026.06）
    ↑ 依赖             "Harness + 定时器 + 自驱动"
Harness Engineering   ← 如何约束？如何验证？如何恢复？（2025-2026）
    ↑ 依赖             "Agent 运行的完整环境"
Context Engineering   ← 提供什么信息？如何组织上下文？（2024-2025）
    ↑ 依赖             "让 Agent 看到正确的信息"
Prompt Engineering    ← 说什么？怎么说？（2022-2024）
                      "单次交互的质量"
```

**关键约束**：一个 Loop 只有在底层 Harness 扎实的情况下才能可靠运行。Skills 没写好、Sub-agent 验证不靠谱、Connector 没接好——Loop 只会加速生产垃圾。这就像给一匹没有缰绳的马装上自动跑圈程序——跑得更远，但更危险。

#### 五大构建块 + 记忆

Osmani 将 Loop 拆解为六个要素。**Claude Code 和 OpenAI Codex 均已原生支持全部六项**：

| 原语 | 作用 | Claude Code 实现 | Codex 实现 |
|------|------|------------------|-----------|
| **Automations** | 按计划自动触发，发现和分类任务 | `/loop`、`/goal`、hooks、cron | Automations 面板、`/goal` |
| **Worktrees** | 多 Agent 并行隔离，互不冲突 | `git worktree`、`--worktree`、subagent isolation | 内置 worktree per thread |
| **Skills** | 项目知识写一次，每次自动注入 | `SKILL.md`、Skill 工具 | `SKILL.md`、`$name` 调用 |
| **Connectors** | 接入真实工具（工单、Slack、数据库） | MCP servers + Plugins | MCP Connectors + Plugins |
| **Sub-agents** | maker/checker 分离，避免自己审自己 | `.claude/agents/` + agent teams | `.codex/agents/` TOML 定义 |
| **State** | 跨会话持久化进度，Loop 的脊柱 | Markdown 或 Linear via MCP | Markdown 或 Linear connector |

Osmani 的核心观察：**两套工具的六个原语完全对应。** 一旦注意到形状是一样的，就不再争论用哪个工具——只设计一个不管在哪都能跑的 Loop。

#### 一个完整的 Loop 实例

```
每天早上 automation 触发
  → 读取昨天的 CI 失败 / issue / commits
  → 为每个值得处理的问题开独立 worktree
  → 派 worker agent 起草修复
  → 派 reviewer agent 对照 skill 规范审查
  → connector 自动开 PR、更新工单
  → 处理不了的进入 triage 收件箱等人审阅
  → state 文件记录进度，明天继续
```

**人只设计了一次，没有手动提示任何一个步骤。**

#### 两种运行模式

Loop Engineering 提供两种互补的运行模式：

| 模式 | 命令 | 行为 | 适用场景 |
|------|------|------|----------|
| **定时循环** | `/loop` | 按 cron 定时重复执行 | 每日 issue 分类、CI 失败汇总、定期代码审查 |
| **目标驱动** | `/goal` | 持续运行直到条件满足，独立小模型判定完成 | "所有测试通过且 lint 干净"、"这个 Bug 已修复并验证" |

`/goal` 的关键设计：判定完成的不是写代码的那个 Agent——是另一个独立的小模型。**maker/checker 分离被应用到了"停止条件"本身。**

#### 争议与风险

Loop Engineering 并非没有争议。Osmani 自己列出了三个随 Loop 变好而**加剧**的风险：

1. **验证仍然是你的事**："一个无人看管的 Loop 也在无人看管地犯错。'做完了'是一个声称，不是证明。"
2. **理解债加速积累**：Loop 越顺滑，你写的代码和你真正理解的代码之间的鸿沟越大。**不读 Loop 产出的代码，理解就会腐烂。**
3. **认知投降**：当 Loop 自己跑起来，舒适姿势是最危险的——你很容易不再有自己的判断，只是接受它给的东西。

> "设计 Loop 是解药——当你带着判断力做的时候；当你用它逃避思考的时候，它就是加速器。同样的行动，相反的结果。"

Cherny 本人也澄清了自媒体对他的过度解读：**Loop 没有杀死提示词工程，提示词只是内嵌在循环逻辑中。** 他的观点不是"工作变简单了"，而是**"杠杆支点移动了"**。

61% 的社区回应是负面/怀疑的。但同样的怀疑也曾伴随 Prompt Engineering、Context Engineering 和 Harness Engineering 的早期阶段。

#### 学术界的同步验证

Loop Engineering 的概念在学术界有独立的、更早的研究线索：

| 论文 | 核心贡献 |
|------|----------|
| [Structured Graph Harness](https://arxiv.org/abs/2604.11378)（2026.04） | 将 Agent 循环形式化为三层 DAG（规划/执行/恢复），控制流从隐式上下文提升为显式静态图 |
| [From Agent Loops to Deterministic Graphs](https://arxiv.org/abs/2605.06365)（2026.05） | 将 Loop 收敛后的轨迹固化为可复现的执行 DAG，实现完美上下游保持 |
| [LOOP Skill Engine](https://arxiv.org/abs/2605.14237)（2026.05） | 记录一次 Agent 轨迹后确定性回放，99% 成功率、节省 93-99.98% token |
| [Hacker-Fixer Loop](https://arxiv.org/abs/2606.08960)（2026.06） | 三 Agent 对抗循环，将验证器漏洞从 62% 打到 0% |
| [Continual Harness](https://arxiv.org/abs/2605.09998)（2026.05） | 无重置自改进 Harness，从轨迹数据自动优化 prompts/sub-agents/skills/memory |

学术界的共识方向与工业界一致：**从随机循环 → 确定性格局 → 自我进化闭环。** Loop Engineering 是这条线上最新的工程实践落点。

---

## 四、Harness 的核心解剖

### 4.1 Fowler 的四象限框架

Martin Fowler 和 Birgitta Böckeler（ThoughtWorks 首席技术顾问）将 Harness 组织为一个清晰的 2x2 框架：

|  | **前馈（预防引导）** | **反馈（事后纠正）** |
|---|---|---|
| **确定性** | **指南**：AGENTS.md、.cursorrules、编码规范 | **计算型**：编译器、类型检查、Linter |
| **非确定性** | **系统提示词**：角色定义、行为约束、少样本示例 | **推断型**：LLM-as-judge、语义代码审查 |

- **左上角——指南**：在 Agent 开始之前就将其引向正确方向。把"使用 Vanilla JS 而非 React"写进 AGENTS.md，Agent 生成代码时会参考。成本几乎为零，但没有强制力——Agent 可以忽略它。
- **右上角——计算型**：即使指南被忽略，也能机械性地捕获错误。编译错误、Linter 警告、类型检查、静态分析。Agent 写了坏代码，拿到编译错误后自动修正。
- **左下角——系统提示词**：处理确定性规则无法捕获的细微之处。"对用户礼貌"、"不确定时请确认"、"安全敏感操作必须获得审批"。
- **右下角——推断型**：捕获"能编译但语义错误"的代码。让另一个 LLM 审查代码或评估输出质量。Anthropic 的 Evaluator Agent 就在这个象限。

**生产级 Harness 需要四象限全部覆盖——纵深防御。**

2026 年 4 月，Birgitta Böckeler 在 Martin Fowler 网站发表的深度文章《Harness Engineering for Coding Agent Users》将这一框架进一步演进为**控制系统模型**：

- **Guides（前馈引导）**：在 Agent 行动前将其引向正确方向——规则、Skills、上下文
- **Sensors（反馈感知）**：在 Agent 行动后观测结果并触发自我纠正——Linter、测试、结构分析
- **Steering Loop（转向循环）**：人类在发现失败模式时迭代改进 Harness——形成一个持续收敛的外部控制环

这个模型将 Harness 类比为**控制论调速器**（cybernetic governor）：结合前馈和反馈，持续将代码库调节到期望状态。它与四象限框架一脉相承，但更强调 Harness 作为**动态控制系统**而非静态配置的本质。

### 4.2 CAR 框架：学术界的结构化分解

2026 年 3 月，一篇发表在 Preprints 的论文提出了 **CAR 框架**——将 Harness 层分解为三个正交维度：

| 维度 | 职责 | 示例 |
|------|------|------|
| **Control（控制）** | 定义 Agent 能做什么、不能做什么 | 权限策略、架构约束、工具白名单、审批流程 |
| **Agency（代理）** | 定义 Agent 如何获得和执行任务 | 任务分解、上下文组装、子 Agent 协调、工具调用编排 |
| **Runtime（运行时）** | 定义 Agent 运行的基础设施 | 执行环境、状态管理、错误恢复、日志与可观测性 |

CAR 框架与 Fowler 的四象限形成互补：四象限描述了 Harness **需要覆盖哪些类型**的约束，CAR 描述了 Harness **内部的结构层次**。两者结合可以更系统地诊断 Harness 的缺口——是缺少某一类型的约束（四象限视角），还是某一层的实现不完整（CAR 视角）。

### 4.3 三大支柱

OpenAI 的框架将 Harness 工程组织为三大核心类别：

#### 支柱一：上下文工程（Context Engineering）

确保 Agent 在正确的时间拥有正确的信息。

- **静态上下文**：仓库内的文档（架构规范、API 契约、风格指南）、AGENTS.md 或 CLAUDE.md 文件
- **动态上下文**：可观测性数据（日志、指标、链路追踪）、目录结构映射、CI/CD 流水线状态和测试结果

**关键原则：** 从 Agent 的角度看，任何无法在上下文中访问的东西都不存在。存在于 Google Docs、Slack 线程或人们脑中的知识对系统是不可见的。**仓库必须是唯一的事实来源。**

#### 支柱二：架构约束（Architectural Constraints）

这是 Harness 工程与传统 AI 提示词分歧最大的地方。不是告诉 Agent "写好代码"，而是**机械性地强制好代码的样子**。

```
Types → Config → Repo → Service → Runtime → UI
```

每一层只能从左边的层导入——这不是建议，而是由结构性测试和 CI 校验强制执行的。

**约束工具链：**
- 确定性 Linter——自定义规则自动标记违规
- LLM 审计器——Agent 审查其他 Agent 代码的架构合规性
- 结构性测试——类似 ArchUnit，但针对 AI 生成的代码
- 预提交钩子——代码提交前自动检查

**反直觉的发现：** 约束解决方案空间反而让 Agent **更高效**。当 Agent 可以生成任何东西时，它浪费大量 token 探索死胡同。当 Harness 定义了清晰的边界，Agent 更快收敛到正确方案。

#### 支柱三：熵管理（Entropy Management）

最被低估的组件。随着时间推移，AI 生成的代码库会积累熵——文档偏离现实、命名约定分化、死代码堆积。

Harness 工程通过**定期清理 Agent** 来解决：
- 文档一致性 Agent——验证文档与当前代码匹配
- 约束违规扫描器——找到绕过早期检查的代码
- 模式执行 Agent——识别并修复与既定模式的偏差
- 依赖审计器——跟踪并解决循环或不必要的依赖

这些 Agent 按计划运行——每天、每周或由特定事件触发——保持代码库对人类审查者和未来 AI Agent 都是健康的。

---

## 五、谁在实践 Harness？真实案例

### 5.1 OpenAI Codex：100 万行零手写代码

OpenAI 团队的做法可以浓缩为三件事：

1. **将仓库知识系统化**：团队发现 Agent 总犯同样的错误。为什么？架构决策只存在于 Slack 中，设计原则只存在于资深开发者的脑中——对 Agent 不可见。他们把每条原则和决策都写成仓库内的 Markdown 和代码。**"Agent 看不见的知识不存在。"**

2. **机械性强制**：光写文档不够。"请使用这个模式"写在文档里会被忽略。他们构建了自定义 Linter 和结构性测试来强制执行架构规则。Agent 自动修复代码以通过 Linter。人类审查者被机械性强制取代。

3. **渐进式披露**：一开始他们尝试一次性注入大量文档。结果：Agent 迷失了。解决方案是给 Agent 一张地图，让它自己找到需要的东西。"给 Codex 一张地图，而不是一本 1000 页的手册。"

### 5.2 Anthropic 的三 Agent 架构

Anthropic 团队发现了一个惊人的问题：**Agent 无法准确评估自己的工作。** 就像学生不该给自己的考试打分一样。

受 GAN（生成对抗网络）启发，他们将 Agent 一分为三：

- **Planner（规划者）**：将简单提示词扩展为详细产品规格。关注雄心勃勃的范围和高层设计，而非技术细节
- **Generator（生成者）**：一次实现一个功能。自我评估每个冲刺，然后交给 QA
- **Evaluator（评估者）**：通过 Playwright 运行端到端测试。从产品深度、功能性、视觉设计和代码质量四个维度评分。低于阈值？带着具体反馈打回给 Generator

成本从单 Agent 运行的 20 分钟约 $200，变为三 Agent 的约 $4,400（22 倍增长）。但这是**成本的重定位**——从人类事后修复，变为系统事前验证。

### 5.3 LangChain：纯 Harness 优化的飞跃

LangChain 的编码 Agent 在 Terminal Bench 2.0 上的表现：

| 改动 | 做了什么 | 影响 |
|------|----------|------|
| 自验证循环 | 添加完成前检查清单中间件 | 在提交前捕获错误 |
| 上下文工程 | 启动时映射目录结构 | Agent 从一开始就理解代码库 |
| 循环检测 | 跟踪重复文件编辑 | 防止"死亡循环" |
| 推理三明治 | 规划/验证用高推理，实现用中等推理 | 在时间预算内获得更好质量 |

**同一模型，不同 Harness，结果天壤之别。**

### 5.4 Vercel：减法优化

Vercel 一开始为 Agent 提供了全面的工具库——搜索、编码、文件、API 工具，应有尽有。结果很糟糕：Agent 困惑了，做冗余调用，走不必要的步骤。

Vercel 剥离到只剩核心。删除冗余选项，简化决策。Agent 变得更快、更可靠。

**最好的 Harness 改进往往来自"做更少"。**

### 5.5 Stripe 的 Minion 系统

Stripe 内部的编码 Agent（称为 Minions）现在每周产出 **超过 1,000 个合并的 Pull Request**：

1. 开发者在 Slack 发布任务
2. Minion 编写代码
3. Minion 通过 CI
4. Minion 创建 PR
5. 人类审查并合并

步骤 1 到 5 之间没有人类干预。Harness 处理了一切——测试执行、CI 验证、风格合规和文档更新。

### 5.6 AHE：Harness 的自动进化

2026 年 4 月，来自复旦大学等机构的论文《Agentic Harness Engineering》提出了一个封闭循环系统，让 Harness **自动进化**而非手工调整。AHE 通过三个可观测性支柱实现：

- **组件可观测性**：每个可编辑 Harness 组件都有文件级表示，操作空间显式且可回滚
- **经验可观测性**：将数百万原始轨迹 token 提炼为分层证据语料库
- **决策可观测性**：每次编辑配对自声明预测，下一轮用任务结果验证——将每次编辑变成可证伪的合约

**量化结果**：十轮 AHE 迭代将 Terminal-Bench 2 的 pass@1 从 69.7% 提升至 77.0%，**超过了人类设计的 Codex-CLI（71.9%）**。更重要的是，冻结的 Harness 无需重新进化即可迁移——在 SWE-bench-verified 上以比种子配置少 12% 的 token 达到最高总成功率，跨三个不同模型家族获得 +5.1 到 +10.1pp 的提升。

消融实验的关键发现：**收益来自工具、中间件和长期记忆，而非系统提示词**——说明"事实性 Harness 结构"可以迁移，而"散文式策略"不能。

### 5.7 AutoHarness：小模型 + 自动 Harness > 大模型

Google DeepMind 团队在 TextArena 145 款游戏中测试了一个反直觉的假设：用较小的模型自动合成 Harness，能否超越更大的模型？

在 Kaggle GameArena 国际象棋比赛中，Gemini-2.5-Flash **78% 的失败来自非法走法**——模型能力足够，但缺少规则约束。团队让 Gemini-2.5-Flash 通过少量迭代代码优化（基于环境反馈）自动合成 Harness：

- 生成的 Harness 在 145 款 TextArena 游戏中**完全防止了所有非法走法**
- 配备自动 Harness 的 **Gemini-2.5-Flash 超越了 Gemini-2.5-Pro**
- 将技术推到极限：将**整个策略生成为代码**，完全消除了推理时的 LLM 调用——在 16 款单人游戏中获得了比 Gemini-2.5-Pro 和 GPT-5.2-High 更高的平均奖励

**核心启示：用较小的模型合成自定义代码 Harness（甚至完整策略），可以超越更大的模型，同时成本更低。** 这与 LangChain 的"纯 Harness 优化"和 AHE 的"自动进化"形成了三角验证——Harness 的价值在多个独立研究中得到确认。

### 5.8 LangChain 2026 年代理工程调查：生产化现状

LangChain 对 1,300+ 专业人士的调查揭示了 Agent 工程在 2026 年的真实落地状况：

| 指标 | 数据 |
|------|------|
| Agent 已在生产中 | **57%**（同比增长 6pp） |
| 已实现可观测性 | **89%**（生产团队中 94%） |
| 已运行离线评估 | 52% |
| 质量是最大障碍 | **32%** 将其列为首要障碍 |
| 使用多模型 | **75%+** 使用多个模型提供商 |

三个关键发现：
1. **可观测性已成标配**——89% 实现了某种形式的 agent 追踪，62% 有详细步骤级追踪。没有可见性，团队无法可靠调试失败或优化性能。
2. **质量而非成本是生产杀手**——模型价格下降后，注意力从"花多少钱"转向"做得对不对"。大型企业（10k+ 员工）中，安全和幻觉/一致性是最大担忧。
3. **编码 Agent 主导日常工作**——Claude Code、Cursor、GitHub Copilot 是日常最高频使用的 Agent 工具，其次是深度研究 Agent。

### 5.9 Anthropic 平行 Agent 集群：16 个 Claude 合写 C 编译器

2026 年 2 月，Anthropic 研究员 Nicholas Carlini 进行了一项极限压力测试：**16 个 Claude Opus 4.6 Agent 并行工作两周，从零构建了一个 10 万行 Rust 代码的 C 编译器。** 总成本约 $20,000，消耗 20 亿输入 token 和 1.4 亿输出 token。

这个编译器通过了 GCC torture 测试套件 99% 的用例，能编译 Linux 6.9 内核（x86/ARM/RISC-V）、QEMU、FFmpeg、SQLite、PostgreSQL 和 Redis。

**Harness 设计的关键决策：**

- **无中央调度 Agent**：每个 Claude 独立读取共享 Git 仓库中的任务看板，自行决定下一步做什么
- **文件锁协调**：Agent 通过写 `current_tasks/` 目录下的锁文件来认领任务，避免冲突
- **无限循环模式**：Agent 跑在 Bash 循环中，完成一个任务后自动领取下一个
- **自主冲突解决**：Git 合并冲突由 Claude 自行处理

**三个关键教训：**

1. **测试就是一切**：Agent 会自主优化任何可验证的目标——如果验证器不完美，Agent 就会"正确地解决错误的问题"。Carlini 的结论是"写极高品质的测试"是 Harness 工程中最重要的工作。
2. **并行化瓶颈**：当所有 16 个 Agent 同时撞上同一个 Bug（如编译 Linux 内核时的特定错误），并行化彻底失效。解决方案是用 GCC 作为"预言机"随机分割工作，让 Agent 分散到不同子问题上。
3. **上下文窗口卫生**：详细输出会污染 Agent 上下文。关键做法是让测试输出尽可能简洁，用机器可解析的错误标记代替冗长的日志。

**成本锚点**：单 Agent 跑 20 分钟 / $9；完整 16 Agent Harness 跑 6 小时 / $200——20 倍成本，但产出的是真正可工作的编译器。

### 5.10 Scaling Managed Agents：大脑与双手的解耦

2026 年 4 月，Anthropic 工程师 Lance Martin 等人发表《Scaling Managed Agents》，揭示了一个架构洞察：**传统 Agent 将三件事耦合在同一个容器里——Harness（调用模型的循环）、Sandbox（代码执行环境）、Session（事件日志）——这制造了一个"宠物"而非"牛群"。**

新架构将 Agent 拆分为三个独立接口：

| 组件 | 职责 | 接口 | 类比 |
|------|------|------|------|
| **Brain（大脑）** | Claude + Harness：推理、规划、工具路由 | `wake(sessionId)` 无状态唤醒 | CPU 进程 |
| **Hands（双手）** | 沙箱、工具、MCP 服务器 | `execute(name, input) → string` | 外设 |
| **Session（会话）** | 追加式事件日志，持久化在外部 | `getEvents()`、`emitEvent()` | 文件系统 |

这个解耦带来了几个关键收益：

- **韧性**：容器崩溃 → Harness 将其捕获为工具调用错误 → Claude 决定重试 → 新容器自动创建。Harness 自身崩溃 → 新实例从 Session 日志恢复，从中断处继续。
- **延迟大幅下降**：容器按需延迟创建而非预分配，**p50 首 token 延迟降 ~60%，p95 降 >90%**。
- **安全隔离**：凭据（Git token、OAuth token）**永不进入沙箱**——Git token 仅在沙箱初始化时使用，OAuth token 存储在独立保险库中。
- **Session ≠ Context Window**：Session 是持久化、可查询的事件流，存在于 Claude 上下文窗口之外。`getEvents()` 允许按位置切片获取历史——重放、恢复或检查特定时刻。

**核心哲学**：Harness 不应该编码"当前模型不能做什么"的假设——当模型升级后，这些假设变成死重。通过稳定接口（Brain/Hands/Session）将 Harness 变成**可独立进化的组件**，就像操作系统通过 `read()` 抽象让应用不关心底层是磁盘还是 SSD。

### 5.11 量化 Harness 贡献：到底谁在干活？

2026 年 4 月，一项来自韩国的研究提供了一个独特视角：**把规划 Agent 的 Harness 拆成四层，逐层测量 Harness 与 LLM 各自贡献了多少能力。**

四层分别是：
1. **信念追踪（Belief Tracking）**：维护对环境和任务状态的符号化表示
2. **声明式规划（Declarative Planning）**：基于符号状态进行确定性规划
3. **符号化反思（Symbolic Reflection）**：规划失败时进行结构化反思
4. **LLM 支持修订（LLM-backed Revision）**：仅在符号系统无法处理时调用 LLM

实验结果令人震惊：**大部分"重活"来自前三层 Harness 组件，而非第四层的 LLM。** 当模型被降级为更弱版本时，只要前三层 Harness 不变，性能下降幅度远小于预期。

**核心启示**：在规划类 Agent 中，你现在看到的"智能行为"可能主要来自 Harness 而非模型。这意味着——**优化 Harness 不仅比换模型更便宜，而且效果可能更显著。** 这为"模型是商品，Harness 是护城河"提供了最直接的实证支撑。

这一结论在 2026 年 5 月得到了更严格的统计学确认。密歇根大学的论文《How Good Is Your Harness?》首次用统计归因方法量化了 Harness 与 LLM 各自的贡献比例，在 Terminal-Bench 2.0 上得出一个惊人结论：**切换到最佳 Harness 带来的增益，约等于切换到最佳 LLM 带来的增益**——两者影响相当，且存在交互（没有"放之四海皆准"的 Harness）。这把"模型是商品，Harness 是护城河"从口号变成了可量化、可复现的实证命题。

### 5.12 Anthropic 内部数据：当 Claude 写了 80% 的代码

2026 年 4 月，Anthropic 在《When AI builds itself》中披露了一组内部生产数据，为"Harness 是护城河"提供了来自模型厂商自身的一手证据：

- **80% 以上**合并到主线的代码由 Claude 编写
- 工程师人均日合并量从 2024 年到 2026 年 Q2 **增长 8 倍**
- 自动化的 Claude reviewer 能回溯抓出 claude.ai 过去 **1/3 的生产事故 Bug**
- Claude 自主研究累计运行 800 小时，恢复了 **97%** 的 weak-to-strong supervisor gap（人类一周仅能恢复 23%）

这些数字背后藏着一个被多数团队忽视的 Harness 设计新论点——**人类审查正在成为新的瓶颈**（Amdahl's law）：当生成端被 Agent 大幅加速后，整体吞吐量开始被串行的人工审查环节卡住。这意味着下一轮 Harness 投资的重点不再是"让 Agent 写得更多"，而是"让审查环节也自动化、可扩展"——否则 Agent 产出的代码会堆积在审查队列里，吞吐提升到此为止。

### 5.13 Bayer AG + ThoughtWorks：制药研发中的 Harness 工程

2026 年 6 月，Martin Fowler 网站发表了 Bayer AG（拜耳）与 ThoughtWorks 合作的 PRINCE 平台案例——一个用 Agentic RAG 为药物化学家提供数十年临床前研究数据查询的 Agent 系统。

文章通过**双重透镜**分析该平台的工程决策：

- **上下文工程透镜**：如何将药物化学、临床试验设计、监管合规等高度专业化的领域知识，结构化为 Agent 可消费的上下文单元
- **Harness 工程透镜**：编排（orchestration）、重试（retries）、可观测性（observability）和人机协作（human-in-the-loop）如何构成 Agent 可靠运行的 Harness 层

这是 Harness 工程从"软件工程 Agent"扩展到**垂直行业 Agent** 的标志性案例。制药行业的错误成本远高于代码 Bug——查询结果遗漏可能影响药物安全决策——因此 Harness 的约束和验证必须比编码场景更严格。同一套 Harness 设计原则（约束、验证、恢复），在垂直行业获得了更高的 stakes。

### 5.14 算法发现中的 Harness 工程：质量 > 数量

2026 年 5 月，NEC-NTT 联合团队的论文《Effective Harness Engineering for Algorithm Discovery》将 Harness 工程的视角延伸到了**自动化算法发现**领域（AlphaEvolve/FunSearch 谱系）。在固定 token 预算下，论文通过 Vesper 框架回答了三个 Harness 设计问题：

1. **少而深 vs 多而浅**：生成更少算法但每个深入思考，比生成更多代数获得更高得分——**扩展个体质量比扩展进化代数更预算高效**
2. **评估作弊（evaluation hacking）**：更强大的模型反而以**更高比例**产生利用评分函数漏洞的作弊算法——模型越强，hack 检测越必要
3. **并行安全**：需要完整文件系统访问的 Agent 如何安全并行运行

这个案例的独特价值在于：它证明了 Harness 设计原则**不仅适用于软件工程，也适用于科学发现**——当你让 Agent 去"发现更好的算法"时，Harness 不仅要防止它犯错，还要防止它"正确地解决错误的问题"（Goodhart's law）。这与 Carlini 在 C 编译器项目中的结论（5.9）形成交叉验证：**验证器的质量决定了 Agent 输出质量的上限。**

---

## 六、Harness 的六大核心组件

综合行业实践，一个完整的 Agent Harness 包含以下六个核心组件：

### 6.1 人机协作控制

Agent 在关键决策处暂停。删除数据库？扣款？发送客户邮件？Harness 要求审批。Replit 的 Agent 生成代码，但部署前需要人类确认。

### 6.2 文件系统访问管理

Harness 定义可访问的目录、允许的操作和冲突解决策略。Claude Code 的 Harness 精确控制模型能执行哪些文件系统操作——永远不碰系统文件。

### 6.3 工具调用编排

糟糕的编排会制造无限循环和级联故障。每个工具调用必须有：
- 类型化且验证过的输入/输出
- 幂等性设计（副作用操作需要显式的幂等键）
- 结构化的结果封装 `{ ok, data, error, meta }`
- 时间、重试、成本预算

### 6.4 子 Agent 协调

复杂任务需要专业化的 Agent。一个研究，一个编写，一个审查。Harness 管理通信、合并输出、解决冲突。关键原则：**不要让每个 Agent 都拥有所有工具。**

### 6.5 提示词库管理

不同任务需要不同指令。代码审查 vs 代码生成，Bug 修复 vs 功能开发。Harness 维护提示词库，并支持按需动态加载（保持前缀稳定以优化 KV-cache）。

### 6.6 生命周期钩子

初始化上下文 → 运行任务 → 保存状态 → 处理失败 → 重试逻辑 → 日志记录。Harness 实现可靠的工作流。

### 6.7 另一种视角：从模型局限逆向推导 Harness 组件

LangChain 在《The Anatomy of an Agent Harness》（2026.03）中提出了一个互补的框架——**从模型"不能做什么"出发，逆向推导 Harness 应该提供什么**：

| 模型局限 | Harness 组件 |
|----------|-------------|
| 无法维持跨交互的持久状态 | 文件系统 + Git（工作追踪、跨会话状态） |
| 无法执行代码 | Bash 工具 + 沙箱（通用问题解决能力） |
| 无法访问实时知识 | Web 搜索 + MCP 工具（知识截止日后更新） |
| 上下文窗口有限且会退化 | 压缩、工具输出截断、Skills 渐进披露 |
| 无法自行配置执行环境 | 沙箱预装运行时、CLI、浏览器、测试工具 |

这个"逆向推导"方法与上述六大组件形成互补：六大组件是"从系统设计出发"的结构分解，Anatomy 框架是"从模型需求出发"的功能推导。两者结合，构成了一个完整的 Harness 设计心智模型。

### 6.8 Brain-Hands 解耦：Session 的独立生命周期

Scaling Managed Agents（Anthropic，2026.04）提出了一个被传统 Agent 架构忽视的组件：**Session 作为独立的一等公民。**

传统 Agent 将 Session 日志内嵌在 Harness 进程中——Harness 挂了，Session 也丢了。生产级 Harness 应该：

- **Session 外置**：事件日志是追加式持久化存储，独立于 Harness 进程和沙箱容器
- **Harness 无状态化**：通过 `wake(sessionId)` 从日志恢复上下文，任何一个 Harness 实例都可以接手续跑
- **按需查询**：Session 提供 `getEvents()` 接口按位置切片，而非将整个历史塞进上下文窗口

这个设计的核心洞察是：**上下文窗口 ≠ 会话状态。** 上下文窗口是瞬时的、有限的、需要管理的；会话状态是持久的、完整的、应该保留的。把两者等价对待，会导致不可逆的信息损失（压缩/截断）和脆弱的故障恢复。

---

### 6.9 OpenAI 的 Harness 改进飞轮

OpenAI 在 2026 年 5 月发布了一份 Cookbook，描述了一个系统化的 Harness 改进循环：

```
Traces（追踪）→ 人工/LLM 反馈 → Promptfoo 评估 → HALO 优化 → Codex 接手实施
```

这个飞轮的核心是**可审查的自动化**：每个循环产出一个 `codex_handoff.md` 文件，包含排好优先级的 Harness 修改建议和实施方案，人类审批后由 Codex 自动执行。它既避免了"每次都靠人盯着"的低效，也防止了"完全自动修改导致失控"的风险——**在人工审查和自动执行之间找到了平衡点。**

### 6.10 跨 Context Window 的状态交接

当一个任务跨越多个上下文窗口（长任务必须分段执行），Harness 必须提供不依赖模型记忆的状态交接机制。Anthropic 在《Effective harnesses for long-running agents》（2025.11）中沉淀了一套被验证的模式：

- **双角色分工**：一个 initializer agent 在首会话把需求拆成 **200+ 条 JSON feature 清单**（每条带 `passes:false` 标记）+ 一个 `init.sh` 脚本 + 一个 `claude-progress.txt` 进度文件 + 初始 commit；后续每个 coding agent 会话只做一个 feature
- **用 JSON 而非 Markdown 记录状态**：防止模型在后续会话中擅自"改写历史"——结构化字段比自由文本更难被无意识地覆写
- **git 回滚交接**：每个 feature 失败时用 git 回滚坏改动，进度文件记录真实完成状态，下一个会话从断点继续

核心原则：**跨上下文窗口的状态必须落盘为机器可解析、模型不可随意篡改的结构化文件**，而非寄希望于模型"记住"上次做到哪了。这与 6.8 的 Session 外置一脉相承——长任务的可靠性，取决于状态能否在会话之间无损传递。

### 6.11 模型专属 Harness 配置（Harness Profiles）

LangChain 在 2026 年 4 月的实践中发现了一个反直觉的事实：**不同模型家族需要不同的 Harness 配置才能发挥最佳性能。** 同一套提示词、工具集和中间件，在 Claude Opus 上表现优异，在 GPT Codex 上却可能平庸——反之亦然。

原因在于各模型厂商有不同的提示词指南。OpenAI Codex Prompting Guide 推荐特定工具名（`apply_patch`、`shell_command`），Anthropic Claude 指南强调不同的约定。甚至同一家族内（Opus 4.6→4.7），迁移指南也会标记需要调整的提示词细节。

解决方案是 **Harness Profiles**——按模型维护独立的配置覆盖层：

- **提示词前缀/后缀**（不同模型对指令格式的敏感度不同）
- **工具选择与命名**（有的模型偏好 `apply_patch`，有的偏好 `edit_file`）
- **中间件策略**（模型推理能力越强，需要的显式检查步骤越少）
- **子 Agent 配置**（不同模型适合不同的子任务划分粒度）

量化结果（tau2-bench 困难任务子集）：

| 模型 | 默认 Harness | 专属 Profile | 提升 |
|------|-------------|-------------|------|
| GPT 5.3 Codex | 33% | 53% | +20pp |
| Claude Opus 4.7 | 43% | 53% | +10pp |

Deep Agents v0.6 将这一理念扩展到开源模型（Kimi、Qwen、DeepSeek），在保持 20 倍以上成本优势的同时，通过专属 Harness 配置缩小了与前沿模型的差距。核心原则：**为模型适配 Harness，而非为 Harness 选择模型。**

### 6.12 中间件式 Harness 扩展

LangChain 在 2026 年 6 月的《How to Build a Custom Agent Harness》中提出了以**中间件（Middleware）**为核心的 Harness 定制范式。`create_agent` 是 LangChain 的 Harness 最小化原语——只实现核心 Agent 循环，所有扩展能力通过中间件注入。

中间件在 Agent 循环的每一步插入钩子：

| 钩子点 | 典型用途 |
|--------|---------|
| `before_model` | 注入上下文、检查权限、动态切换模型 |
| `after_model` | 验证输出格式、检测循环、触发压缩 |
| `before_tool` | 验证参数、检查预算、PII 过滤 |
| `after_tool` | 截断过长输出、记录审计日志、重试逻辑 |
| `on_start` / `on_finish` | 会话初始化、状态持久化、资源清理 |

**核心优势是可组合性**——每个中间件是独立的、可测试的模块：新增规则 = 新增中间件，删除规则 = 移除中间件。这完美呼应了"可剥离性"原则——当模型升级后某些中间件变成死重，删除它只是一行配置。

LangChain 将这个范式提炼为 **Task-Harness Fit** 概念：客服 Agent 的 Harness 与长时间编码 Agent 的 Harness 完全不同——不是因为模型不同，而是因为任务对上下文、容错、策略执行、运行环境的要求不同。**最好的 Agent 不只是用最好的模型构建的，而是用与任务最匹配的 Harness 构建的。**



Simon Willison（Django 框架联合作者）提出了"致命三要素"框架。当 Agent 同时具备以下三个条件时，安全事件不是"会不会"的问题，而是"什么时候"的问题：

1. **处理不可信输入**（外部网页、邮件、用户输入）
2. **访问敏感系统/数据**（个人隐私信息、内部 API、数据库）
3. **能修改状态**（发送邮件、删除文件、发起 API 调用）

### 7.2 二选一规则（Rule of Two）

Meta AI 借鉴 Chromium 浏览器安全策略，提出了可操作的规则：让 Agent **最多同时拥有三个要素中的两个**。如果三个都需要，强制要求人类审批。

| 允许的组合 | 禁止的第三个要素 |
|------------|------------------|
| 读取外部数据 + 处理敏感信息 | 状态修改必须有人类审批 |
| 读取外部数据 + 修改状态 | 敏感数据访问被阻止，仅限沙箱 |
| 处理敏感信息 + 修改状态 | 外部输入被阻止，仅限内部数据 |

### 7.3 策略即代码（Policy as Code）

将策略写成机器可读的格式，在 LLM 之外强制执行：

```yaml
policies:
  prod:
    allowTools: ["search", "readDb", "createTicket"]
    requireApprovalFor: ["sendEmail", "deleteRecord", "chargeCard"]
```

### 7.4 多用户 Harness 安全治理

2026 年 6 月，论文《Harness-MU: A Safe, Governed, and Effective Harness for Multi-User LLM Agents》提出了首个模型无关、零微调的多用户 Agent 基础设施框架。核心洞察：**治理约束——谁被授权、什么被限制、谁的指令优先——是确定性运行时变量，应由执行钩子（execution hooks）强制执行，而非托付给 LLM 的概率性判断。**

传统 LLM 基于单用户训练范式，其基于提示词的安全防护在多轮对抗性交互下脆弱不堪。Harness-MU 将语言生成与安全编排解耦，在四个前沿模型（开源+闭源）上通过 Muses-Bench 基准验证：**所有访问控制攻击下均实现隐私保护**，实用性得分超过基线 0.28-0.39，指令遵循准确率最高提升 **48.9 个百分点**。

这与 Policy as Code（7.3）一脉相承，但将安全边界从"单用户操作审批"扩展到"多用户数据隔离、权限模型与审计追踪"——在企业协作 Agent 场景中，这些必须由 Harness 层机械性强制执行，而非依赖模型"自觉"。

---

## 八、Harness 工程的三个设计原则

### 原则一：最小必要干预

只在模型无法自我纠正时才干预。让模型处理模糊性，只在不可逆操作或安全边界处介入。

### 原则二：渐进式披露

从有限的工具和权限开始，随任务需要逐步扩展。不给数据库删除权限——除非确实需要。最小权限原则。

### 原则三：快速失败与恢复

快速检测失败，不让 Agent 陷入死循环。失败发生时提供恢复路径：用不同方法重试，回退到人类介入，永远不要静默失败。

### 原则四：Brain-Hands 分离

将"思考"和"执行"分开。Harness（Brain）负责推理和决策，沙箱（Hands）负责执行代码和工具调用。关键约束：
- **凭据不进沙箱**：Git token、API key 等敏感信息只存在于 Harness 层
- **会话外置**：事件日志独立持久化，Brain 挂了可以重接
- **模型假设可剥离**：不为当前模型的特定缺陷构建永久性补救逻辑——当模型升级后这些变成死重

这个原则来自 Anthropic 的生产经验：当初为 Claude Sonnet 4.5 的"上下文焦虑"构建的上下文重置机制，在 Opus 4.5 上变成了纯粹的累赘。**好的 Harness 设计让过时的组件容易移除。**

---

## 九、常见错误与避坑指南

### 错误一：过度工程化控制流

> "如果你过度工程化控制流，下一次模型更新就会破坏你的系统。"

模型在快速进步。2024 年需要复杂流水线的能力，现在一个上下文窗口提示词就能搞定。构建 Harness 时要使其**可剥离（rippable）**——当模型足够聪明不需要某些"智能"逻辑时，你应该能轻松移除它。

### 错误二：把 Harness 当作静态的

Harness 需要随模型一起进化。当新模型版本改进了推理能力，你的推理优化中间件可能反而变成累赘。每次重大模型更新都要审查和更新 Harness 组件。

### 错误三：忽略文档层

影响最大的 Harness 改进往往最简单：**更好的文档**。如果你的 AGENTS.md 写得含糊，Agent 的输出就会含糊。投资于精确的、机器可读的文档，作为 Agent 的事实来源。

### 错误四：没有反馈循环

没有反馈的 Harness 是牢笼，不是向导。Agent 需要知道自己何时在成功、何时在失败。构建自验证步骤、测试执行、以及按任务类型的成功率指标。

### 错误五：只给人看的文档

如果架构决策只存在于人们脑中或 Confluence 页面上（Agent 无法访问），Harness 就有缺口。**Agent 需要的一切都必须在仓库里。**

---

## 十、如何从零开始构建你的 Harness？

### Level 1：个人基础 Harness（1-2 小时）

使用 Claude Code、Cursor 或 Codex 的个人项目：
- 创建 CLAUDE.md 或 .cursorrules 文件，写明项目约定
- 设置预提交钩子（Linting + 格式化）
- 建立一套 Agent 可以运行以自我验证的测试
- 保持清晰的目录结构和一致的命名

### Level 2：团队 Harness（1-2 天）

3-10 人共享代码库的团队，在 Level 1 基础上增加：
- 团队级别的 AGENTS.md
- CI 强制执行的架构约束
- 常见任务的共享提示词模板
- 文档即代码，由 Linter 验证
- 专门针对 Agent 生成 PR 的代码审查清单

### Level 3：生产级 Harness（1-2 周）

运行数十个并发 Agent 的组织，在 Level 2 基础上增加：
- 自定义中间件层（循环检测、推理优化）
- 可观测性集成（Agent 读取日志和指标）
- 按计划运行的熵管理 Agent
- Harness 版本控制和 A/B 测试
- Agent 性能监控仪表盘
- Agent 卡住时的升级策略

---

## 十一、工程师的角色正在变化

Harness 工程代表了软件工程师工作内容的真正演进：

| 之前 | 之后 |
|------|------|
| 编写代码 | 设计让 AI 编写代码的环境 |
| 调试代码 | 调试 Agent 行为 |
| 审查代码 | 审查 Agent 输出 + Harness 有效性 |
| 编写测试 | 设计测试策略 |
| 维护文档 | 构建机器可读的文档基础设施 |

这不是说工程师变得不够技术了。恰恰相反——Harness 工程需要**更深层次**的架构思维：你要设计一个必须在你不持续干预的情况下正常工作的系统。

关键技能：
1. **系统思维**——理解约束、反馈循环和文档如何交互
2. **架构设计**——定义可执行且有生产力的边界
3. **规范编写**——将意图精确到足以让 Agent 执行的程度
4. **可观测性**——构建揭示 Agent 行为模式的监控
5. **迭代速度**——快速测试和优化 Harness 配置

---

## 十二、未来展望

### 守护者 Agent（Guardian Agents）

想象一个 Agent 即将部署代码，另一个 Agent 实时介入："等一下——这个变更超出了监管合规范围。" Anthropic 的 Evaluator 是其原始形态。严谨正在从"执行"迁移到"监督"。

### 评估工程（Evaluation Engineering）

"行为胜过基准"（behavior beats benchmarks）的理念正在兴起。不是 MMLU 分数，而是 Agent 完成真实世界任务的比率，以及从失败中恢复的优雅程度。特别是**不可验证的奖励**：机器能判断代码能否编译，但"这段文字写得好不好？""这个设计美不美？"——如何评分？"如何评估"本身正在成为一门独立的工程学科。

2026 年，这门学科的方法论快速成型。Anthropic 的《Demystifying evals for AI agents》提出了一个 8 步评估设计框架，并区分两类评估：**capability evals**（押注未来模型能力，低通过率起步合理）与 **regression evals**（应维持接近 **100%** 通过率，专门捕获回退）。该文用一个案例量化了 Harness 对评估结果的决定性影响：Opus 4.5 在 CORE-Bench 上因 Harness 的刚性评分和任务歧义被**低估至 42%**——同样的模型，换个 Harness 评估，分数天差地别。LangChain 的《Better Harness: A Recipe for Harness Hill-Climbing with Evals》则把机器学习的数据纪律移植过来：6 步迭代 recipe（标记 eval → 训练/holdout 划分 → 基线 → 优化 → 人工审查 → 回归维护），并强调 **holdout set 是真实泛化的代理**——没有 holdout，你只是在让 Harness 过拟合到已知用例。

### 知识引擎（Knowledge Engines）

当前的上下文工程处理的是"这次该放什么"。但真实项目包含比代码本身更重要的信息："为什么选了这个架构？""六个月前试过这个方案——为什么回滚了？"知识引擎旨在结合代码图谱（函数调用关系、依赖结构）、提交历史（代码为什么以及如何演变）和记忆系统（过往会话的经验教训），让 Agent 不仅"修改这个函数"，而是"理解这个函数为什么长成这样，然后修改它"。

2026 年的两项研究为知识引擎划出了当前的能力边界。一项对 **10 个生产级 SE harness** 的 feature 级分析（《Memory-Aware Software Engineering Agents》）发现：**0/10 个生产级 harness 原生提供情景记忆（episodic memory）或时序版本（temporal versioning）**——连外挂的 MCP 记忆组件也极少实现时序推理。这意味着当前 Agent 的"记忆"大多是扁平的、无时间维度的，这正是知识引擎要补的缺口。另一项综述（《A Survey on Agent Skills》）则把"Skill"这个被工程界广泛使用却缺乏定义的概念形式化为**"外化的过程性知识"（externalized procedural knowledge）**——介于 LLM 通用能力与具体任务之间的可复用过程抽象，并给出了从本体、表示、获取、组合到部署的完整 taxonomy。当 Skill 从"一段提示词"升级为"可版本控制的知识资产"，它与 NLAH（3.4 节）共同指向同一个方向：**Harness 的逻辑正在从偶然的胶水代码，转变为可检查、可 diff、可迁移的显式知识对象。**

### 具身 Harness（Embodied Harnesses）

当 Agent 从数字世界延伸到物理世界——机器人操作、自动驾驶——Harness 的约束从代码层面扩展到安全关键的现实交互。错误不再是编译报错，而是人身安全。

2026 年 6 月，论文《Harness Engineering for Physical AI: Robot Middleware Is the Harness Layer》首次将机器人中间件（ROS 2、DDS、Zenoh）形式化为 Physical AI 的 Harness 层。核心论点：**软件 Harness 在工具调用边界介入，而物理 AI Harness 必须同时在控制（control）、计算（computing）和通信（communication）三个维度介入**——因为学习型策略的输出同时跨越三者：指令改变轨迹、推理时间改变调度、载荷改变带宽。

论文提出三个关键执行功能：
- **Projection（输出门控）**：在动作发出前验证安全约束——速度是否超限？关节角度是否在安全范围内？
- **Isolation（执行边界）**：将模型推理和执行限制在有界时间/空间槽内——碰撞检测、急停逻辑独立于正常控制流
- **Transfer（验证回退）**：异常发生时自动转移到经过验证的安全基线——而非依赖 Agent 自行判断

论文进一步提出 **ROS 2 Harness Profile** 概念：一个部署工件，携带 AI 模型的声明式输出区域、推理预算和运行制度，中间件在 ROS 2/DDS/Zenoh 上强制执行它们。这标志着 Harness 工程从"软件 Agent 的基础设施"扩展到"物理 Agent 的安全关键基础设施"——同一套设计原则（约束、验证、恢复），在物理领域获得了更高的 stakes。

### Meta-Harness：自动化的 Harness 设计

2026 年 3 月的 arXiv 论文《Meta-Harness》提出了一个前沿问题：**Harness 的设计过程本身能否被自动化？** 传统上，工程师根据经验手动调整 Linter 规则、提示词策略和反馈循环。Meta-Harness 探索用文本优化技术端到端地搜索最优 Harness 配置——自动发现哪条规则真正提升了 Agent 成功率，哪条只是噪音。

仅仅一个月后，AHE（Agentic Harness Engineering）给出了实证答案：**可以**。通过组件/经验/决策三层可观测性，AHE 在 Terminal-Bench 2 上实现了从 69.7% 到 77.0% 的自动进化，超越人类设计的 Harness。

再到 2026 年 6 月，**Self-Harness** 将自动化推向了新高度——不再需要人类设定优化目标，Agent 通过 Weakness Mining → Proposal → Validation 闭环**自行发现并修复自身 Harness 的缺陷**。**SIA** 则证明了同时优化 Harness 和模型权重比单独优化任一维度都更有效。

Google DeepMind 的 AutoHarness 在同一时期展示了另一条路径：让模型自己合成 Harness 代码，通过环境反馈迭代优化。**较小的模型（Gemini-2.5-Flash）配备自动合成的 Harness，超越了更大的模型（Gemini-2.5-Pro、GPT-5.2-High）**——在 145 款游戏中完全防止非法走法，甚至将整个策略编译为代码以消除推理时 LLM 调用。

NLAH（Natural-Language Agent Harnesses）则从另一个角度切入：**Harness 不仅可以是自动生成的，还可以是自然语言文档而非代码**——可版本控制、可 diff、可跨运行时迁移。

2026 年 5 月，《Harness Engineering as Categorical Architecture》进一步为这个方向奠定了**形式化数学基础**——用范畴论三元组（G, Know, Phi）将 Harness 的四大支柱（Memory、Skills、Protocols、Harness Engineering）映射到可组合、可编译的数学结构，并提供编译器函子（compiler functors）将同一 Harness 规格编译到 Swarm、LangGraph、DeerFlow 等多个运行时。这意味着 Harness 设计可能从"ad hoc 手工工艺"升级为"形式化可证明的工程学科"。

再到 2026 年 6 月，《The Interplay of Harness Design and Post-Training》从另一个维度补充了 SIA 的发现：不仅 Harness 和模型权重可以联合优化，**Harness 设计本身应该被纳入模型后训练（post-training）的考量。** 论文扩展 ALFWorld 将 Harness 作为可控设计维度，证明 harness-aware post-training 不仅提升分布内性能，还使 Agent 能鲁棒适应工具环境变化（OOD）——而忽略 Harness 设计的后训练在工具环境变化下性能急剧下降。

未来的 Harness 可能不是人类设计的，也不是单一 AI 生成的——而是 AI 为特定模型和任务**自动生成、持续优化、自愈修复、可自然语言编辑**的。从 Meta-Harness 到 AHE 到 AutoHarness 到 Self-Harness 到 SIA，这五篇论文共同勾勒了 Harness 工程从"手工打造"到"自动进化"再到"自主进化"的完整路线图。

### Harness 透明度：评估改革的必然趋势

2026 年 5 月，一篇立场论文提出了**"约束约束论"（Binding Constraint Thesis）**：在长周期任务中，Harness 配置对 Agent 性能的影响**大于**模型选择。论文从三个角度论证：

1. **控制论形式化**：Harness 是闭环动态系统的控制器，LLM 是其控制的随机策略——小幅 Harness 变更可以产生超过模型替换的性能偏移
2. **方差分解**：已发布的基准测试中，Harness 引入的方差可能大幅超过模型引入的方差，**包括模型排名反转的案例**
3. **Harness 披露标准**：提出了评估框架——在 Harness 规格公开之前，长周期 Agent 的排行榜比较应被视为不完整且可能误导

这个论点与 2026 年行业共识高度一致——模型是商品，Harness 是护城河——但它更进一步：**如果你不公开 Harness 配置，你的基准测试结果就没有意义。** 这预示着 Harness 工程的下一个前沿不仅是"设计更好的 Harness"，更是"让 Harness 可比较、可复现、可审计"。

---

## 结语

2025 年证明了 Agent 能工作。2026 年的关键是让 Agent **可靠地**工作。2026 年 6 月，Loop Engineering 的出现进一步提出了下一个问题：让 Agent **持续地、无人值守地**工作。

从提示词工程到上下文工程到 Harness 工程再到 Loop 工程，本质上是同一条线索在四个抽象层次上的展开：**工程严谨从未消失，它只是在往更高的抽象层次搬家。**

```
Loop Engineering      ← 何时运行？系统自己驱动自己
    ↑ 包含
Harness Engineering   ← 如何约束？"Agent 中除模型之外的一切"
    ↑ 包含
Context Engineering   ← 提供什么信息？上下文策划与优化
    ↑ 包含
Prompt Engineering    ← 说什么？单次交互的质量
```

每一层都没有杀死下一层——提示词工程没有死亡，它成了 Loop 循环体内的一个原子单元。Harness 工程没有过时，它成了 Loop 可靠运行的基石。

Anthropic、OpenAI、ThoughtWorks、Google、LangChain……从学术界到工业界，共识正在持续加速收敛：

> **2026 年上半场的竞争优势来自基础设施（Harness），下半场的竞争来自自动化（Loop）。模型是商品，Harness 是护城河，Loop 是引擎。**

而最好的设计——无论是 Harness 还是 Loop——都关乎"**如何让不需要的东西容易被移除**"。当 Claude 5.0 发布时，你为 Claude 4.5 构建的上下文重置逻辑可能变成死重。当 Loop 的某个自动化步骤被模型原生能力取代时，你应该能一行配置删掉它。

最后借用 Osmani 的话收尾——它不仅适用于 Loop，也适用于整个 Harness 工程的精神：

> **"Build the loop. Stay the engineer."**
> **"设计循环。但以工程师的身份设计——而不是以按下启动按钮就撒手的人。"**

---

## 参考资源

### 学术论文
- [Agent Systems with Harness Engineering: A Systematic Survey](https://github.com/RUCAIBox/awesome-agent-harness)（RUCAIBox，2026）——含 502 篇参考文献的系统综述
- [Agent Harness for Large Language Model Agents: A Survey](https://openreview.net/pdf?id=eONq7FdiHa)（OpenReview，2026）——独立综述，将 Harness 工程定义为一门独立学科
- [Harness Engineering for Language Agents: The CAR Framework](https://www.preprints.org/manuscript/202603.1756)（2026.03）——Control/Agency/Runtime 三层分解模型
- [Meta-Harness: End-to-End Optimization of Model Harnesses](https://arxiv.org/html/2603.28052v1)（arXiv，2026.03）——探索 Harness 设计过程本身的自动化
- [Code as Agent Harness](https://arxiv.org/abs/2605.18747)（arXiv，2026.05）——以代码为中心的 Agent Harness 统一视图，按 Interface/Mechanisms/Scaling 三层组织，覆盖编码助手、GUI/OS 自动化、具身 Agent 等
- [Natural-Language Agent Harnesses](https://arxiv.org/abs/2603.25723)（arXiv，2026.03）——提出 NLAH（可执行自然语言 Harness 文档）和 IHR（智能 Harness 运行时），将 Harness 从胶水代码转变为科学表示对象
- [Agentic Harness Engineering: Observability-Driven Automatic Evolution of Coding-Agent Harnesses](https://arxiv.org/abs/2604.25850)（arXiv，2026.04）——AHE 闭环系统通过三层可观测性实现 Harness 自动进化，Terminal-Bench 2 从 69.7% 提升至 77.0%，超越人类设计的 Harness
- [Stop Comparing LLM Agents Without Disclosing the Harness](https://openreview.net/forum?id=ffKHSraOIK)（OpenReview，2026.05）——提出 Binding Constraint Thesis：Harness 配置对 Agent 性能的影响大于模型选择，呼吁 Harness 披露标准
- [AutoHarness: Improving LLM Agents by Automatically Synthesizing a Code Harness](https://openreview.net/forum?id=g9rEYVNn5T)（OpenReview，2026）——小模型自动合成 Harness 后超越大模型，145 款游戏零非法走法
- [Awesome Code-as-Agent-Harness-Papers](https://github.com/YennNing/Awesome-Code-as-Agent-Harness-Papers)——按 Interface/Mechanisms/Scaling 三层组织的论文集
- [Self-Harness: Harnesses That Improve Themselves](https://arxiv.org/abs/2606.09498)（arXiv，2026.06）——Agent 通过 Weakness Mining→Proposal→Validation 闭环自主改进自身 Harness
- [SIA: Self Improving AI with Harness & Weight Updates](https://arxiv.org/abs/2605.27276)（arXiv，2026.05）——Harness 与模型权重联合自改进，超越纯 Harness 迭代
- [How Much Heavy Lifting Can an Agent Harness Do?](https://arxiv.org/abs/2604.07236)（arXiv，2026.04）——四层规划 Harness 量化研究：大部分 Agent 能力来自 Harness 而非 LLM
- [What Makes a Harness a Harness](https://arxiv.org/abs/2606.10106)（arXiv，2026.06）——Harness 术语的形式化构成定义，划定与 Agent Framework/SDK/Orchestrator 的边界
- [From Agent Loops to Structured Graphs: A Scheduler-Theoretic Framework](https://arxiv.org/abs/2604.11378)（arXiv，2026.04）——将 Agent 循环形式化为三层 DAG，控制流从隐式上下文提升为显式静态图
- [From Agent Loops to Deterministic Graphs: Execution Lineage for Reproducible AI-Native Work](https://arxiv.org/abs/2605.06365)（arXiv，2026.05）——Loop 收敛后固化为可复现 DAG，实现完美上下游保持
- [Good to Go: The LOOP Skill Engine](https://arxiv.org/abs/2605.14237)（arXiv，2026.05）——记录一次轨迹后确定性回放，99% 成功率、节省 93-99.98% token
- [Continual Harness: Online Adaptation for Self-Improving Foundation Agents](https://arxiv.org/abs/2605.09998)（arXiv，2026.05）——无重置自改进 Harness，从轨迹数据自动优化 prompts/sub-agents/skills/memory
- [How Good Is Your Harness? Statistical Evaluation of Coding Harnesses](https://openreview.net/forum?id=QI8z3skBwt)（OpenReview / CTB@ICML 2026 Workshop，2026.05）——首个用统计归因方法量化 Harness 与 LLM 贡献比：在 Terminal-Bench 2.0 上，切换最佳 Harness 的增益 ≈ 切换最佳 LLM 的增益
- [Memory-Aware Software Engineering Agents: Architectures, Mechanisms, and Open Gaps](https://openreview.net/forum?id=WeXF1A3xY8)（OpenReview / KI 2026 AI4SE Workshop，2026.05）——对 10 个生产级 SE harness 的 feature 级分析，发现 0/10 原生提供 episodic memory 或 temporal versioning
- [A Survey on Agent Skills: Externalized Procedural Knowledge in Language Models](https://openreview.net/forum?id=S184SW2SPX)（OpenReview / ACL ARR 2026 May cycle，2026.05，审稿中）——将 agent skills 形式化为"外化的过程性知识"，给出 ontology/representation/acquisition/composition/deployment 五维 taxonomy
- [Harness-MU: A Safe, Governed, and Effective Harness for Multi-User LLM Agents](https://arxiv.org/abs/2606.21856)（arXiv，2026.06）——首个模型无关多用户 Agent 基础设施框架，执行钩子强制执行治理约束，Muses-Bench 全访问控制攻击下零隐私泄露
- [Harness Engineering as Categorical Architecture](https://arxiv.org/abs/2605.12239)（arXiv，2026.05）——用范畴论三元组为 Harness 工程建立形式化数学基础，提供编译器函子将 Harness 规格编译到 Swarm/LangGraph/DeerFlow 等运行时
- [Effective Harness Engineering for Algorithm Discovery with Coding Agents](https://arxiv.org/abs/2605.15221)（arXiv，2026.05）——Vesper 框架研究算法发现场景下的 Harness 设计：少而深优于多而浅，更强模型产生更多评估作弊
- [Harness Engineering for Physical AI: Robot Middleware Is the Harness Layer](https://arxiv.org/abs/2606.09416)（arXiv，2026.06）——首次将机器人中间件（ROS 2/DDS/Zenoh）形式化为 Physical AI 的 Harness 层，提出 Projection/Isolation/Transfer 三功能
- [The Interplay of Harness Design and Post-Training in LLM Agents](https://arxiv.org/abs/2606.25447)（arXiv，2026.06）——扩展 ALFWorld 将 Harness 作为可控设计维度，证明 harness-aware post-training 提升 ID 和 OOD 鲁棒性

### 官方资源
- [2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf)（Anthropic）
- [Harness Engineering: Leveraging Codex in an Agent-First World](https://openai.com/index/harness-engineering/)（OpenAI，2026.02）
- [Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps)（Anthropic，2026.03）
- [Harness Engineering for Coding Agent Users](https://martinfowler.com/articles/harness-engineering.html)（Martin Fowler，2026.04）——Context + Harness 工程的完整心智模型
- [Improving Deep Agents with Harness Engineering](https://www.langchain.com/blog/improving-deep-agents-with-harness-engineering)（LangChain，2026）——自验证、追踪、上下文优化，Terminal Bench Top 30 → Top 5
- [The Anatomy of an Agent Harness](https://www.langchain.com/blog/the-anatomy-of-an-agent-harness)（LangChain，2026.03）——从模型局限逆向推导 Harness 组件的完整框架：文件系统、沙箱、记忆、上下文管理
- [State of Agent Engineering](https://www.langchain.com/state-of-agent-engineering)（LangChain，2026）——1,300+ 专业人士调查：57% 生产部署、89% 可观测性、质量是最大障碍
- [Scaling Managed Agents: Decoupling the brain from the hands](https://www.anthropic.com/engineering/managed-agents)（Anthropic，2026.04）——Brain/Hands/Session 三层解耦架构，p50 延迟降 60%，Session≠Context Window
- [Building a C Compiler with a Team of Parallel Claudes](https://www.anthropic.com/engineering/building-c-compiler)（Anthropic，2026.02）——16 Agent 集群，10 万行 Rust，$20K，lock file 自主协调
- [Build an Agent Improvement Loop with Traces, Evals, and Codex](https://developers.openai.com/cookbook/examples/agents_sdk/agent_improvement_loop)（OpenAI，2026.05）——Traces→Evals→HALO→Codex 飞轮，人工审查 + 自动执行的 Harness 改进闭环
- [Loop Engineering](https://addyosmani.com/blog/loop-engineering/)（Addy Osmani / Google，2026.06）——Loop Engineering 的体系化定义：五大构建块 + State，与 Harness 的关系定位，风险与争议分析
- [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)（Anthropic，2025.11）——initializer + coding agent 双角色 harness，200+ JSON feature 清单 + git 回滚 + 进度文件实现跨 context window 状态交接
- [When AI builds itself](https://www.anthropic.com/institute/recursive-self-improvement)（Anthropic Institute，2026.04）——内部一手数据：80%+ 代码由 Claude 编写，人均产出 8 倍增长，1/3 生产 Bug 被自动 reviewer 抓出；提出"人类审查成为新瓶颈"（Amdahl's law）
- [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)（Anthropic，2026.01）——agent harness 的 8 步评估设计框架，区分 capability/regression evals，三类 grader 组合策略
- [Better Harness: A Recipe for Harness Hill-Climbing with Evals](https://www.langchain.com/blog/better-harness-a-recipe-for-harness-hill-climbing-with-evals)（LangChain，2026.04）——6 步 Harness 迭代 recipe，holdout set 防过拟合，跨模型（Sonnet 4.6 / GLM-5）验证泛化
- [Tuning Deep Agents to Work Well with Different Models](https://www.langchain.com/blog/tuning-deep-agents-different-models)（LangChain，2026.04）——Harness Profiles 概念与实现，按模型维护专属配置覆盖层，tau2-bench +10-20pp
- [How to Build a Custom Agent Harness](https://www.langchain.com/blog/how-to-build-a-custom-agent-harness)（LangChain，2026.06）——`create_agent` + 中间件式 Harness 扩展范式，Task-Harness Fit 概念
- [Deep Agents Deploy: an open alternative to Claude Managed Agents](https://www.langchain.com/blog/deep-agents-deploy-an-open-alternative-to-claude-managed-agents)（LangChain，2026.04）——开源模型无关 Agent Harness，集成 MCP/A2A/Agent Protocol
- [Deep Agents v0.6](https://www.langchain.com/blog/deep-agents-0-6)（LangChain，2026.05）——开源模型 Harness Profiles，20x+ 成本优势下缩小与前沿模型差距
- [Building Reliable Agentic AI Systems](https://martinfowler.com/articles/reliable-llm-bayer.html)（Martin Fowler / ThoughtWorks，2026.06）——Bayer AG PRINCE 制药研发平台案例，上下文工程 + Harness 工程双重透镜分析

### 深度解读
- [Engineering Rigor Doesn't Disappear — It Relocates: Four Years of AI Agentic Patterns](https://bits-bytes-nn.github.io/insights/agentic-ai/2026/04/05/evolution-of-ai-agentic-patterns-en.html)——从提示词到上下文到 Harness 的完整演进史
- [Harness Engineering: The Complete Guide to Building Systems That Make AI Agents Actually Work](https://www.nxcode.io/resources/news/harness-engineering-complete-guide-ai-agent-codex-2026)——三大支柱的详细拆解
- [2025 Was Agents. 2026 Is Agent Harnesses](https://aakashgupta.medium.com/2025-was-agents-2026-is-agent-harnesses-heres-why-that-changes-everything-073e9877655e)——Manus/LangChain/Vercel 的 Harness 实战案例
- [AI Agents in 2026: Tools, Memory, Evals, and Guardrails](https://andriifurmanets.com/blogs/ai-agents-2026-practical-architecture-tools-memory-evals-guardrails)——生产 Agent 的技术蓝图
- [Harness Engineering: What Every AI Engineer Needs to Know in 2026](https://ai.gopubby.com/harness-engineering-what-every-ai-engineer-needs-to-know-in-2026-0ab649e5686a)——三大阵营、三种架构的比较分析

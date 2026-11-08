# Harness 工程原则评估框架

> 基于权威来源的行业共识，用于评估 AI Agent 开发环境的成熟度。
> 模型是商品，Harness 是护城河。—— 2026 行业共识

**版本**: v1.25
**更新日期**: 2026-11-08
**用途**: 审计任意项目的 Agent Harness 成熟度，输出评分 + 改进建议

---

## 结构导航

> 本文件阅读顺序：**权威来源表**（证据底座，可跳过先看框架）→ **评估维度 D1-D9**（每维度含来源/原则/检查项/星级标准）→ **评分体系**（星级含义 + 成熟度等级 + 评分协议）→ **常见反模式 1-18** → **评估输出模板** → **更新日志**。
>
> 只想评估项目：直接跳 D1-D9 逐项打勾，再按评分协议汇总；只想了解反模式排查：跳反模式章节；跨文件找主题：见 README 文件导航。

## 权威来源

| 来源 | 机构 | 核心贡献 |
|------|------|----------|
| [Agent Systems with Harness Engineering](https://github.com/RUCAIBox/awesome-agent-harness) | RUCAIBox（人大） | 502 篇参考文献系统综述，确立 Harness 为一等公民 |
| [Harness Engineering: Leveraging Codex](https://openai.com/index/harness-engineering/) | OpenAI（2026.02） | 三大支柱框架，100 万行零手写代码实践 |
| [Harness Design for Long-Running Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) | Anthropic（2026.03） | 三 Agent 架构（Planner/Generator/Evaluator） |
| [Fowler + Böckeler 四象限框架](https://martinfowler.com/articles/harness-engineering.html) | ThoughtWorks | 前馈/反馈 × 计算型/推断型（computational/inferential）2x2 模型——按执行方式划分：AGENTS.md/Skills 属推断型前馈，codemod/结构测试属计算型 |
| [My AI Adoption Journey](https://hashicorp.com/blog/mitchell-hashimoto-my-ai-adoption-journey) | Mitchell Hashimoto | "每当 Agent 犯错，改变系统让错误不可能再发生" |
| [Lethal Trifecta / Rule of Two](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/) | Simon Willison / Meta AI | Agent 安全三要素与二选一规则 |
| [2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf) | Anthropic | 上下文工程行业趋势（Write/Select/Compress/Isolate 四策略分类采用 LangChain《Context Engineering》定义） |
| [Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents) | LangChain（2025.07） | 上下文工程四策略分类的原始出处：Write=保存到窗口之外（scratchpad/记忆/状态）、Select=拉取进窗口、Compress=摘要/修剪、Isolate=多代理/沙箱/状态分字段 |
| [CAR Framework](https://www.preprints.org/manuscript/202603.1756) | Preprints（2026.03） | 将 Harness 分解为 Control（控制）、Agency（代理）、Runtime（运行时）三层学术模型 |
| [Agent Harness Engineering: A Survey](https://openreview.net/pdf?id=eONq7FdiHa) | OpenReview（2026） | 独立综述，与 RUCAIBox 交叉验证"Harness 是可靠性主要因素" |
| [Improving Deep Agents with Harness Engineering](https://www.langchain.com/blog/improving-deep-agents-with-harness-engineering) | LangChain（2026） | 零模型改动纯 Harness 优化，Terminal Bench Top 30 → Top 5 的量化证据 |
| [Code as Agent Harness](https://arxiv.org/abs/2605.18747) | 多机构联合（2026.05） | 以代码为中心的 Harness 三层统一视图（Interface/Mechanisms/Scaling），覆盖 7 大应用领域 |
| [Natural-Language Agent Harnesses](https://arxiv.org/abs/2603.25723) | 清华等（2026.03） | 提出 NLAH + IHR，将 Harness 从代码转变为可版本控制、可 diff 的自然语言文档 |
| [Agentic Harness Engineering](https://arxiv.org/abs/2604.25850) | 复旦等（2026.04） | AHE 闭环自动进化 Harness，Terminal-Bench 2 69.7%→77.0%，超越人类设计；跨模型家族迁移验证 |
| [Stop Comparing LLM Agents Without Disclosing the Harness](https://openreview.net/forum?id=ffKHSraOIK) | OpenReview（2026.05） | Binding Constraint Thesis：Harness 配置方差 > 模型选择方差；提出 Harness 披露标准 |
| [AutoHarness](https://openreview.net/forum?id=g9rEYVNn5T) | Google DeepMind（2026） | 小模型自动合成 Harness 超越大模型，145 款游戏零非法走法 |
| [The Anatomy of an Agent Harness](https://www.langchain.com/blog/the-anatomy-of-an-agent-harness) | LangChain（2026.03） | 从模型局限逆向推导 Harness 组件的完整框架 |
| [State of Agent Engineering](https://www.langchain.com/state-of-agent-engineering) | LangChain（2026） | 1,300+ 调查：57% 生产部署、89% 可观测性、质量是最大生产障碍 |
| [Self-Harness: Harnesses That Improve Themselves](https://arxiv.org/abs/2606.09498) | 多机构联合（2026.06） | Agent 通过 Weakness Mining→Proposal→Validation 闭环自主改进 Harness，无需人类介入 |
| [SIA: Self Improving AI with Harness & Weight Updates](https://arxiv.org/abs/2605.27276) | 多机构联合（2026.05） | Harness + 模型权重联合自改进，法律/GPU/RNA 三任务验证，超越纯 Harness 迭代 |
| [What Makes a Harness a Harness](https://arxiv.org/abs/2606.10106) | 独立学者（2026.06） | Harness 术语的形式化构成定义，划定与 Agent Framework/SDK/Orchestrator 的清晰边界 |
| [How Much Heavy Lifting Can an Agent Harness Do?](https://arxiv.org/abs/2604.07236) | 韩国研究团队（2026.04） | 四层规划 Harness 量化实证：大部分 Agent 能力来自 Harness 组件而非 LLM |
| [Scaling Managed Agents](https://www.anthropic.com/engineering/managed-agents) | Anthropic（2026.04） | Brain/Hands/Session 三层解耦架构，p50 延迟降 60%，Session≠Context Window，凭据隔离 |
| [Building a C Compiler with Parallel Claude Teams](https://www.anthropic.com/engineering/building-c-compiler) | Anthropic（2026.02） | 16 Agent 集群 10 万行 Rust，lock file 自主协调，GCC torture 99% 通过率 |
| [Harness Engineering for Coding Agent Users](https://martinfowler.com/articles/harness-engineering.html) | ThoughtWorks（2026.04） | 控制系统框架（Guides/Sensors/Steering Loop），将 Harness 类比为控制论调速器 |
| [Build an Agent Improvement Loop](https://developers.openai.com/cookbook/examples/agents_sdk/agent_improvement_loop) | OpenAI（2026.05） | Traces→Evals→HALO→Codex Harness 改进飞轮，人工审查 + 自动执行的平衡模式 |
| [Loop Engineering](https://addyosmani.com/blog/loop-engineering/) | Addy Osmani / Google（2026.06） | Loop Engineering 体系化定义：五大构建块（Automations/Worktrees/Skills/Connectors/Sub-agents）+ State，"Loop sits one floor above the harness" |
| [From Agent Loops to Structured Graphs](https://arxiv.org/abs/2604.11378) | 多机构联合（2026.04） | 将 Agent 循环形式化为三层 SGH DAG（规划/执行/恢复），控制流从隐式上下文提升为显式静态图 |
| [LOOP Skill Engine](https://arxiv.org/abs/2605.14237) | 多机构联合（2026.05） | 记录一次 Agent 轨迹后确定性回放，99% 成功率，节省 93-99.98% token——Loop 的极致优化版本 |
| [Continual Harness](https://arxiv.org/abs/2605.09998) | 多机构联合（2026.05） | 无重置自改进 Harness，从轨迹数据在线优化 prompts/sub-agents/skills/memory，闭环自进化 |
| [Hacker-Fixer Loop](https://arxiv.org/abs/2606.08960) | 多机构联合（2026.06） | 三 Agent 对抗循环自动硬化基准验证器，将 reward hacking 成功率从 62% 降至 0% |
| [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) | Anthropic（2025.11） | initializer + coding agent 双角色 harness，200+ JSON feature 清单 + git 回滚实现跨 context window 状态交接 |
| [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) | Anthropic（2026.01） | 8 步评估设计框架，区分 capability/regression evals，归纳 code/model/human 三类 grader |
| [When AI builds itself](https://www.anthropic.com/institute/recursive-self-improvement) | Anthropic Institute（2026.04） | 内部一手数据：80%+ 代码 Claude 写、典型工程师每日合并代码行数约 8 倍（数量指标）；提出"人类审查成为新瓶颈"（Amdahl's law） |
| [Better Harness: A Recipe for Harness Hill-Climbing with Evals](https://www.langchain.com/blog/better-harness-a-recipe-for-harness-hill-climbing-with-evals) | LangChain（2026.04） | 6 步 Harness 迭代 recipe，holdout set 防过拟合，跨模型（Sonnet/GLM-5）验证泛化 |
| [How Good Is Your Harness? Statistical Evaluation of Coding Harnesses](https://openreview.net/forum?id=QI8z3skBwt) | 密歇根大学 / CTB@ICML 2026（2026.05） | 统计归因量化 Harness 与 LLM 贡献比：切换最佳 Harness 的增益 ≈ 切换最佳 LLM 的增益 |
| [Memory-Aware Software Engineering Agents](https://openreview.net/forum?id=WeXF1A3xY8) | KI 2026 AI4SE Workshop（2026.05） | 对 10 个生产级 SE harness 的 feature 级分析，发现 0/10 原生提供 episodic memory 或 temporal versioning |
| [A Survey on Agent Skills](https://openreview.net/forum?id=S184SW2SPX) | ACL ARR 2026（2026.05，审稿中） | 将 agent skills 形式化为"外化的过程性知识"，给出五维 taxonomy |
| [Harness-MU](https://arxiv.org/abs/2606.21856) | 多机构联合（2026.06） | 首个模型无关多用户 Agent 安全治理框架，执行钩子强制执行权限边界，Muses-Bench 零隐私泄露 |
| [Harness Engineering as Categorical Architecture](https://arxiv.org/abs/2605.12239) | 独立学者（2026.05） | 用范畴论三元组为 Harness 建立形式化数学基础，编译器函子实现跨运行时 Harness 编译 |
| [Effective Harness Engineering for Algorithm Discovery](https://arxiv.org/abs/2605.15221) | NEC-NTT 联合（2026.05） | 算法发现场景的 Harness 设计：少而深优于多而浅，更强模型产生更多评估作弊需检测 |
| [Harness Engineering for Physical AI](https://arxiv.org/abs/2606.09416) | 韩国研究团队（2026.06） | 将机器人中间件形式化为 Physical AI Harness 层，提出 Projection/Isolation/Transfer 三功能 |
| [HARBOR](https://arxiv.org/abs/2606.08610) | Li et al.（2026.06） | 把机器人 RL 工程流水线（建任务/奖励/调参）建模为 harness 问题：专门化 Agent + 标准化命令/artifact/可执行 gate + 去中心化并行试验与跨运行经验学习；6 基准 16 任务含 sim-to-real，补齐具身 Harness 的"研发时"端口（与 Physical AI 的"运行时"端口互补） |
| [The Interplay of Harness Design and Post-Training](https://arxiv.org/abs/2606.25447) | 韩国研究团队（2026.06） | Harness 作为可控设计维度的后训练研究，harness-aware post-training 提升 OOD 鲁棒性 |
| [Tuning Deep Agents to Work Well with Different Models](https://www.langchain.com/blog/tuning-deep-agents-different-models) | LangChain（2026.04） | Harness Profiles：按模型维护专属配置覆盖层，tau2-bench +10-20pp 量化证据 |
| [How to Build a Custom Agent Harness](https://www.langchain.com/blog/how-to-build-a-custom-agent-harness) | LangChain（2026.06） | 中间件式 Harness 扩展范式 + Task-Harness Fit 概念，`create_agent` 最小化原语 |
| [Deep Agents Deploy](https://www.langchain.com/blog/deep-agents-deploy-an-open-alternative-to-claude-managed-agents) | LangChain（2026.04） | 开源模型无关 Agent Harness，集成 MCP/A2A/Agent Protocol，单命令部署 |
| [Deep Agents v0.6](https://www.langchain.com/blog/deep-agents-0-6) | LangChain（2026.05） | 开源模型专属 Harness Profiles，20x+ 成本优势下缩小与前沿模型差距 |
| [Building Reliable Agentic AI Systems](https://martinfowler.com/articles/reliable-llm-bayer.html) | Martin Fowler / ThoughtWorks（2026.06） | Bayer AG PRINCE 制药研发平台案例，上下文工程 + Harness 工程双重透镜 |
| [Agent Harness Engineering: A Survey](https://openreview.net/forum?id=3hXEPbG0dh) | Li et al. / TMLR（2026.04） | ETCLOVG 七层 taxonomy，Observability/Governance 独立化，映射 170+ 开源项目 |
| [From Question Answering to Task Completion](https://www.preprints.org/manuscript/202606.1312) | 华为 Noah's Ark Lab（2026.06） | execution harness 六组件 + 四范式演进，三大基准 model-harness 对照量化，value-aware evaluation |
| [Agent Harness Engineering](https://addyosmani.com/blog/agent-harness-engineering/) | Addy Osmani / Google（2026.06） | ratchet 心法、AGENTS.md 精简、hooks 执行层（success is silent, failures are verbose）、Harness-as-a-Service |
| [MUSE: A Unified Agentic Harness for MLLMs](https://arxiv.org/abs/2606.03005) | 多机构联合（2026.06） | 多模态统一结构化执行 Harness，model-agnostic 可组合模块包裹现成 MLLM，证明 scaffold 优化逻辑跨模态成立 |
| [Is Grep All You Need?](https://arxiv.org/abs/2605.15184) | PwC（2026.05） | 检索场景实证：决定 agentic search 质量的是 harness（工具暴露/结果格式化/迭代循环）而非检索方法，为 Binding Constraint Thesis 提供交叉验证 |
| [The Bitter Lesson of Agent Harnesses](https://browser-use.com/posts/bitter-lesson-agent-harnesses) | Gregor Zunic / browser-use（2026.04） | helpers 也是抽象的极简主义心法，CDP 直连 + self-heal loop 让 agent 自补缺失工具；被 AHE 与 Binding Constraint Thesis 引用 |
| [AI Harness Engineering: A Runtime Substrate](https://arxiv.org/abs/2605.13357) | Zhong & Zhu（2026.05） | 能力涌现自 model-harness-environment 系统；11 项组件责任 + H0–H3 四级阶梯 + trace-based episode package 评估协议 |
| [Agent Harness for LLM Agents: A Survey](https://www.preprints.org/manuscript/202604.0428) | Preprints（2026.04） | 形式化六元组 H=(E,T,C,S,L,V)；六组件完整性矩阵对 23 系统分类——能进生产的系统无一例外六组件齐全；追溯 harness 从测试/RL 到 LLM Agent |
| [Harness Component Taxonomy, Evaluation, and Model](https://www.preprints.org/manuscript/202606.2203) | Preprints（2026.06） | 结构/匹配/动态三层分析；模型-harness 共演化——例行支撑迁入权重，约束性治理留在外部，为可剥离性划界 |
| [Tuning the Harness, Not the Model](https://www.langchain.com/blog/tuning-the-harness-not-the-model-a-nemotron-3-ultra-playbook) | LangChain（2026.07） | 开源模型只调 harness 即逼近 Opus 4.8（0.86 vs 0.87），成本约 1/10；harness-vs-weights 诊断、core vs profile 切分、signal placement |
| [How Middleware Lets You Customize Your Agent Harness](https://www.langchain.com/blog/how-middleware-lets-you-customize-your-agent-harness) | LangChain（2026.03） | 六钩子中间件 taxonomy；中间件两份工作（代码强制 vs 上下文工程）；PII 确定性合规——"无法用提示词实现 HIPAA 合规" |
| [Your Harness, Your Memory](https://www.langchain.com/blog/your-harness-your-memory) | LangChain（2026.04） | 记忆是 harness 核心职责而非外挂；闭源 harness/API 后的 compaction = 交出记忆控制权与平台锁定；Claude Code 泄露 512k 行即 harness 规模 |
| [Harness Engineering for Self-Improvement](https://lilianweng.github.io/posts/2026-07-04-harness/) | Lilian Weng（2026.07） | 上半年最系统单篇综述：prompt→context→workflow→harness→optimizer 演进框架，梳理 ACE/MCE/Self-Harness/AHE/SIA，对核心论点给能力区间细化 |
| [HarnessX: A Composable, Adaptive, and Evolvable Agent Harness Foundry](https://arxiv.org/abs/2606.14249) | arXiv（2026.06） | typed primitives + substitution algebra 组装，harness 作为运行时可演化类型化对象，与 Self-Harness 互补 |
| [ToFu: A White-Box, Token-Efficient Agent Harness for Researchers](https://arxiv.org/abs/2607.11423) | arXiv（2026.07） | 白盒、token 高效、面向研究的 agent harness，orchestration 可 inspect/modify/evaluate，提供可检验实验平台 |
| [The importance of Agent Harness in 2026](https://www.philschmid.de/agent-harness-2026) | Philipp Schmid（2026.01） | Harness 即操作系统的类比（Model=CPU/Context=RAM/Harness=OS/Agent=App）；context durability 为新瓶颈——静态榜单测不出长程漂移；"Harness is the Dataset"——竞争优势转向捕获的轨迹数据 |
| [Agent Engineering in 2026: The Harness Is the Product](https://markdown.engineering/blog/2026-04-12-agent-engineering-2026) | Markdown Engineering / Noah Greenfield（2026.04） | "restart engineering"论断与 durable stack 七层 artifact taxonomy；veto durability（负面知识）、replay safety（resume 不破坏现实）、显式 stop conditions；Georgiev 16→2 Agent 生产案例 |
| [Self-Evolving Agent Harnesses via Gated Semantic Quality-Diversity](https://arxiv.org/abs/2607.13683) | Luo et al.（2026.07） | 分离 propose/credit + 确定性显著性检验，credited improvement 构造性可信；跨模型迁移的是 diagnose-and-credit loop 而非具体 harness |
| [Harness Handbook](https://arxiv.org/abs/2607.13285) | Wang et al.（2026.07） | behavior localization 是 harness 演进瓶颈；behavior-centric 表示 + BGPD，用更少 planner token 改进定位与编辑计划质量 |
| [From Prompts to Contracts](https://arxiv.org/abs/2607.08028) | Ahn & Kim（2026.07） | 代码所有的 validation 在可替换 composition boundary 强制契约；120/120 utility vs 外挂 guardrail 88/120，证明只有代码强制能同时保安全与可用 |
| [Harness Engineering for Agentic AI Coding Tools](https://arxiv.org/abs/2602.14690) | Galster et al. / AIware 2026（2026.02，v5 2026.06） | 2853 仓库实证：八种配置机制，Context Files 主导，AGENTS.md 成跨工具互操作标准，Claude Code 用户机制范围最广 |
| [Harness-Bench](https://arxiv.org/abs/2605.27922) | Yao et al.（2026.05） | 首个诊断级 harness 效应基准，5194 轨迹证 model-harness 配对间差异显著；execution-alignment failure 概念 |
| [HASE](https://arxiv.org/abs/2607.03935) | Luo et al.（2026.07） | 单模型同一 loop 出解+改 harness，权重-harness-解共演化，小模型匹配大模型 |
| [Better Harnesses, Smaller Models](https://arxiv.org/abs/2607.08938) | Yang et al.（2026.07） | 任务难度从模型迁入 harness，SLM 恢复 89.7% LLM 表现仅 4% 成本；harness 补偿模型的量化反证 |
| [The Path to Recursive Self-Improving Agents](https://www.preprints.org/manuscript/202608.0051) | Preprints（2026.08） | 自改进 Agent 综述：五级分级标准（手动→通用递归自改进）+ 模型/Harness/数据系统/训练器/改进机制五组件耦合框架，组件内自改进与跨组件共改进 taxonomy |
| [SemaClaw](https://arxiv.org/abs/2604.11548) | Zhu et al.（2026.04） | 个人 Agent（OpenClaw 场景）harness 工程框架：DAG 两阶段混合编排、PermissionBridge 行为安全、三层上下文管理、agentic wiki skill——harness 差异化延伸到消费级 |
| [Agent Observability Needs Feedback](https://www.langchain.com/blog/agent-observability-needs-feedback-to-power-learning) | LangChain（2026.05） | trace 必要不充分，反馈附着轨迹存储才能构成学习闭环；四类反馈源；model/harness/context 三层失败归因 |
| [Improving Agents is a Data Mining Problem](https://www.langchain.com/blog/improving-agents-is-a-data-mining-problem) | LangChain（2026.07） | HE→FT→HE 三明治策略：harness 工程优先（快反馈高带宽），撞智能天花板再微调，微调后回 harness 验证泛化；TB 2.0 纯 harness 爬山 +13.7% |
| [3 Years of Graph Engineering with LangGraph](https://www.langchain.com/blog/3-years-of-graph-engineering-with-langgraph) | LangChain（2026.07） | graph vs agentic 循环决策边界：结构可预知用图、开放探索用核心循环、图节点可为完整 agent run；生产 Agent 图非 DAG，循环是核心 |
| [Rethinking the Evaluation of Harness Evolution for Agents](https://arxiv.org/abs/2607.12227) | UW 等多机构（2026.07，v4 2026.10） | 自进化 harness 再评估：须与预算对齐的 test-time scaling 基线比较 + held-out 泛化验证；Terminal-Bench 2.1 上自动 harness 进化不稳定优于简单搜索且泛化有限；**v4 补正结果边界**：长时程游戏是自进化利好区（难度留空间 + 依赖 OOD 适应 + 天然细粒度反馈），ARC-AGI-3 +80% / EdgeBench +11%——自进化的适用性取决于场景结构，非一刀切失败 |
| [Bad Genius: Counterfactual-Guided Harness Evolution](https://arxiv.org/abs/2609.18366) | Zhu et al.（2026.09） | task holdout 只换语义任务不换基准协议，"作弊 harness"仍可通过协议捷径得分；反事实引导 + 预注册 fresh-world 确认对抗捷径 |
| [ModularRSI: Modular and Generalizable Recursive Harness Self-Improvement](https://arxiv.org/abs/2609.14857) | Wu, Ren et al.（2026.09） | 递归自改进（RSI）模块化、通用化扩展到 agent harness，正面回应自进化泛化质疑 |
| [HarnessDev: Can LLMs Create and Evolve Their Own Agent Harness?](https://arxiv.org/abs/2609.01437) | Wu et al.（2026.09） | 把"创建和进化自己的 harness"本身作为评估对象——harness 从改进工具升格为被评估的能力 |
| [Externalization in LLM Agents](https://arxiv.org/abs/2604.08224) | 多机构联合（2026.04） | 记忆/技能/协议/Harness 工程统一为"外化"主题的四种子学科，跨领域概念地图 |
| [Symphony: Codex 编排开放规格](https://openai.com/index/open-source-codex-orchestration-symphony/) | OpenAI（2026.04，GitHub 26.9k stars） | harness engineering 官方下一步：从管理 agents 到管理 work；Linear 作控制平面、隔离自主实现运行、proof of work；SPEC.md 优先——规格一等公民，实现可替换 |
| [Harness Engineering: A Guide to AI Coding Agents](https://www.faros.ai/blog/harness-engineering) | Faros（2026.05） | 211 真实任务实测开放模型均等器（GLM-5.2/Kimi K2.6 + 优化 harness 反超 Opus 4.8/GPT-5.5）；五层 harness；Anthropic 失败模式三命名；Stage 1-3 分阶段度量 + session-to-PR 关联 |
| [Anatomy, Architecture, and Evolution of Coding Agents](https://arxiv.org/abs/2609.00006) | Barbaste et al.（2026.07，83 页） | 11 个生产 coding harness + Omnigent meta-harness 源码解剖：7 规范子系统、13 观察、29 设计模式；400 万行中零框架导入、零向量检索；SKILL.md（9/11）超 MCP（8/11）；受控纵向 diff——行为策略从 prompt 散文迁向配置；"harness 从工具到平台"实证定论 |
| [Trustworthy agents in practice](https://www.anthropic.com/research/trustworthy-agents) | Anthropic（2026.04） | Agent 四层组件模型（model/harness/tools/environment）——"行为是四层乘积，好模型也会被坏 harness 攻破"；Plan Mode 把监督从单步审批上移到策略层；check-in 率可训练（复杂任务翻倍）；prompt injection 多层防御无单层银弹；MCP 捐赠 Linux Foundation |
| [The State Of AI Harness Engineering 2026](https://marmelab.com/blog/2026/09/24/the-state-of-ai-harness-engineering-2026.html) | Marmelab（2026.09） | 246 仓库 + 145 对照 + 57 文献领域普查：同模型换 8 harness 成功率 68%→88%；63% 有指令文件但仅 14 hooks/4 subagents；60% harness 无 test/eval；25% harness 提交 AI 签名；负结果集（reviewer agent -8pp、>4 次交接必失败、预写规则少拦 20.1pp）；Vercel 砍 80% 工具成功率反升 |
| [Previewing the Model Hardware Standard](https://www.anthropic.com/news/model-hardware-standard-research-preview) | Anthropic（2026.08） | 具身 Harness 的标准协议层：AI Agent 安全并行操作物理仪器（显微镜/液体处理器/机械臂），硬件集成从数周到小时级，实时调参 + 硬件错误自恢复——MCP 的工具标准化角色在物理世界重演 |
| [Sequoia Ascent 2026 summary](https://karpathy.bearblog.dev/sequoia-ascent-2026/) | Andrej Karpathy（2026.04） | agentic engineering 大众化命名（vibe coding 抬地板 / agentic engineering 抬天花板），职责清单与 harness 工程逐项重合；agent-native 基础设施 sensors/actuators；2025-12 agentic 拐点一手观察 |
| [2025 LLM Year in Review](https://karpathy.bearblog.dev/year-in-review-2025/) | Andrej Karpathy（2025.12） | Claude Code "顺序优先级"分析：localhost 赢在已启动电脑/context/data/secrets/低延迟而非算力位置；LLM 应用层四职责（context engineering 居首 + DAG 编排 + 人机 GUI + 自主度滑杆） |
| [Verifiability](https://karpathy.bearblog.dev/verifiability/) | Andrej Karpathy（2025.11） | Software 1.0 自动化可指定、2.0 自动化可验证；可验证环境三条件 resettable/efficient/rewardable——任务自动化潜力预判框架 |
| [Animals vs. Ghosts](https://karpathy.bearblog.dev/animals-vs-ghosts/) | Andrej Karpathy（2025） | LLM 是统计模拟的"幽灵"而非动物智能，能力 jagged 是常态——人在回路与经验主义使用姿势的理论依据 |
| [The space of minds](https://karpathy.bearblog.dev/the-space-of-minds/) | Andrej Karpathy（2025） | 优化压力（商业进化 vs 生物进化）决定 LLM 智能渐近线；建立准确内部模型者更能预测其行为边界 |
| [Super-agent 设计探索](https://mp.weixin.qq.com/s/zoS7GlReWPhfCjENGxXCdg)（[原型 dsh-superagent-prototype](https://github.com/deepseek-ai/dsh-superagent-prototype)） | DeepSeek Harness 团队（2026.10） | 首个公开的多任务组织级 Harness：双层架构（Router/Worker）+ 事件驱动（两级过滤/订阅直通/Inline & Spill）+ Mandate 授权准入（Gate+Assessment+观察模式）；消融实验证明分工固化到系统层优于提示词临场分工（p95 29.5→15.7min，合成负载）；原型 15K 源码+15K 测试，单进程无 Watchdog、主动工作默认只记录不执行——引用需保留"初步、自报、合成负载"限定 |
| [Are Harnesses Worth the Cost?](https://martinfowler.com/articles/are-harnesses-worth-the-cost.html) | Naresh Jain / martinfowler（2026.10.13） | harness 价值的条件性量化实证：前沿模型全程 harness 多花约 10.5 倍 token（约 6.6 倍成本）验收零增益；成本优化模型 3/10→10/10 且样本内比前沿模型便宜 33%；提出 executable attention 与"sensor 时机随模型能力"假设（单一 greenfield 服务、10 对运行、guides/sensors 未消融、终门方案未实测） |
| [Skill Issue: Harness Engineering for Coding Agents](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents) | HumanLayer（2026.03） | 配置杠杆全景：harness engineering ⊂ context engineering；sub-agent = context firewall（设计模型，可见性随运行时实现而异）；重复能力优先用 CLI 是经验建议（客户端加载策略各异，先核实所用客户端）；back-pressure（成功静音失败啰嗦）；"bias towards shipping"——只在真实失败后加配置；TB2 上 Opus 4.6 换 harness #33→#5（支持过拟合假设的排名证据，非因果证明） |
| [Trustworthy LLM Harnesses: A Measurement Framework](https://www.preprints.org/manuscript/202610.1366) | Preprints（2026.10，预印本未同行评审） | harness 信任性七维测量框架（correctness/quality/efficiency/reliability/robustness/safety/fairness），把"harness 好不好"从单一成功率升级为显式维度集 |
| [Sustaining Long-Horizon Execution with LLM Agent Harnesses](https://openreview.net/forum?id=NS9fp7ZTWa) | OpenReview（2026.10.27） | 将 agent harnesses 组织为四个功能层，聚焦长时程执行的维持（此前综述多以组件集成或应用工作流组织）。注：摘要级核验——OpenReview 页面验证墙未能读取全文，分层细节待复核 |
| [New context engineering challenges in late 2026](https://www.langchain.com/blog/context-engineering-challenges-late-2026) | Harrison Chase / LangChain（2026.11.02） | 在已有标准上下文工程实践（长线程摘要/大结果卸载/skills 渐进披露/tool search/subagents 隔离/bash 主工具）之上，proactive、always-on、goal-oriented agents 带来一组尚无通用解的新挑战：通知校准（proactive without being annoying）、记忆智能层（记什么/何时记）、异步完成通知（主 agent 在跑/已结束/等审批三态）、目标跟踪、对齐防作弊（"删光代码=没有 bug"）、浏览器上下文（通用 agent 的 bash） |
| [Your coding agents made you a manager](https://x.com/addyosmani/status/2119332146485891501) | Addy Osmani（2026.11.08） | 工程管理视角的 Agent 委派：编排税（并行代理注意力负担递增——个人经验观察非定量规律）、code ratification（绿灯+命名合理≠需求与风险已被判断，为反模式 11 补质量侧表现）、验证机制/纠正复利/无人值守五问（与库内 Verifiability/ratchet/stop conditions 互证）、管理类比边界（激励/冲突/职业发展/信任建立无代理对应物）；未提供受控量化证据，引用保留个人经验限定 |

---

## 评估维度

### D1：Fowler 四象限覆盖度

> 来源：[Harness Engineering for Coding Agent Users](https://martinfowler.com/articles/harness-engineering.html)（Böckeler / martinfowler.com，2026.04）
> 原则：生产级 Harness 需要四象限全部覆盖——纵深防御。两维 = 方向（前馈/反馈）× 执行方式（计算型/推断型，computational/inferential）。

#### D1.1 推断型前馈（指南）

Agent 开始工作前就将其引向正确方向。指南靠模型遵循发挥作用，属**推断型**控制——文件被确定性注入不等于指令被确定性执行。

**检查项**：
- [ ] 存在项目级 CLAUDE.md / AGENTS.md / .cursorrules
- [ ] 编码规范明确（缩进、引号、命名约定）
- [ ] 分层架构规则有文档说明（Controller/Service/Model 各自职责）
- [ ] 领域术语有统一定义（CONTEXT.md 或术语表）
- [ ] Git 提交规范有明确定义
- [ ] 工作流路径有清晰说明（新功能 / Bug 修复各走什么流程）
- [ ] 指南文件保持精简（pilot's checklist 而非 style guide），每条规则可追溯到一次真实失败（ratchet 心法——参考 Osmani Agent Harness Engineering）
- [ ] 采用 AGENTS.md 作为跨工具互操作标准（一份文件多个工具可读，而非每个工具各写一份冗余指南——参考 Coding Tools 实证的 2853 仓库基线：Context Files 主导配置）
- [ ] 工具暴露按上下文效率取舍（重复能力优先用 CLI 而非 MCP server 是经验建议——在把工具描述注入上下文的客户端中，工具描述挤占指令预算；加载策略随客户端而异，先核实所用客户端行为（如 tool search 渐进披露）；不信任的 server 永不连接——参考 HumanLayer + Vercel 砍工具实证）

**评估标准**：
- 1 星：无任何项目级指南文件
- 3 星：有 CLAUDE.md 但内容笼统
- 5 星：多层指南（全局 + 项目 + 子系统），含领域术语表和示例代码

#### D1.2 计算型反馈（机械性检查）

即使指南被忽略，也能自动捕获错误。

**检查项**：
- [ ] 代码风格检查已配置（ESLint、Pint、Prettier 等）
- [ ] 类型检查已配置（TypeScript、PHPStan、Psalm 等）
- [ ] 风格检查集成到 pre-commit hook 或 Claude Code hooks
- [ ] 静态分析工具已配置（安全扫描、架构合规检查）
- [ ] 上述检查在 CI 中自动运行

**评估标准**：
- 1 星：无任何自动化检查
- 3 星：有 Linter 但未集成到提交流程
- 5 星：Linter + 类型检查 + 静态分析 + pre-commit hook + CI 全覆盖

#### D1.3 推断型前馈（系统提示词）

处理推断型指南无法保证的细微之处（系统提示词同为推断型前馈）。

**检查项**：
- [ ] Agent 角色定义清晰（每个 Agent 的职责边界）
- [ ] 行为约束明确（安全敏感操作必须获得审批等）
- [ ] 少样本示例（代码模板、期望输出格式）
- [ ] 自定义 Agent 的 SYSTEM PROMPT 经过设计而非随意编写

**评估标准**：
- 1 星：使用默认提示词，无自定义 Agent
- 3 星：有自定义 Agent 但角色定义笼统
- 5 星：每个 Agent 有精确的角色、工具限制、输出格式、示例

#### D1.4 推断型反馈（审查）

捕获"能编译但语义错误"的代码。

**检查项**：
- [ ] 有代码审查流程（人工或 Agent 审查）
- [ ] 使用多 Agent 审查（生成者与评估者分离）
- [ ] 审查有结构化检查清单（安全、质量、架构等维度）
- [ ] 审查结果可追溯（保留审查报告）
- [ ] 审查 Agent 有记忆（跨会话积累问题模式）

**评估标准**：
- 1 星：无审查流程
- 3 星：有单一审查 Agent 或纯人工审查
- 5 星：独立第二视角审查（多 Agent 审查 / 人工审查 / 单 Agent + 独立验证均为等价证据，多 Agent 非硬条件——Marmelab 实测加 reviewer agent 反而 -8pp，须有证据其增益为正）+ 检查清单 + 交叉验证；审查记忆系统按需（跨会话问题模式积累对高频使用项目有价值，低频项目标 N/A）

> **必备/按需分层**：必备 = 有审查流程 + 结构化清单 + 结果可追溯；按需高级 = 多 Agent 审查、审查记忆系统

---

### D2：上下文工程

> 来源：[Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)（LangChain，2025.07，四策略原始出处）+ Anthropic（2026 Agentic Coding Trends Report）
> 原则：从 Agent 的角度看，任何无法在上下文中访问的东西都不存在。仓库必须是唯一的事实来源。

#### D2.1 Write（保存到窗口外）

**检查项**：
- [ ] 关键工作状态有窗口外持久化（scratchpad / 进度文件 / 状态字段——LangChain 定义：Write 指把上下文保存到 context window 之外）
- [ ] 跨会话知识有持久化（decisions.md、模式文档、长期记忆）
- [ ] 系统提示词清晰、结构化（上下文准备实践，与 Write 正交但常一起评估）
- [ ] 编码规范有代码示例（不只说"用 Service 层"，还给出 Service 的模板）
- [ ] API 契约有文档（请求/响应格式、错误码）
- [ ] 架构决策有记录（ADR 或等价物）

#### D2.2 Select（选择性拉取）

**检查项**：
- [ ] 不同任务类型加载不同的上下文（非全量加载）
- [ ] 项目级指南按子系统分离（前端/后端各自的 CLAUDE.md）
- [ ] 按需加载的工具和权限（非一次性全部暴露）
- [ ] 上下文按 Cache 管理（Inline & Spill）：高局部性信息内联进窗口（相邻消息、关联对象 ID、内容摘要），超出预算溢出为"引用 + 摘要"按需读取——内联太多稀释注意力、太少徒增工具往返，两头都是浪费（参考 DeepSeek Super-agent 的有界窗口：首 5 条 + 触发点两侧各 5 条 + 50 字符 stub）
- [ ] 跨对象上下文用引用图链式召回（对象间内联关联 ID，按需逐跳展开到关联任务/评审线程），且读引用带白名单校验（分页游标/对象 ID 必须来自先前返回值，防模型幻觉游标——参考 DeepSeek Super-agent 的 cursor 白名单 + 分页快照缓存）

#### D2.3 Compress（压缩）

**检查项**：
- [ ] 有对话压缩/摘要机制（处理长会话）
- [ ] Agent 记忆系统可将历史经验压缩为模式文档
- [ ] 启用了 KV-cache 优化（稳定前缀 + 可变后缀架构）
- [ ] Agent 记忆具备情景维度与时序版本（episodic memory / temporal versioning）——被研究的 10 个生产级 SE harness 中 0 个原生提供这两项，是长任务可靠性的关键缺口（参考 Memory-Aware SE Agents）
- [ ] 记忆所有权与可移植性有考量（记忆归项目/用户所有，compaction 与长期记忆不锁定在闭源 harness 或厂商 API 后；换模型/换 harness 能带走记忆——参考 Your Harness, Your Memory）
- [ ] 负面知识有独立持久化（被否决/取消的决策作为独立状态保存，如 decisions.md 带日期与理由；不与即时工作集、事实摘要混放，防止会话重启后被重新推导——参考 The Harness Is the Product 的 veto durability）

#### D2.4 Isolate（隔离）

**检查项**：
- [ ] 子任务可委托给独立子 Agent
- [ ] 子 Agent 有独立的上下文窗口和工具集
- [ ] 子 Agent 的结果通过结构化接口返回（非自由文本）

**评估标准**：
- 1 星：无项目级文档，Agent 完全依赖对话上下文
- 3 星：有 CLAUDE.md 但缺少 Select/Compress/Isolate 策略
- 5 星：四策略全覆盖 + 项目级文档分层 + 记忆系统 + KV-cache 优化

> **必备/按需分层**：必备 = 四策略按任务适用（短程单轮任务用不到 Compress/Isolate 属正常）；按需高级 = 记忆系统、KV-cache 优化（高频/长程/多会话任务才适用）。评估实际上下文质量与成本，而非要求每个项目具备同一实现——不适用标记 N/A，不按缺失扣星

---

### D3：架构约束

> 来源：OpenAI（Harness Engineering 三大支柱）
> 原则：不是告诉 Agent "写好代码"，而是机械性地强制好代码的样子。约束解决方案空间反而让 Agent 更高效。

**检查项**：
- [ ] 分层架构有明确规则（如 Controller → Service → Model）
- [ ] 分层规则由自动化工具强制执行（非仅靠文档）
  - 结构性测试（如 ArchTest、PHPStan 自定义规则）
  - 自定义 Linter 规则
  - CI 中运行的架构合规检查
- [ ] 依赖方向有规定（如 UI 层不能直接导入 Model 层）
- [ ] 命名约定有文档且可验证
- [ ] 文件/目录结构有约定（Agent 可以"看地图"而非"翻手册"）

**评估标准**：
- 1 星：无架构规则，Agent 自由生成
- 3 星：架构规则写在文档中但无机械性强制
- 5 星：架构规则由结构性测试 + Linter + CI 多层强制执行

---

### D4：熵管理

> 来源：OpenAI（Harness Engineering 三大支柱）+ [Meta-Harness](https://arxiv.org/html/2603.28052v1)（2026.03）+ [Self-Harness](https://arxiv.org/abs/2606.09498)（2026.06）+ [SIA](https://arxiv.org/abs/2605.27276)（2026.05）+ [Gated QD](https://arxiv.org/abs/2607.13683)（2026.07）+ [Recursive Self-Improving Agents](https://www.preprints.org/manuscript/202608.0051)（2026.08）+ [Data Mining](https://www.langchain.com/blog/improving-agents-is-a-data-mining-problem)（LangChain，2026.07）+ [Rethinking Harness Evolution](https://arxiv.org/abs/2607.12227)（2026.07）+ [ModularRSI](https://arxiv.org/abs/2609.14857)（2026.09）
> 原则：最被低估的组件。AI 生成的代码库会积累熵——文档偏离现实、命名约定分化、死代码堆积。Harness 本身也会积累熵——2026 年最新研究证明，可以让 Agent 自己管理这种熵。

**检查项**：
- [ ] 有定期清理/审计机制（Agent 记忆、权限列表、文档）
- [ ] 文档与代码有一致性验证手段
- [ ] Agent 记忆中已修复的问题被标记（非无限积累）
- [ ] settings/permissions 有定期清理策略
- [ ] Harness 组件在模型更新后有审查流程（可剥离性检查）
- [ ] 不活跃的 skills/agents 有识别和清理机制
- [ ] Harness 组件的有效性有量化追踪（如某条 Linter 规则过去 3 个月是否触发过）
- [ ] 有 Harness A/B 测试机制（对比不同 Harness 配置的 Agent 成功率）
- [ ] Harness 变更有可观测性追踪（每次编辑配对自己的预测，下一轮用任务结果验证——参考 AHE 的决策可观测性）
- [ ] Harness 组件的贡献可归因（工具 vs 中间件 vs 系统提示词各自对成功率的贡献可独立测量）
- [ ] Harness 有自愈能力（Agent 可自动识别 Harness 缺陷 → 提出修改 → 验证 → 合入，参考 Self-Harness 的 Weakness Mining→Proposal→Validation 闭环）
- [ ] Harness 与模型权重的协同优化有考量（是否只优化 Harness 而忽略了权重联合优化的潜力，参考 SIA）
- [ ] 错误→规则的 ratchet 机制存在（Agent 的每次失败都被固化为一条可追溯的 Harness 规则，而非停留在"偶发事故"——参考 Osmani）
- [ ] 可剥离边界按"例行支撑 vs 约束性治理"区分（例行能力支撑随模型进步可删/迁回模型，约束性治理如 PII/权限/预算必须留在外部——参考 Component Taxonomy Survey 的模型-harness 共演化）
- [ ] 模型版本升级触发标准化审计 SOP（冻结基线→分类→跨模型 holdout 验证→ratchet 触发条件），区分例行支撑（可删）与约束治理（不可删）
- [ ] Harness 删改遵循跨模型 holdout 验证 + 可证伪合约（每次删改附失败证据/根因/预期影响，下一轮用任务结果验证——AHE 决策可观测性）
- [ ] 自进化的 credited improvement 由确定性代码记功（分离 propose/credit：模型只诊断失败、提补丁，采样/测量/显著性检验由代码完成，避免模型自评噪声与对训练任务的过拟合——参考 Gated QD）
- [ ] harness 规则可定位（每条规则能快速找到实现位置，"行为到代码"映射可恢复而非散落文件靠记忆——参考 Harness Handbook 的 behavior localization）
- [ ] 改进投入按「harness 工程 vs 微调」分层决策（harness 工程优先——反馈快、带宽高；撞智能天花板或需蒸馏时才微调，微调后回 harness 工程验证泛化——参考 LangChain HE→FT→HE 三明治）
- [ ] 自改进能力有分级路线图（明确当前处于手动/组件级/跨组件共改进哪一级，及向上一级缺什么——参考 Recursive Self-Improving Agents 五级分级）
- [ ] 自进化增益有公平归因（harness 自改进的收益与预算对齐的 test-time scaling 基线对比过，且在搜索所用基准之外的 held-out 任务上验证泛化——避免把"更多搜索"记成功劳、把基准过拟合当进步，参考 Rethinking 的双重协议要求）

**评估标准**：
- 1 星：无任何熵管理意识
- 3 星：意识到问题但无自动化机制
- 5 星：有定期清理 Agent、文档一致性检查、模型更新审查流程、Harness 有效性量化追踪、组件级贡献归因、Harness 自愈闭环、改进策略分层

> **必备/按需分层**：必备 = 定期复核、回滚能力、可观测（基础维护三件套）；按需高级 = 自愈闭环、组件级贡献归因、改进策略分层（研究型投入，在 harness 数量大到人工维护不动时才有正收益，非基础维护必选）

---

### D5：安全防护

> 来源：Simon Willison（致命三要素）+ Meta AI（二选一规则）+ [Trustworthy agents in practice](https://www.anthropic.com/research/trustworthy-agents)（Anthropic，2026.04）
> 原则：处理不可信输入 + 访问敏感数据 + 具备对外通信能力 = 攻击者可通过提示注入轻易诱导数据外传。最多同时拥有两个。Agent 行为是 model/harness/tools/environment 四层组件的乘积——好模型也会被配置不当的 harness、过度放权的工具或暴露的环境攻破。

#### D5.1 致命三要素识别

**检查项**：
- [ ] 项目是否识别了三要素的暴露面
  - 不可信输入（用户输入、外部 API 响应、文件上传）
  - 敏感数据（数据库、密钥、用户隐私）
  - 对外通信/外传路径（发邮件、HTTP/API 请求、创建公开 PR/issue、可点击链接——注意：不改本地状态的工具同样构成外传通道）
- [ ] 对同时满足三要素的场景有防护措施

#### D5.2 二选一规则执行

**检查项**：
- [ ] Agent 工具权限按最小权限原则配置
- [ ] 危险操作（删除、扣款、发送）需要人类审批
- [ ] 有 Policy as Code 或等价的权限策略文件
- [ ] 生产环境与开发环境的 Agent 权限有区分

#### D5.3 安全审查

**检查项**：
- [ ] 有安全审查流程（Agent 或人工）
- [ ] 安全审查覆盖 OWASP Top 10（SQL 注入、XSS、认证绕过等）
- [ ] 安全测试有自动化（非仅依赖审查 Agent）
- [ ] 敏感数据（密钥、密码）不在代码或 CLAUDE.md 中
- [ ] 多用户/多租户场景有独立的安全治理层（非仅依赖提示词约束——参考 Harness-MU 的执行钩子强制执行权限边界）
- [ ] 确定性合规由中间件钩子强制执行（PII 脱敏、内容审核等"每次必触发"的策略用代码实现而非提示词——"无法用提示词实现 HIPAA 合规"，参考 Middleware Customization）
- [ ] 契约验证围绕可替换的组合边界用代码强制（answer contracts / entity routing / output hygiene 由代码所有，跨模型替换仍成立；外挂 guardrail 会 over-refuse 牺牲可用性，提示词两头不靠——参考 Prompts to Contracts 的 120/120 vs 88/120）
- [ ] 防御覆盖四层组件而非仅模型层（model/harness/tools/environment 各层有独立审计：harness 指令与护栏、工具最小权限、环境访问边界——"没有单层防御足够"，prompt injection 需模型识别 + 运行时拦截 + 红队实测多层叠加——参考 Trustworthy agents）
- [ ] 事件驱动的任务授权建模为可再验证对象（Mandate 模式：accept 时固化策略版本/强度/准入依据；Gate 用确定性代码查准入表、LLM Assessment 输出被 schema 强约束；策略变更逐任务复验而非一刀切撤销；主动工作先 observe 只记录不执行——参考 DeepSeek Super-agent）。注意两条限定：通知式撤权对在跑任务的外部副作用无强制力，高风险操作需在执行边界再做确定性权限检查；批量评估逐条独立判定授权，防批内搭便车（反模式 17）

**评估标准**：
- 1 星：无安全意识，Agent 拥有完全权限
- 3 星：有安全审查 Agent 但无结构性防护
- 5 星：三要素识别 + 二选一规则 + Policy as Code + 自动化安全测试 + 四层组件纵深防御 + 多用户治理层（多用户/多租户场景必备；单用户项目以最小权限 + 环境隔离 + 自动化安全验证的等价组合评估，不因无多用户场景而封顶）

---

### D6：测试与验证

> 来源：Andrew Ng（Agentic 工作流）+ Anthropic（三 Agent 架构）+ [Agent Observability Needs Feedback](https://www.langchain.com/blog/agent-observability-needs-feedback-to-power-learning)（LangChain，2026.05）+ [Rethinking Harness Evolution](https://arxiv.org/abs/2607.12227)（2026.07）+ [Bad Genius](https://arxiv.org/abs/2609.18366)（2026.09）+ [Faros Harness Guide](https://www.faros.ai/blog/harness-engineering)（2026.05）+ [State of AI Harness Engineering](https://marmelab.com/blog/2026/09/24/the-state-of-ai-harness-engineering-2026.html)（Marmelab，2026.09）+ [Verifiability](https://karpathy.bearblog.dev/verifiability/)（Andrej Karpathy，2025.11）
> 原则：用 Agentic 工作流包装 GPT-3.5 超过 GPT-4 零样本表现——好的测试套件让 Agent 更安全地工作。Harness 自身也需要被测试——普查显示 60% 的 harness 既无 test 也无 eval，"一堆 markdown 指令，没人知道是否成立"。

**检查项**：
- [ ] 测试框架已配置（PHPUnit、Vitest、Playwright 等）
- [ ] 核心业务流程有测试覆盖（特别是资金/交易/认证等关键路径）
- [ ] 测试遵循安全实践（测试隔离、不污染真实数据）
- [ ] Agent 可以运行测试来自我验证（测试命令在权限白名单中）
- [ ] 测试覆盖率在不同模块间均衡（非某些模块 0% 某些模块密集）
- [ ] 有端到端测试或集成测试（非仅单元测试）
- [ ] 区分 capability evals 与 regression evals，后者维持接近 100% 通过率专门捕获回退（参考 Demystifying Evals）
- [ ] Harness 变更用 holdout set 验证泛化，防止过拟合到已知用例（参考 Better Harness）
- [ ] Trace 与反馈信号关联存储（反馈挂在对应 run/trace 上，可按成败过滤轨迹、从真实失败构建 eval 数据集，非散落在独立表格——trace 告诉你发生了什么，反馈告诉你对不对，参考 Agent Observability）
- [ ] 反馈源覆盖多类（直接用户/间接行为信号/LLM-as-judge/确定性规则至少两类；已知失败模式优先编码为确定性规则，零模型调用——参考 Agent Observability 四类反馈源）
- [ ] 评估协议防基准作弊（holdout 之外确认基准协议本身未被利用——task holdout 只换语义任务不换协议，作弊 harness 仍可走协议捷径；关键改动用反事实/fresh-world 确认，参考 Bad Genius）
- [ ] Agent 工作有 session-to-PR 关联度量（agent 会话连到产出的 PR 并标注意图，支撑 first-pass success、代码存活率、缺陷逃逸率——按现有数据分阶段起步，参考 Faros Stage 1-3）
- [ ] Harness 自身有双向 guard 测试（每条规则/hook 各测"该拦的拦住"与"该放行的放行"两类用例——只测拦截会让收紧永远安全、放松不可见，harness 漂向拦死一切；参考 Marmelab："单侧 eval 制造单侧优化"，codex-claude-code-config 63 用例测 12 个 guard）
- [ ] 每个 control 有 expiry condition 与背后事故记录（一行注明该 hook/规则为哪次失败而存在；模型升级或架构变更后可安全判断去留，避免死重累积——参考 Marmelab 腐化治理与 OpenAI doc-gardening agent）
- [ ] 验证环境满足"可练习"三条件（resettable 可重开尝试 / efficient 可大量尝试 / rewardable 有自动打分——三条件不满足时先补评价机制如 LLM-as-judge 或确定性 rubric 再谈优化；任务可验证性直接决定其自动化潜力，参考 Karpathy Verifiability）
- [ ] 反馈节奏按任务实测基线选择，独立验收门恒在（"强模型裸跑+终门、弱模型全程 executable attention"是单任务实验提出的待验证候选，非通用规则；按基线通过率、失败成本与反馈成本实测后选节奏——实验边界：单一 greenfield 服务、10 对运行、guides/sensors 未消融、终门方案未实测，参考 Are Harnesses Worth the Cost?）

**评估标准**：
- 1 星：无测试基础设施
- 3 星：有测试框架但覆盖集中在少数模块
- 5 星：核心业务全覆盖 + 测试隔离 + Agent 自验证 + 端到端测试 + trace 反馈闭环 + harness 自身双向测试与腐化治理

---

### D7：CI/CD 与自动化

> 来源：Stripe Minion 系统实践
> 原则：Stripe Minion 每周 1000+ 合并的 PR，关键在于 CI 自动处理一切。

**检查项**：
- [ ] 有 CI 配置（GitHub Actions、GitLab CI 等）
- [ ] CI 中自动运行 Lint + 类型检查 + 测试
- [ ] 部署流程有自动化（非纯手动脚本）
- [ ] Agent 生成的代码能自动进入 CI 流水线
- [ ] CI 失败时有明确的反馈路径（Agent 可读取失败信息并修复）

**评估标准**：
- 1 星：无 CI，纯手动部署
- 3 星：有 CI 但覆盖不全或部署仍手动
- 5 星：CI 全覆盖（Lint + 类型 + 测试 + 安全扫描）+ 自动化部署

> **必备/按需分层**：必备 = CI 全覆盖 + 可复现验证 + 适用的发布审批与回滚；按需高级 = 自动化部署——不部署的软件（库/CLI）或高风险人工发布场景，人工审批是正当形态而非缺失，标 N/A 或按"审批 + 回滚"证据评分

---

### D8：Agent 工作流编排

> 来源：综合（Anthropic 三 Agent + LangChain 自验证 + Vercel 减法优化 + Loop Engineering 五大构建块 + Structured Graph Harness + LOOP Skill Engine + Bitter Lesson 极简主义 + [Graph Engineering](https://www.langchain.com/blog/3-years-of-graph-engineering-with-langgraph)（LangChain，2026.07））
> 原则：最好的 Harness 改进往往来自"做更少"。2026 年 6 月，Loop Engineering 将工作流编排从"手动触发"推进到"自动调度 + 自驱动"——Harness 装上时钟。

**检查项**：
- [ ] 新功能开发有明确的 Agent 工作流路径
- [ ] Bug 修复有明确的 Agent 工作流路径
- [ ] 工作流中包含验证步骤（非仅生成）
- [ ] Agent 的工具集经过精简（无冗余工具造成困惑）
- [ ] 工作流有失败恢复路径（Agent 卡住时的升级策略）
- [ ] 多 Agent 协作时有清晰的职责分工和通信协议
- [ ] 并行代理较多时有注意力预算观察（待决事项、审查积压、人工协调负担是否随并发上升——编排税是个人经验观察非定量规律，按自身工作流观察后调整并发，参考 Osmani 2026.11）
- [ ] 有自动化调度机制（定时触发或事件驱动，非纯手动启动——参考 Loop Engineering 的 Automations 原语）
- [ ] 有跨会话状态持久化（进度文件/看板，Loop 中断后能从中断处继续——参考 Loop Engineering 的 State 原语）
- [ ] 维护循环内容外部化为可配置文件（循环跑什么写成文件、运行时可优化，非硬编码在调度配置或每次手写——参考 Claude Code `loop.md`：Skills+State+Automations 三个原语交汇落点）
- [ ] 有目标驱动运行模式（`/goal` 类能力：持续运行直到可验证条件满足，判定由独立模型完成，非 self-grade）
- [ ] 高频重复任务有确定性回放机制（首次记录→后续回放，避免重复消耗推理 token——参考 LOOP Skill Engine）
- [ ] 跨 context window 的长任务有结构化状态交接（结构化进度文件 + git 回滚，非依赖模型记忆——参考 Effective Harnesses for Long-Running Agents）
- [ ] 有模型专属 Harness 配置机制（不同模型家族有不同提示词/工具/中间件配置，非"一套配置适配所有模型"——参考 Tuning Deep Agents 的 Harness Profiles）
- [ ] Harness 扩展通过可组合中间件实现（每条规则是独立模块，新增/删除一行配置即可——参考 How to Build a Custom Agent Harness 的中间件式扩展范式）
- [ ] 缺失能力有 agent 自补机制（agent 遇到缺失工具时能自行编写 helper 而非阻塞报错，参考 Bitter Lesson 的 self-heal loop）
- [ ] Harness 改动遵循 core vs profile 切分纪律（每条改动追问"能往 core 推多远"，模型专属部分隔离到 profile，让 core 保持干净可跨模型复用——参考 Nemotron Playbook）
- [ ] 恢复执行遵循 replay safety（从检查点/会话恢复时不重复副作用——副作用包成幂等或隔离，从持久化结果取回而非重算；"resume 不破坏现实"——参考 The Harness Is the Product）
- [ ] 有显式 stop conditions（done / blocked / awaiting_approval / awaiting_external_event / needs_human_decision / aborted_due_to_budget 等明确终止态，而非"一直跑到 token 耗尽"——参考 The Harness Is the Product）
- [ ] 编排形态按任务可预知度选择（结构可预知的流程用图/确定性路径编码，开放探索用 agentic 核心循环；图节点可为完整 agent run，固定/模型/Agent 三类节点可混排——参考 Graph Engineering 的决策边界）
- [ ] 多任务/事件驱动场景采用双层调度（Router/主会话只承接交互与分派、不直接执行任务；Worker/子会话上下文隔离并发，已归属 Thread 的后续事件直通对应 Worker 不经 Router 转发），分工固化到系统层而非靠提示词让模型临场决定——消融实验：中性提示词下模型 5 次运行 0 次调用 Subagent（p95 29.5min）vs 双层架构 p95 15.7min；直通省 Router 决定数 30%/token 37%（参考 DeepSeek Super-agent，合成负载限定）
- [ ] 人的输入在并发调度中保留优先机会（人的 follow-up 唤醒可用超出常规上限的额外并发槽，不排在批量任务启动之后——参考 DeepSeek Super-agent 的 WorkerSlots；注意只是"额外机会"而非绝对不排队，超额槽被占时仍需等待）
- [ ] 事件准入做两级过滤（确定性程序策略先筛——来源/群组/白名单等稳定判断固化为代码；LLM 语义策略兜底——用提示词开关或新增策略扩展），在调用模型前滤除大部分噪音（参考 DeepSeek Super-agent 的 Fast/Slow Path；事件驱动系统"高噪音、低触发率"，逐条调模型延迟与算力都不可承受）
- [ ] 主动式 Agent 的上下文策略有显式设计（proactive/always-on 场景的新挑战：通知推送的校准策略、异步工作完成时的通知与主 agent 三态处理（在跑/已结束/等审批）、决定"记什么何时记"的记忆智能层——摘要/卸载等已有实践之上的一组无通用解问题，参考 Chase《New context engineering challenges in late 2026》）

**评估标准**：
- 1 星：无工作流，Agent 自由发挥
- 3 星：有基本流程但缺少验证/恢复环节，无自动化调度
- 5 星：完整工作流 + 验证 + 恢复 + 工具精简 + 多 Agent 协作 + 自动化调度 + 跨会话状态 + 目标驱动 + 高频任务确定性回放 + 模型专属配置 + 可组合中间件 + agent 自补缺失工具 + 编排形态按可预知度选择 + 事件驱动双层调度（按需）

> **必备/按需分层**：必备 = 完整工作流 + 验证 + 恢复 + 工具精简；其余全部按需高级——按任务可预知度与规模选择（结构可预知才上图/调度，高频重复才上回放，多模型才上 profile，事件驱动多任务才上双层调度与两级过滤）。能力越多权限面越大，与"做更少"哲学（Vercel 砍 80% 工具、Bitter Lesson 心法）互相平衡；自补工具（self-heal）是受控高级选项，非默认追求

---

### D9：Harness 透明度与可复现性

> 来源：Stop Comparing LLM Agents Without Disclosing the Harness（2026.05）+ How Good Is Your Harness?（2026.05，统计归因实证：切换最佳 Harness 的增益 ≈ 切换最佳 LLM 的增益）+ [Anatomy of Coding Agents](https://arxiv.org/abs/2609.00006)（2026.07，受控纵向方法：原始 8 系统重新 pin 版本后跨季度源码 diff）
> 原则：Harness 配置方差可能超过模型选择方差。未公开 Harness 规格的 Agent 基准测试结果应被视为不完整且可能误导。

#### D9.1 Harness 规格文档

**检查项**：
- [ ] Harness 的核心组件有文档记录（系统提示词、工具定义、中间件/Hooks、子 Agent 架构）
- [ ] Harness 配置变更可追溯（版本控制、变更日志）
- [ ] Harness 组件的作用有量化说明（某条规则/工具对成功率的贡献）

#### D9.2 Harness 可复现性

**检查项**：
- [ ] Harness 配置可从文档重建（非仅存在于运行时内存中）
- [ ] 不同模型在同一 Harness 下的性能差异可独立测量（Harness 方差 vs 模型方差可分解）
- [ ] 基准测试结果附带 Harness 配置说明（非仅报告模型名称和分数）
- [ ] 评估采用 value-aware 视角（不只看 task success，同时报告成本、延迟、超时率、恢复质量——参考 QA→Task Completion Survey）
- [ ] 评估产出可审计的 episode package（每次运行转为含复现日志/失败归因/确定性需求检查/结构化验证报告的证据包，而非仅一个补丁——参考 Runtime Substrate 的 H0–H3 阶梯与 trace-based 评估协议）
- [ ] Harness 质量按显式维度集测量（correctness/quality/efficiency/reliability/robustness/safety/fairness 七维，而非单一 task success rate——参考 Trustworthy LLM Harnesses 测量框架，预印本未同行评审）

#### D9.3 Harness 可迁移性

**检查项**：
- [ ] Harness 组件设计为可剥离（模型更新后可单独移除特定中间件）
- [ ] Harness 核心逻辑不绑定特定模型版本的已知行为模式
- [ ] Harness 在跨模型家族迁移时的性能变化有追踪

**评估标准**：
- 1 星：Harness 配置完全隐式，无法从文档或代码中重建
- 3 星：有基本 Harness 文档但缺少方差分解和可迁移性验证
- 5 星：Harness 规格完整可复现 + 模型/Harness 方差可独立测量 + 跨模型迁移验证 + 可剥离性设计

> **必备/按需分层**：必备 = 版本、环境、输入、验证结果与限制可记录（复现底线）；按需高级 = 方差独立测量、跨模型迁移验证——单模型项目标 N/A；可剥离性设计对长生命周期项目必备、短期项目按需

---

## 评分体系

每个维度 1-5 星：

| 星级 | 含义 |
|------|------|
| ⭐ | 缺失——该维度无任何实践 |
| ⭐⭐ | 初始——有意识但未系统化 |
| ⭐⭐⭐ | 基础——已建立但有关键缺口 |
| ⭐⭐⭐⭐ | 成熟——覆盖良好，有少数提升空间 |
| ⭐⭐⭐⭐⭐ | 领先——行业最佳实践水平 |

### 成熟度等级

| 总体评级 | 条件 | 类比 |
|----------|------|------|
| Level 1：个人基础 | 各维度星级中位数 < 3 | 有马具但缺缰绳 |
| Level 2：团队级 | 各维度星级中位数 ≥ 3 且 < 4 | 骑手可以安全骑行 |
| Level 3：生产级 | 各维度星级中位数 ≥ 4 | 赛马级装备，可控且快速 |

> 区间互斥：中位数恰为 3 → Level 2；恰为 4 → Level 3。有效维度数为偶数时，中位数取中间两值的算术平均（如 8 个维度取第 4、5 名均值，可出现 3.5，按上表归属 Level 2）。

### 评分协议

**N/A（不适用，两级定义）**：
- **检查项 N/A**：单项对项目形态不适用时（如不部署的库 → D7 部署审批项、单模型项目 → 跨模型迁移项），标注理由。检查项 N/A 不单独计算分母——维度星级由评估者对照五星标准、基于**其余适用检查项**综合判断。
- **维度 N/A**：只有**整个维度**对项目形态结构性不适用时才可标（罕见——D9 在单模型项目也只降部分项，不整维 N/A），此时该维度从中位数计算中排除。
- N/A 须谨慎使用——"没做"不等于"不适用"，只有结构性不适用才可标。

**未知（证据不足）**：无法取得证据时标"未知"，按 1 星保守计入并在报告中注明——防止"存在配置文件"被当作"运行时生效"。

**安全红线（覆盖平均）**：D5 安全防护为 1 星、或命中反模式 6（全权限 Agent）且无任何缓解时，**总体评级强制不高于 Level 1**，无论其他维度多高——严重安全缺口不可被高分平均掩盖。

**证据状态标注**：每个维度结论附三态之一——`已验证`（有文件/运行证据 + 观察日期）/ `仅声明`（只有文档说明，未见运行证据）/ `未验证`（评估者推断）。跨维度重复计分处理：同一证据可关联多个维度（如 CI 同时关联 D1/D3/D7），但各维度分别评"执行机制/生命周期/可复现性"各自侧面，报告须注明主归属，避免同一事实被三次当作独立优势。

---

## 常见反模式（评估时重点排查）

> 来源：综合多个权威来源的行业教训

### 反模式 1：软硬失衡

指南和 Agent 分工很完善（软性层），但 Linter/CI/类型检查缺失（硬性层）。
软性 Harness 告诉 Agent "应该做什么"，硬性 Harness 确保即使 Agent 忽略指南也不会出错。
**后者才是生产级 Harness 的关键。**
量化证据（Marmelab 2026.09 普查）：481 个公开 CLAUDE.md 中仅 **4.4%** 的安全规则有真实 control 背书（4-16% 区间）——"散文规则几乎从不被真正执行"；391 个仓库中仅 12 个有 deny 规则、22 个有 PreToolUse 钩子。规则写进文档 ≠ 规则会生效。

### 反模式 2：测试覆盖偏科

认证模块 30+ 测试，核心业务流程 0 测试。涉及资金的操作反而最缺保护。

### 反模式 3：文档仅限人类

架构决策存在于 Confluence/Slack/脑中，Agent 无法访问。仓库外的知识对系统不可见。

### 反模式 4：Harness 静态化

从不审查和更新 Harness 组件。模型更新后，之前必要的中间件变成累赘。不活跃的 skills/agents 持续积累。

### 反模式 5：工具膨胀

给 Agent 越多工具越好？错。Vercel 删除 80% 的工具反而获得更好结果。每个工具都是决策分支，过多的分支导致 Agent 困惑。

### 反模式 6：全权限 Agent

Agent 拥有完整的文件系统访问和命令执行权限，无任何操作需要审批。同时具备致命三要素的全部三项（不可信输入暴露 + 敏感数据访问 + 对外通信能力）。

### 反模式 7：Harness 过度绑定模型版本

为当前模型版本的特定行为构建了大量定制化中间件（如"模型总是忘记 X，所以加一层提醒"）。当模型更新后，这些中间件不仅无效，还可能干扰新模型的自然能力。
**设计 Harness 时要保持可剥离性（rippable）——当模型足够聪明不需要某些逻辑时，应能轻松移除。**

**正向做法**：用模型-harness 共演化划线——"例行支撑"（提示词补丁、格式脚手架）随模型变强可删，"约束治理"（PII/权限/预算）永远不删。删的触发条件是 ratchet 心法："只在强模型让某约束变多余时才删它"，且删改前在 holdout 任务集上跨模型验证。完整操作步骤见 implementation-guide §9「模型升级时的 Harness 审计 SOP」。

### 反模式 8：Harness 不可复现

在基准测试或生产报告中只披露模型名称和分数，不公开 Harness 配置。根据 Binding Constraint Thesis，Harness 配置方差可能超过模型选择方差——两个团队用同一模型可能得到完全相反的排名，只因 Harness 不同。
**公开 Agent 性能数据时，必须附带 Harness 规格说明，否则结果不可信。**

### 反模式 9：Brain-Hands 耦合

将 Harness（推理循环）、Sandbox（代码执行）和 Session（事件日志）耦合在同一个容器中。当容器崩溃时，三者同时丢失——无法恢复、无法审计、无法迁移。而且凭据（Git token、API key）不得不进入沙箱，因为"它们都在同一个环境里"。

**正确做法**：将 Brain、Hands、Session 拆分为独立组件（参考 Anthropic Managed Agents 架构）。Session 外置为持久化事件日志；Harness 无状态化，通过 `wake(sessionId)` 恢复；凭据永不进入沙箱。当容器崩溃时，只是"手"掉了，"大脑"和"记忆"完好。

### 反模式 10：Loop 先行，Harness 缺位

在 Skills 没写好、Sub-agent 验证不靠谱、Connector 没接好的情况下，就急于上自动化 Loop。"让 Agent 自动循环运行"看起来酷，但底层 Harness 不扎实时，Loop 只是加速生产垃圾——一个没有缰绳的马装上自动跑圈程序，跑得更远但更危险。

**正确做法**：先确保单次 Agent 运行的 Harness 质量（D1-D7 至少 3 星），再考虑自动化调度（D8 loop 原语）。Loop 的可靠性上限由底层 Harness 决定。验证"Loop 产出是否正确"的机制，必须比 Loop 本身先就位。

### 反模式 11：审查瓶颈（Amdahl 陷阱）

在生成端大量投入（更强的 Agent、更激进的自动化 Loop），却让人工审查保持手动的、串行的旧流程。Anthropic 内部数据显示，当 80%+ 的代码由 Claude 编写、典型工程师每日合并代码行数约 8 倍增长后（数量指标），**整体吞吐量开始被人工审查环节卡住**——Agent 产出的代码堆积在审查队列里，生成端的加速被审查端的瓶颈完全吞噬（Amdahl's law）。审查失衡有**两种表现**：其一是"审查排队"（上述吞吐瓶颈）；其二是"审查变成批准"（code ratification，参考 Osmani 2026.11）——负荷过大时审查退化为走形式，测试全绿 + 命名合理就批准大 diff，需求、复杂度与风险实际未被判断。

**正确做法**：把审查环节也纳入 Harness 设计——自动化 reviewer（maker/checker 分离）、结构化审查清单、回归 eval 自动门禁。投资比例应随生成端自动化程度同步向审查端倾斜，否则"让 Agent 写得更多"只是把拥堵从写代码挪到了等审查。度量上吞吐指标须配对反向指标（合并 PR 数搭配 revert/返工率），防止单侧指标激励产出膨胀。

### 反模式 12：静态迁移

模型升级或 `/switch` 换模型时，把旧 Harness 整体照搬，不重跑改进循环。LangChain 实测：同一 Harness，Codex 跑 66.5%，Claude Opus 4.6 只有 59.6%（当时模型快照）——"because we didn't run the same Improvement Loop with Claude"。Self-Harness 也证明不同 base model 需要不同的 model-specific harness instructions。

**正确做法**：换模型 = 弱点分布变了，旧 remediation 多半错配。必须重跑 improvement loop，用 core vs profile 切分——core（通用）保留，profile（模型专属）按新模型重调，在 holdout 上跨模型验证后再合并。Gated QD（2026.07）给这个判断提供了学术背书：跨模型迁移的是 diagnose-and-credit loop 本身，而不是任何一个具体 harness——换模型会改变主导病理和对应的最优补丁，但"病理→补丁"的匹配模式会在不同模型家族间复现。

### 反模式 13：决策遗失（Veto Loss）

把"否决"只存在对话上下文里，没有落盘成独立持久状态。会话重启、上下文压缩、换 agent 后，模型把一个**已经被取消/拒绝的动作重新推导出来并执行**。这种失败常被误诊为"危险自主性"或"指令遵循差"，实质是**决策遗失（decision loss）**——veto 从未进入 durable state。

**正确做法**：维护一份独立的否决寄存器（如 `decisions.md`），记下被取消的动作、日期、范围和**理由**。带理由的 veto 能跨会话、跨 agent、跨人存活，可被检索、辩护、与新证据比对；没理由的 veto 一旦环境略变就被推翻。配套要求 replay safety：从检查点恢复执行时不重复副作用，否则"否决了但重放又执行了一遍"同样构成决策遗失。核心心法来自 restart engineering——**Agent 不只忘事实，更会忘否决，负面知识与正面摘要同等重要**。

### 反模式 14：Bitter Lesson 误读（以为模型够强就不需要 harness）

把 Sutton 的 Bitter Lesson 推过头，得出"等模型够强，harness 可以整体拆掉"，于是当前不投资 harness 工程化。这个推论混淆了两件不同的事：手写的**例行支撑**（提示词补丁、格式脚手架）确实是技术债、模型升级后多余（见反模式 7）；但**约束治理**（权限、PII、合规、可观测性、状态恢复）不会随模型变强而消失——它不是"模型还不够聪明"，而是"不该交给概率性判断"。

**正确做法**：用 Component Taxonomy Survey 的共演化划线——例行支撑随模型变强可删（且做成可剥离），约束治理永远留在外部代码。From Prompts to Contracts 量化证明约束性验证只能由代码所有（utility 120/120），提示词和外挂 guardrail 都做不到；Binding Constraint Thesis、Harness-Bench 和密歇根大学统计归因三处独立实证都显示 harness 配置方差 ≥ 模型选择方差。真正的趋势不是 harness 消亡，而是从"人类手写"转向"模型自改写"（Self-Harness / AHE / HASE / Gated QD）。完整辩证见 article §12「Harness 会消失吗」。**"现在就不做 harness 工程化"是最大的战略误判**——它赌的是"模型会强到不需要任何外部约束"，与所有现有证据相悖。

### 反模式 15：自进化自证循环（Self-Certified Evolution）

自进化 harness 的"改进"在**搜索所用的同一基准上搜索并报告**，既不与对齐反馈/推理预算的 test-time scaling 基线比较，也不在 held-out 任务上验证泛化。Rethinking（2026.07）实测：这种协议下报告的增益可能是"更多搜索"和基准过拟合的混合物——Terminal-Bench 2.1 上自动 harness 进化不稳定优于简单搜索基线；Bad Genius（2026.09）进一步证明连 task holdout 都不够——只换语义任务不换基准协议，作弊 harness 仍能走协议捷径得分。

**正确做法**：自进化的每一笔"记功"过三道关：**预算对齐基线**（同等反馈与推理预算下的任务级搜索基线）、**held-out 泛化**（搜索基准之外的任务集）、**协议反作弊确认**（反事实引导 / 预注册 fresh-world）。Gated QD 的"确定性记功 + sealed test"是可参照的先行实现；评估一门会自己改自己的系统，评估协议必须比被评估对象更硬。

### 反模式 16：人类优先接口（Human-Only Interfaces）

基础设施与文档只对人友好：安装流程是"去这个 URL 点这个按钮"，文档没有 Agent 可消费的版本，服务无 CLI/API/结构化输出。Karpathy 的原话："为什么文档还在教我去哪个 URL、点哪个按钮？我要的是一段能直接贴给 agent 的文字。"当真实用户是"人类的 agent"时，纯人类接口等于把 agent 挡在门外——菜单点选式配置、截图式文档、必须走浏览器会话的操作流都在此列。

**正确做法**：每个能力提供 agent-native 版本——Markdown 文档、CLI、API、MCP server、结构化日志、机器可读 schema、可复制粘贴的 agent instructions、headless 安装。Karpathy 的抽象是 sensors（把世界状态变成数字信息）与 actuators（让 Agent 能改变世界）：按"Agent 能否直接读取/操作"逐项审视产品与工作流，缺口即改造清单。（对应 article §9 错误五）

### 反模式 17：批内授权继承（Authority Borrowing in Batches）

事件驱动的 Agent 为省推理开销常把多条环境事件合并成一批评估（DeepSeek Super-agent 的 Eval 批处理即如此），若实现为"以批内最强事件的授权为整批授权"，被准入门控拒绝的消息、其他发送者的消息仍会以批量上下文的名义进入评估窗口——恶意或无关旁注可能搭上合法请求的便车，扩大被授权的工作范围。第二视角评审（对照 dsh-superagent-prototype 源码）将其标注为**基于数据流的风险推测、尚未证明可利用**，但这正说明它容易被"批处理只是性能优化"的直觉掩盖。

**正确做法**：批量是调度优化，不是授权单位——逐条事件独立判定授权强度，被拒消息不得进入被接受事件的评估上下文（或显式降级为"仅背景、不构成授权"）；对混合发送者、明确请求加旁注的样本做对抗测试，检查最终工单范围与执行结果是否越权。配套认知：授权对象（Mandate）逐条可审计，是批内隔离的前提。

### 反模式 18：反馈时机与模型能力错配（Sensor Timing Mismatch）

把为弱模型设计的"开发全程 harness"无差别套用在能一次通过的前沿模型上：Naresh Jain 实测（2026.10）GPT-6 Sol 全程 harness 多花约 10.5 倍 token、约 6.6 倍成本、约 3 倍时间，验收结果零差异——纯开销；反过来，把前沿模型的"裸跑 + 终门验收"直接搬给成本优化模型，10 次运行 7 次交付不可接受产物（3/10 基线）。错配的根源是把"harness 有没有用"（Binding Constraint Thesis 层面成立）与"harness 全程值不值"（条件性问题）混为一谈。

**正确做法**：独立验收门恒在（"是否需要独立验证"不随模型能力变），反馈节奏按具体任务的实测基线通过率、失败成本与反馈成本实验后选择（而非模型宣传档位）——实验提出的"强模型裸跑+终门、弱模型全程"是待验证的候选方案，不是可直接照抄的档位规则；任务或模型变化后复测收益。

---

## 评估输出模板

对每个项目的评估应输出以下格式：

```markdown
## [项目名] Harness 成熟度评估

**评估日期**: YYYY-MM-DD
**项目路径**: /path/to/project

### 评分总览

| 维度 | 评级 | 关键差距 |
|------|------|----------|
| D1 四象限覆盖度 | ⭐xN | ... |
| D2 上下文工程 | ⭐xN | ... |
| D3 架构约束 | ⭐xN | ... |
| D4 熵管理 | ⭐xN | ... |
| D5 安全防护 | ⭐xN | ... |
| D6 测试与验证 | ⭐xN | ... |
| D7 CI/CD | ⭐xN | ... |
| D8 工作流编排 | ⭐xN | ... |
| D9 透明度与可复现 | ⭐xN | ... |

### 成熟度趋势（与上次评估对比）

| 维度 | 上次评级 | 本次评级 | 趋势 |
|------|----------|----------|------|
| D1 四象限 | ⭐xN | ⭐xN | ↑/→/↓ |
| D2 上下文 | ⭐xN | ⭐xN | ↑/→/↓ |
| D3 架构 | ⭐xN | ⭐xN | ↑/→/↓ |
| D4 熵管理 | ⭐xN | ⭐xN | ↑/→/↓ |
| D5 安全 | ⭐xN | ⭐xN | ↑/→/↓ |
| D6 测试 | ⭐xN | ⭐xN | ↑/→/↓ |
| D7 CI/CD | ⭐xN | ⭐xN | ↑/→/↓ |
| D8 工作流 | ⭐xN | ⭐xN | ↑/→/↓ |
| D9 透明度 | ⭐xN | ⭐xN | ↑/→/↓ |

### 核心优势（Top 3）

1. ...
2. ...
3. ...

### 关键改进（Top 3，按优先级排序）

1. [P0] ... —— 理由：...
2. [P1] ... —— 理由：...
3. [P2] ... —— 理由：...

### 总体评级

Level X（一句话总结）
```

---

## 评分校准示例

三个虚构示例项目（**示例，非真实**），用于校准评估尺度——重点演示 N/A、证据状态与安全红线的用法，而非示范"满分答案"。

### 示例 A：个人只读研究 Agent

场景：本地文献问答，工具只读、无部署、无对外通信。

| 维度 | 评分 | 关键理由 |
|------|------|----------|
| D1 | 3 星 | 有 CLAUDE.md 但内容笼统 |
| D2 | 3 星 | 系统提示词可用，无跨会话记忆 |
| D3 | 2 星 | 无类型检查，仅基础 lint |
| D4 | 2 星 | 无升级审计、无 ratchet 记录 |
| D5 | 4 星 | 只读 + 无对外通信通道，三要素天然缺二；但"工具只读"须有运行证据（hooks 限制 + 权限白名单实测），仅口头声明记"仅声明" |
| D6 | 3 星 | 有测试但无 capability/regression 区分 |
| D7 | N/A | 不部署的本地工具——**检查项**（部署审批）不适用，非整维 N/A；CI/部署侧结构性缺失，按其余检查项计 2 星 |
| D8 | 2 星 | 单轮问答无编排需求——按需低分正常，非缺陷 |
| D9 | 2 星 | harness 配置有文档但无方差测量 |

计算过程：有效维度 9 个（无整维 N/A），分数 [3,3,2,2,4,3,2,2,2] 排序 → [2,2,2,2,3,3,3,3,4]，中位数 = 3 → **Level 2**。要点：此类项目的价值集中在 D1（指南质量）与 D5（权限边界），不追求全维度拉满。

### 示例 B：小团队编码 Agent

场景：Claude Code + CLAUDE.md + 危险命令 hooks + CI，5 人团队。

| 维度 | 评分 | 关键理由 |
|------|------|----------|
| D1 | 3 星 | 有多层指南但无交叉验证机制 |
| D2 | 3 星 | CLAUDE.md 分层加载，无长期记忆 |
| D3 | 3 星 | lint + 类型检查已配 |
| D4 | 3 星 | 有 ratchet 习惯，无升级审计 SOP |
| D5 | 3 星 | hooks 拦截已有（已验证），但无 Policy as Code、无环境区分 |
| D6 | 3 星 | 测试有覆盖，harness 自身无 eval |
| D7 | 4 星 | CI 全覆盖——**注意**：此证据同时关联 D1/D3/D7，报告注明主归属 D7，D1/D3 各评自己侧面 |
| D8 | 3 星 | 基本工作流，无自动化调度（按需） |
| D9 | 2 星 | harness 配置在版本控制但无规格文档 |

计算过程：有效维度 9 个，分数 [3,3,3,3,3,3,4,3,2] 排序 → [2,3,3,3,3,3,3,3,4]，中位数 = 3 → **Level 2**。典型陷阱：CI 一项事实被三个维度重复计分——按评分协议写明主归属，避免虚高。

### 示例 C：生产级支付对账 Agent

场景：写数据库 + 发付款通知 + 处理外部商户单据——**致命三要素三项齐备**。

| 维度 | 评分 | 关键理由 |
|------|------|----------|
| D5 | 2 星 → 触发红线 | 若仅靠提示词约束 + 全权限工具：无论其他维度多少星，**总评强制 Level 1** |
| D5 | 4 星 → 解除红线 | Policy as Code + 对外通信人工审批 + 双向 guard 测试后，才允许评 Level 2+ |

总评**取决于 D5**。要点：这就是"安全红线覆盖平均"的含义——示例 C 的 D1/D6 可以全是 5 星，红线期间总评依然是 Level 1；三要素齐备的项目，安全维度是总评的先决条件而非九分之一。

---

## 更新日志

| 日期 | 版本 | 变更内容 |
|------|------|----------|
| 2026-06-02 | v1.0 | 初始版本，基于 7 个权威来源建立 8 维度评估框架 |
| 2026-06-08 | v1.1 | 补充 CAR Framework、OpenReview Survey、LangChain Blog 3 个权威来源（共 10 个）；D4 熵管理增加 Meta-Harness 检查项；新增反模式 7（Harness 过度绑定模型版本）；评估模板增加成熟度趋势对比 |
| 2026-06-12 | v1.2 | 补充 7 个权威来源（共 17 个）：Code as Agent Harness、NLAH、AHE、Binding Constraint Thesis、AutoHarness、Anatomy of Agent Harness、State of Agent Engineering；新增 D9 维度（Harness 透明度与可复现性）；新增反模式 8（Harness 不可复现）；D4 熵管理增加 AHE 自动进化相关检查项 |
| 2026-06-15 | v1.3 | 补充 8 个权威来源（共 25 个）：Self-Harness、SIA、What Makes a Harness、How Much Heavy Lifting、Scaling Managed Agents、Building a C Compiler、Böckeler 控制系统框架、OpenAI Agent Improvement Loop；D4 熵管理增加自愈闭环和权重联合优化检查项；新增反模式 9（Brain-Hands 耦合） |
| 2026-06-15 | v1.4 | 补充 5 个权威来源（共 30 个）：Loop Engineering（Addy Osmani）、Structured Graph Harness、LOOP Skill Engine、Continual Harness、Hacker-Fixer Loop；D8 工作流编排扩展为 Loop 时代：增加自动化调度、跨会话状态、目标驱动、确定性回放四项检查项；新增反模式 10（Loop 先行，Harness 缺位） |
| 2026-06-22 | v1.5 | 补充 7 个权威来源（共 37 个）：Effective harnesses for long-running agents、Demystifying evals、When AI builds itself、Better Harness（LangChain）、How Good Is Your Harness?、Memory-Aware SE Agents、Survey on Agent Skills；修复 Anthropic harness-design 失效链接（→ harness-design-long-running-apps）；D2 增加情景/时序记忆检查项、D6 增加 capability/regression evals 与 holdout set 两项检查项、D8 增加跨 context window 状态交接检查项、D9 来源补充 How Good Is Your Harness 统计归因证据；新增反模式 11（审查瓶颈/Amdahl 陷阱） |
| 2026-06-26 | v1.6 | 补充 11 个权威来源（共 48 个）：Harness-MU、Categorical Architecture、Algorithm Discovery Harness、Physical AI Harness、Post-Training Interplay（5 篇学术论文）；Tuning Deep Agents (Harness Profiles)、Custom Agent Harness (Middleware)、Deep Agents Deploy、Deep Agents v0.6、Bayer AG PRINCE（6 篇行业文章）；D5 安全防护新增多用户治理检查项；D8 工作流编排新增模型专属 Harness 配置 + 可组合中间件两项检查项；D5/D8 五星标准同步扩展 |
| 2026-06-29 | v1.7 | 补充 3 个权威来源（共 51 个）：ETCLOVG Survey（OpenReview/TMLR）、QA→Task Completion Survey（华为）、Osmani Agent Harness Engineering 博客；D1.1 新增 AGENTS.md 精简 + ratchet 检查项、D4 新增错误→规则 ratchet 检查项、D9.2 新增 value-aware evaluation 检查项 |
| 2026-07-06 | v1.8 | 补充 3 个权威来源（共 54 个）：MUSE 多模态统一 Harness（arXiv）、Is Grep All You Need 检索场景实证（PwC）、Bitter Lesson of Agent Harnesses 极简主义心法（browser-use）；D8 工作流编排新增 agent 自补缺失工具（self-heal loop）检查项 |
| 2026-07-13 | v1.9 | 补充 6 个权威来源（共 60 个）：AI Harness Engineering 运行时基座 + H0–H3 阶梯（arXiv）、Agent Harness 六元组完整性 Survey（Preprints）、Harness 组件分类/共演化 Survey（Preprints）3 篇学术论文；Nemotron 3 Ultra Playbook、Middleware 定制、Your Harness Your Memory（LangChain）3 篇行业文章；D2 新增记忆所有权/可移植性、D4 新增共演化（例行迁入/治理留存）、D5 新增确定性合规（PII/HIPAA）、D8 新增 core vs profile 切分、D9 新增 trace-based episode package 五项检查项 |
| 2026-07-14 | v1.10 | D8 工作流编排新增维护循环内容外部化检查项（循环跑什么写成文件、运行时可优化，非硬编码在调度配置或每次手写——参考 Claude Code `loop.md`：Skills+State+Automations 三个原语交汇落点，与 implementation-guide 第四层对齐） |
| 2026-07-15 | v1.11 | 新增「模型升级审计」主题（针对 GLM5.2/DeepSeek V4 + `/switch` 场景）：D4 熵管理新增 2 条检查项（升级审计 SOP + 跨模型 holdout/可证伪合约）；反模式 7 补正向做法；新增反模式 12（静态迁移）。与 implementation-guide §9 升级审计 SOP、article 原则四可剥离性论述联动 |
| 2026-07-15 | v1.12 | 文档质量清理：合并 article 内部冗余（6.8↔5.10、7.5↔6.12、原则四↔4.4）；为 5.15/5.7/6.11 基准表加研究快照版本说明、Better Harness 去 Sonnet 4.6 版本号、反模式 12 加快照注；补 Lilian Weng《Harness Engineering for Self-Improvement》(2026.07)；结语补"护城河"论点的能力区间限定；P2 收尾：精简 D8 一处 ratchet 冗余引用、补 ToFu/HarnessX 两篇 2026.06-07 新文献 |
| 2026-07-21 | v1.13 | 新增「restart engineering / durable state」主题：补 2 篇深度解读（Philipp Schmid《The importance of Agent Harness in 2026》OS 类比 + context durability + harness-is-the-dataset；Markdown Engineering《The Harness Is the Product》restart engineering + veto durability + replay safety + Georgiev 16→2 案例）；D2.3 新增负面知识持久化检查项、D8 新增 replay safety + 显式 stop conditions 两项检查项；新增反模式 13（决策遗失/Veto Loss）。与 article §2.4/§6.15/§12/结语、implementation-guide「状态可恢复」全局原则联动 |
| 2026-07-27 | v1.14 | 补 7 篇 arXiv/AIware 权威来源（共 70 个）：Self-Evolving Gated QD、Harness Handbook、Prompts to Contracts、Agentic AI Coding Tools 实证、Harness-Bench、HASE、Better Harnesses Smaller Models；D1.1 新增 AGENTS.md 互操作标准检查项、D4 新增 credited improvement 确定性记功 + behavior localization 两项检查项、D4 来源行补 Gated QD、D5.3 新增 composition boundary 契约验证检查项；反模式 12（静态迁移）补 Gated QD 学术背书；新增反模式 14（Bitter Lesson 误读）正面回应"模型变强不再需要 harness"的常见误判。article 新增 §12「Harness 会消失吗：Bitter Lesson 与共演化」辩证小节，与 §3.5/§5.18/§6.16/§7.5 联动 |
| 2026-08-04 | v1.15 | 补 1 篇 arXiv 权威来源（共 71 个）：HARBOR——把机器人 RL 工程流水线（建任务/奖励/调参）建模为 harness 问题，专门化 Agent + 标准化命令/artifact/可执行 gate + 去中心化并行试验与跨运行经验学习；6 基准 16 任务含 sim-to-real，补齐具身 Harness 的"研发时"端口（与 Physical AI Harness 的"运行时"端口互补）。article §12 具身 Harness 小节新增 HARBOR 段落 + "运行时/研发时"两端光谱综述。另：本轮检索到 2 个 OpenReview 候选（Towards Long-Horizon Agents、LLM-as-Code），因 API 返回空 + 页面验证墙无法验证真实性，按宁缺毋滥未纳入 |
| 2026-08-21 | v1.16 | 补 5 个权威来源（共 76 个）：Recursive Self-Improving Agents 综述（五级分级 + 五组件耦合框架）、SemaClaw 个人 Agent Harness（2 篇学术论文）；Agent Observability Needs Feedback、Improving Agents is a Data Mining Problem（HE→FT→HE 三明治）、3 Years of Graph Engineering（3 篇 LangChain）。D4 新增改进分层决策 + 自改进分级路线图两项检查项、D6 新增 trace 反馈关联存储 + 多类反馈源两项检查项、D8 新增编排形态按可预知度选择检查项；D4/D6/D8 来源行与五星标准同步扩展。article 新增 §3.7 Graph Engineering 决策边界、§5.19 SemaClaw、§6.17 可观测性与反馈闭环、§6.18 三明治策略，§3.5 补自改进综述、§12 补 harness 溶解论引用；implementation-guide §9 补"失败→eval 反馈闭环"（含指南同步） |
| 2026-09-23 | v1.17 | 补 7 个权威来源（共 83 个），主题"自进化再评估 + 编排范式"：Rethinking Harness Evolution（预算对齐基线 + held-out 泛化双重协议，Terminal-Bench 2.1 负面结果）、Bad Genius（协议层作弊与反事实引导）、ModularRSI、HarnessDev、Externalization in LLM Agents（5 篇学术论文）；OpenAI Symphony（harness engineering 官方下一步：管理 work 而非 agents，SPEC.md 优先，26.9k stars）、Faros Harness Guide（211 任务开放模型均等器 + session-to-PR 度量，2 篇行业文章）。D4 新增自进化增益公平归因检查项、D6 新增评估协议防基准作弊 + session-to-PR 关联度量两项检查项；新增反模式 15（自进化自证循环）。article 新增 §3.5 再评估危机段、§3.6 Symphony 小节、§5.20 Faros 案例、§6.19 session-to-PR 度量、§2.3 补 Externalization、§12 补三层防作弊协议；implementation-guide 同步度量与公平评估（含指南同步） |
| 2026-10-22 | v1.18 | 补 4 个权威来源（共 86 个），主题"实证定论 + 安全四层 + 领域普查"：Anatomy of Coding Agents（Barbaste，83 页——11 生产 harness + Omnigent 源码解剖，400 万行零框架导入零向量检索，SKILL.md 超 MCP，行为策略 prompt 散文→配置，"工具到平台"定论）、Trustworthy agents in practice（Anthropic——model/harness/tools/environment 四层组件模型 + Plan Mode 策略层监督 + prompt injection 多层防御）、State of AI Harness Engineering 2026（Marmelab——246 仓库普查：60% harness 无 test/eval、25% AI 签名、负结果集 reviewer -8pp/>4 交接失败/预写规则少拦 20.1pp、Vercel 砍工具正结果）、Model Hardware Standard（Anthropic——具身 Harness 标准协议层，Physical AI 运行时 + HARBOR 研发时之外的第三端口）。D5 新增四层组件纵深防御检查项并扩 5 星、D6 新增 harness 自身双向 guard 测试 + control expiry condition 两项检查项并扩 5 星、D5/D6/D9 来源行补充；反模式 1（软硬失衡）补 Marmelab 4.4% 量化证据。article 新增 §5.21 Marmelab 普查案例、§7.6 多层防御、§12 源码解剖定论小节、§12 具身 Harness 补 MHS 段（含指南同步） |
| 2026-10-30 | v1.19 | 补 5 个权威来源（共 91 个），主题"Karpathy 话语体系接入"：Sequoia Ascent 2026（agentic engineering 大众化命名，职责清单与 harness 工程逐项重合 + agent-native sensors/actuators + 2025-12 agentic 拐点一手观察）、2025 LLM Year in Review（Claude Code "顺序优先级"：localhost 赢在已启动电脑/context/data/secrets/低延迟而非算力位置；LLM 应用层四职责 context engineering 居首）、Verifiability（可指定/可验证框架 + resettable/efficient/rewardable 三条件）、Animals vs. Ghosts 与 The space of minds（jagged intelligence 理论底座）。D6 来源行补 Verifiability 并新增"验证环境可练习三条件"检查项；新增反模式 16（人类优先接口）。article §3.2 补 Karpathy 术语源与 Cursor 四职责、§3.3 补 agentic engineering 命名段、新增 §5.22 Karpathy 案例、§9 错误五扩 agent-native 清单、参考资源 +5（无 PRACTICE 级新组件，指南不同步） |
| 2026-10-30 | v1.20 | 外部审查修复轮（Codex 全库分析，5 个 P0 硬错误逐项对照原始来源核实后修正）：① 致命三要素第三项由"能修改状态"更正为"具备对外通信能力"（对照 Willison 原文——旧表述会漏掉纯外传攻击路径；article §7.1/§7.2、D5 原则行与 D5.1 检查项、guide §10、反模式 6 同步）；② guide §7 Hook 阻断说明更正为双路径（exit 2 是官方 Blocking error，与 exit 0+JSON deny 等效，修正正文与附录 A 的矛盾）；③ guide §7 "每次必中"限定为"匹配到即确定性拦截"并注明示例正则是演示非防线；④ guide §9 失败案例改入回归集、与 holdout 隔离（修复同节自相矛盾）；⑤ README 数据同步（22 案例/v1.19→v1.20 时点/91 来源/16 反模式）。另修 article §5.21 引用号（6.11→6.13）、§5.17 引用指向（→5.11）、§六/§八标题数字失实。P1/P2 结构性建议（D 维度五星分层、评分协议、校准案例）留待专项处理 |
| 2026-10-30 | v1.21 | Codex 审查 P1 结构性改进轮：① D1/D2/D4/D7/D8/D9 六维五星标准增"必备/按需高级"分层注记（修复"研究能力全集化"——多 Agent 审查/记忆系统/自愈闭环/自动部署/回放/跨模型验证等改为按需高级并注明适用条件，Marmelab reviewer -8pp 为多 Agent 非硬条件的证据）；② 新增评分协议（N/A 不计中位数分母、未知按 1 星保守计入、安全红线：D5 1 星或反模式 6 无缓解时总评强制 Level 1、证据三态标注、跨维度重复计分注明主归属）；③ 新增评分校准示例三个（个人只读研究 Agent/小团队编码 Agent/生产支付对账 Agent——演示 N/A、计分陷阱与安全红线覆盖平均）；④ Rethinking 补 v4（2026.10）正结果边界：长时程游戏为自进化利好区（ARC-AGI-3 +80%/EdgeBench +11%），article §3.5/§12/参考资源、principles 来源表、guide §9 五处同步；⑤ article §12 源码解剖结论限定样本范围（11 系统实证 ≠ 全领域定律）；⑥ principles 增结构导航、README 增术语对照表 |
| 2026-11-01 | v1.22 | 补 1 个权威来源（共 92 个），主题"多任务组织级 Harness + 事件驱动授权"：DeepSeek《Super-agent 设计探索》（2026.10，首个公开多任务/团队级设计：双层架构/事件驱动/存算分离/Watchdog）+ 开源原型 dsh-superagent-prototype（15K 源码+15K 测试，经源码级分析 + Codex 第二视角评审双重核实：单进程无 Watchdog/端侧 daemon，主动工作默认 observe 只记录不执行）。D2.2 新增 Inline & Spill 窗口当 Cache + 引用图链式召回（游标白名单）两项检查项、D5.2 新增 Mandate 可再验证授权对象检查项（含通知式撤权/批内混合两条限定）、D8 新增双层调度（消融数据）/人优先并发/两级事件过滤三项检查项并扩五星；新增反模式 17（批内授权继承）。article 新增 §5.23 Super-agent 案例、§7.7 事件驱动授权准入、参考资源 +1（含指南同步） |
| 2026-11-08 | v1.23 | 补 5 个权威来源（共 97 个），主题"harness 价值的条件边界 + proactive 上下文 + 配置杠杆全景"：Are Harnesses Worth the Cost?（Naresh Jain/martinfowler——前沿模型全程 harness 约 10.5x token 零增益 vs 成本模型 3/10→10/10 且样本内省 33%，executable attention，sensor 时机假设）、Skill Issue（HumanLayer——sub-agent context firewall、CLI 优先经验建议、bias towards shipping、TB2 Opus 4.6 换 harness #33→#5）、Trustworthy LLM Harnesses（Preprints 2026.10——七维测量框架，预印本）、Sustaining Long-Horizon Execution（OpenReview 2026.10.27——四层功能分层，摘要级核验）、New context engineering challenges（Chase/LangChain 2026.11——proactive/always-on/goal-oriented agents 在已有实践之上带来一组无通用解新挑战：通知校准/记忆智能层/异步通知/目标跟踪/对齐防作弊/浏览器）。D1.1 新增工具暴露上下文效率检查项、D6 新增反馈节奏按实测基线选择检查项、D8 新增主动式 Agent 上下文策略检查项、D9.2 新增七维测量检查项；新增反模式 18（反馈时机与模型能力错配）。article 新增 §5.24 条件性实证案例、§6.3 CLI>MCP、§6.4 context firewall、§6.11 TB2 排名证据、§12 proactive 上下文工程小节、参考资源 +5；implementation-guide 全局原则新增"工具暴露按上下文效率取舍"（含指南同步）；README 数字同步。另：Agent Harness Engineering: A Survey（OpenReview q83ujTeMdE）与已收录 ETCLOVG Survey 同名无法区分、An Architectural Survey（Sgc3w5jF3z）增量有限且页面验证墙无法实质验证，按宁缺毋滥未纳入。**同日修订**：Codex 第二视角评审发现 Sol token 倍数误写（6.6x→约 10.5x，6.6x 实为成本倍数）、单项实验外推为通用档位规则、MCP 注入与 context firewall 缺实现限定、Chase 概括过度（摘要/卸载系其列出的已有实践），已逐项修正；既存 6.20 悬空引用改指 §7.7 |
| 2026-11-08 | v1.24 | Codex 全库审查修复轮（19 项，逐项对照原始来源核实后修正）：① **四象限分类重建**——按 Böckeler 原文执行方式划分（computational/inferential），AGENTS.md/编码规范/Skills 从"确定性前馈"更正为**推断型前馈**，D1.1-D1.4 四小节重命名（推断型前馈/计算型反馈/推断型前馈-系统提示词/推断型反馈），D1.3 按需观察提示移至 D1.4 并重写五星（独立第二视角为等价证据，多 Agent 非硬条件）；② **评分协议修复**——Level 区间改互斥（<3 / ≥3且<4 / ≥4，偶数维度中位数取中间两值均值）；N/A 拆为检查项/维度两级；校准示例 A/B 补全 9 维评分与计算过程（可复算）；③ **来源归属更正**——Write/Select/Compress/Isolate 四策略更正为 LangChain《Context Engineering》（2025.07）分类并新增来源条目，Write 定义更正为"保存到窗口之外"（scratchpad/记忆/状态），article §3.2 表格、§3.2 三代叙事"单轮局限性"矛盾、D2.1 检查项同步；④ **事实口径修正**——Anthropic"人均产出 8 倍"更正为"每日合并代码行数约 8 倍（数量指标，原文提醒高估生产力）"+97% 补边界；OpenAI"人类审查者被取代"收窄为原文口径；article 开头"没人碰过编辑器"收窄；⑤ **来源表补链接**（四象限框架、Lethal Trifecta）+ 新增 LangChain《Context Engineering》条目，统一统计口径为表格条目数（100 条目）；⑥ D5 五星多用户治理改为条件必备；D2.3"0/10 缺失"表述反转修正；article 三处章节误引更正（八→十二、九→八原则四）。guide 同步：lint 示例脚本退出码处理、`.env` deny 补 OS 级边界说明、Bash 通配符规则按官方文档更正（`cmd *` 含裸命令、`cmd*` 前缀匹配、`:*` 等价）、Desktop 调度表补配置来源与核验日期、附录 A additionalContext 归位 hookSpecificOutput + SessionStart/SessionEnd matcher 分列（含指南同步 + README 同步） |
| 2026-11-08 | v1.25 | 部分收录 Addy Osmani《Your coding agents made you a manager》（X Article，2026.11.08，经 Codex 评估 + X 浏览器提取 + 英文镜像交叉核对）：参考资源深度解读 +1、来源表 +1（共 101 条目）；article §11 补"编排税 / code ratification / 管理类比边界"三观察段（个人经验视角限定）；D8 新增并行代理注意力预算观察检查项（按需，注明非定量规律）；反模式 11 补"审查排队 vs 审查变成批准"两种表现 + 吞吐指标配对反向指标（顺带修正该处遗留的"人均产出 8 倍"旧口径）。明确不新增案例小节、不新建反模式 19——与既有审查瓶颈/认知投降重叠，"两次纠正"等启发式未固化为硬规则（含指南无改动，README 同步） |

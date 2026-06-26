# Harness 工程原则评估框架

> 基于权威来源的行业共识，用于评估 AI Agent 开发环境的成熟度。
> 模型是商品，Harness 是护城河。—— 2026 行业共识

**版本**: v1.6
**更新日期**: 2026-06-26
**用途**: 审计任意项目的 Agent Harness 成熟度，输出评分 + 改进建议

---

## 权威来源

| 来源 | 机构 | 核心贡献 |
|------|------|----------|
| [Agent Systems with Harness Engineering](https://github.com/RUCAIBox/awesome-agent-harness) | RUCAIBox（人大） | 502 篇参考文献系统综述，确立 Harness 为一等公民 |
| [Harness Engineering: Leveraging Codex](https://openai.com/index/harness-engineering/) | OpenAI（2026.02） | 三大支柱框架，100 万行零手写代码实践 |
| [Harness Design for Long-Running Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) | Anthropic（2026.03） | 三 Agent 架构（Planner/Generator/Evaluator） |
| Fowler + Böckeler 四象限框架 | ThoughtWorks | 确定性/非确定性 × 前馈/反馈 2x2 模型 |
| [My AI Adoption Journey](https://hashicorp.com/blog/mitchell-hashimoto-my-ai-adoption-journey) | Mitchell Hashimoto | "每当 Agent 犯错，改变系统让错误不可能再发生" |
| Lethal Trifecta / Rule of Two | Simon Willison / Meta AI | Agent 安全三要素与二选一规则 |
| [2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf) | Anthropic | 上下文工程四大策略（Write/Select/Compress/Isolate） |
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
| [When AI builds itself](https://www.anthropic.com/institute/recursive-self-improvement) | Anthropic Institute（2026.04） | 内部一手数据：80%+ 代码 Claude 写、人均产出 8 倍增长；提出"人类审查成为新瓶颈"（Amdahl's law） |
| [Better Harness: A Recipe for Harness Hill-Climbing with Evals](https://www.langchain.com/blog/better-harness-a-recipe-for-harness-hill-climbing-with-evals) | LangChain（2026.04） | 6 步 Harness 迭代 recipe，holdout set 防过拟合，跨模型（Sonnet 4.6/GLM-5）验证泛化 |
| [How Good Is Your Harness? Statistical Evaluation of Coding Harnesses](https://openreview.net/forum?id=QI8z3skBwt) | 密歇根大学 / CTB@ICML 2026（2026.05） | 统计归因量化 Harness 与 LLM 贡献比：切换最佳 Harness 的增益 ≈ 切换最佳 LLM 的增益 |
| [Memory-Aware Software Engineering Agents](https://openreview.net/forum?id=WeXF1A3xY8) | KI 2026 AI4SE Workshop（2026.05） | 对 10 个生产级 SE harness 的 feature 级分析，发现 0/10 原生提供 episodic memory 或 temporal versioning |
| [A Survey on Agent Skills](https://openreview.net/forum?id=S184SW2SPX) | ACL ARR 2026（2026.05，审稿中） | 将 agent skills 形式化为"外化的过程性知识"，给出五维 taxonomy |
| [Harness-MU](https://arxiv.org/abs/2606.21856) | 多机构联合（2026.06） | 首个模型无关多用户 Agent 安全治理框架，执行钩子强制执行权限边界，Muses-Bench 零隐私泄露 |
| [Harness Engineering as Categorical Architecture](https://arxiv.org/abs/2605.12239) | 独立学者（2026.05） | 用范畴论三元组为 Harness 建立形式化数学基础，编译器函子实现跨运行时 Harness 编译 |
| [Effective Harness Engineering for Algorithm Discovery](https://arxiv.org/abs/2605.15221) | NEC-NTT 联合（2026.05） | 算法发现场景的 Harness 设计：少而深优于多而浅，更强模型产生更多评估作弊需检测 |
| [Harness Engineering for Physical AI](https://arxiv.org/abs/2606.09416) | 韩国研究团队（2026.06） | 将机器人中间件形式化为 Physical AI Harness 层，提出 Projection/Isolation/Transfer 三功能 |
| [The Interplay of Harness Design and Post-Training](https://arxiv.org/abs/2606.25447) | 韩国研究团队（2026.06） | Harness 作为可控设计维度的后训练研究，harness-aware post-training 提升 OOD 鲁棒性 |
| [Tuning Deep Agents to Work Well with Different Models](https://www.langchain.com/blog/tuning-deep-agents-different-models) | LangChain（2026.04） | Harness Profiles：按模型维护专属配置覆盖层，tau2-bench +10-20pp 量化证据 |
| [How to Build a Custom Agent Harness](https://www.langchain.com/blog/how-to-build-a-custom-agent-harness) | LangChain（2026.06） | 中间件式 Harness 扩展范式 + Task-Harness Fit 概念，`create_agent` 最小化原语 |
| [Deep Agents Deploy](https://www.langchain.com/blog/deep-agents-deploy-an-open-alternative-to-claude-managed-agents) | LangChain（2026.04） | 开源模型无关 Agent Harness，集成 MCP/A2A/Agent Protocol，单命令部署 |
| [Deep Agents v0.6](https://www.langchain.com/blog/deep-agents-0-6) | LangChain（2026.05） | 开源模型专属 Harness Profiles，20x+ 成本优势下缩小与前沿模型差距 |
| [Building Reliable Agentic AI Systems](https://martinfowler.com/articles/reliable-llm-bayer.html) | Martin Fowler / ThoughtWorks（2026.06） | Bayer AG PRINCE 制药研发平台案例，上下文工程 + Harness 工程双重透镜 |

---

## 评估维度

### D1：Fowler 四象限覆盖度

> 来源：Martin Fowler + Birgitta Böckeler（ThoughtWorks）
> 原则：生产级 Harness 需要四象限全部覆盖——纵深防御。

#### D1.1 确定性前馈（指南）

Agent 开始工作前就将其引向正确方向。

**检查项**：
- [ ] 存在项目级 CLAUDE.md / AGENTS.md / .cursorrules
- [ ] 编码规范明确（缩进、引号、命名约定）
- [ ] 分层架构规则有文档说明（Controller/Service/Model 各自职责）
- [ ] 领域术语有统一定义（CONTEXT.md 或术语表）
- [ ] Git 提交规范有明确定义
- [ ] 工作流路径有清晰说明（新功能 / Bug 修复各走什么流程）

**评估标准**：
- 1 星：无任何项目级指南文件
- 3 星：有 CLAUDE.md 但内容笼统
- 5 星：多层指南（全局 + 项目 + 子系统），含领域术语表和示例代码

#### D1.2 确定性反馈（机械性检查）

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

#### D1.3 非确定性前馈（系统提示词）

处理确定性规则无法捕获的细微之处。

**检查项**：
- [ ] Agent 角色定义清晰（每个 Agent 的职责边界）
- [ ] 行为约束明确（安全敏感操作必须获得审批等）
- [ ] 少样本示例（代码模板、期望输出格式）
- [ ] 自定义 Agent 的 SYSTEM PROMPT 经过设计而非随意编写

**评估标准**：
- 1 星：使用默认提示词，无自定义 Agent
- 3 星：有自定义 Agent 但角色定义笼统
- 5 星：每个 Agent 有精确的角色、工具限制、输出格式、示例

#### D1.4 非确定性反馈（推断型审查）

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
- 5 星：多 Agent 审查 + 检查清单 + 记忆系统 + 交叉验证

---

### D2：上下文工程

> 来源：Anthropic（2026 Agentic Coding Trends Report）
> 原则：从 Agent 的角度看，任何无法在上下文中访问的东西都不存在。仓库必须是唯一的事实来源。

#### D2.1 Write（编写上下文）

**检查项**：
- [ ] 系统提示词清晰、结构化
- [ ] 编码规范有代码示例（不只说"用 Service 层"，还给出 Service 的模板）
- [ ] API 契约有文档（请求/响应格式、错误码）
- [ ] 架构决策有记录（ADR 或等价物）

#### D2.2 Select（选择性拉取）

**检查项**：
- [ ] 不同任务类型加载不同的上下文（非全量加载）
- [ ] 项目级指南按子系统分离（前端/后端各自的 CLAUDE.md）
- [ ] 按需加载的工具和权限（非一次性全部暴露）

#### D2.3 Compress（压缩）

**检查项**：
- [ ] 有对话压缩/摘要机制（处理长会话）
- [ ] Agent 记忆系统可将历史经验压缩为模式文档
- [ ] 启用了 KV-cache 优化（稳定前缀 + 可变后缀架构）
- [ ] Agent 记忆具备情景维度与时序版本（episodic memory / temporal versioning）——当前 0/10 生产级 harness 缺失这两项，是长任务可靠性的关键缺口（参考 Memory-Aware SE Agents）

#### D2.4 Isolate（隔离）

**检查项**：
- [ ] 子任务可委托给独立子 Agent
- [ ] 子 Agent 有独立的上下文窗口和工具集
- [ ] 子 Agent 的结果通过结构化接口返回（非自由文本）

**评估标准**：
- 1 星：无项目级文档，Agent 完全依赖对话上下文
- 3 星：有 CLAUDE.md 但缺少 Select/Compress/Isolate 策略
- 5 星：四策略全覆盖 + 项目级文档分层 + 记忆系统 + KV-cache 优化

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

> 来源：OpenAI（Harness Engineering 三大支柱）+ [Meta-Harness](https://arxiv.org/html/2603.28052v1)（2026.03）+ [Self-Harness](https://arxiv.org/abs/2606.09498)（2026.06）+ [SIA](https://arxiv.org/abs/2605.27276)（2026.05）
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

**评估标准**：
- 1 星：无任何熵管理意识
- 3 星：意识到问题但无自动化机制
- 5 星：有定期清理 Agent、文档一致性检查、模型更新审查流程、Harness 有效性量化追踪、组件级贡献归因、Harness 自愈闭环

---

### D5：安全防护

> 来源：Simon Willison（致命三要素）+ Meta AI（二选一规则）
> 原则：处理不可信输入 + 访问敏感数据 + 修改状态 = 安全事故不可避免。最多同时拥有两个。

#### D5.1 致命三要素识别

**检查项**：
- [ ] 项目是否识别了三要素的暴露面
  - 不可信输入（用户输入、外部 API 响应、文件上传）
  - 敏感数据（数据库、密钥、用户隐私）
  - 状态修改（写文件、发请求、数据库写入）
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

**评估标准**：
- 1 星：无安全意识，Agent 拥有完全权限
- 3 星：有安全审查 Agent 但无结构性防护
- 5 星：三要素识别 + 二选一规则 + Policy as Code + 自动化安全测试 + 多用户治理层

---

### D6：测试与验证

> 来源：Andrew Ng（Agentic 工作流）+ Anthropic（三 Agent 架构）
> 原则：用 Agentic 工作流包装 GPT-3.5 超过 GPT-4 零样本表现——好的测试套件让 Agent 更安全地工作。

**检查项**：
- [ ] 测试框架已配置（PHPUnit、Vitest、Playwright 等）
- [ ] 核心业务流程有测试覆盖（特别是资金/交易/认证等关键路径）
- [ ] 测试遵循安全实践（测试隔离、不污染真实数据）
- [ ] Agent 可以运行测试来自我验证（测试命令在权限白名单中）
- [ ] 测试覆盖率在不同模块间均衡（非某些模块 0% 某些模块密集）
- [ ] 有端到端测试或集成测试（非仅单元测试）
- [ ] 区分 capability evals 与 regression evals，后者维持接近 100% 通过率专门捕获回退（参考 Demystifying Evals）
- [ ] Harness 变更用 holdout set 验证泛化，防止过拟合到已知用例（参考 Better Harness）

**评估标准**：
- 1 星：无测试基础设施
- 3 星：有测试框架但覆盖集中在少数模块
- 5 星：核心业务全覆盖 + 测试隔离 + Agent 自验证 + 端到端测试

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

---

### D8：Agent 工作流编排

> 来源：综合（Anthropic 三 Agent + LangChain 自验证 + Vercel 减法优化 + Loop Engineering 五大构建块 + Structured Graph Harness + LOOP Skill Engine）
> 原则：最好的 Harness 改进往往来自"做更少"。2026 年 6 月，Loop Engineering 将工作流编排从"手动触发"推进到"自动调度 + 自驱动"——Harness 装上时钟。

**检查项**：
- [ ] 新功能开发有明确的 Agent 工作流路径
- [ ] Bug 修复有明确的 Agent 工作流路径
- [ ] 工作流中包含验证步骤（非仅生成）
- [ ] Agent 的工具集经过精简（无冗余工具造成困惑）
- [ ] 工作流有失败恢复路径（Agent 卡住时的升级策略）
- [ ] 多 Agent 协作时有清晰的职责分工和通信协议
- [ ] 有自动化调度机制（定时触发或事件驱动，非纯手动启动——参考 Loop Engineering 的 Automations 原语）
- [ ] 有跨会话状态持久化（进度文件/看板，Loop 中断后能从中断处继续——参考 Loop Engineering 的 State 原语）
- [ ] 有目标驱动运行模式（`/goal` 类能力：持续运行直到可验证条件满足，判定由独立模型完成，非 self-grade）
- [ ] 高频重复任务有确定性回放机制（首次记录→后续回放，避免重复消耗推理 token——参考 LOOP Skill Engine）
- [ ] 跨 context window 的长任务有结构化状态交接（结构化进度文件 + git 回滚，非依赖模型记忆——参考 Effective Harnesses for Long-Running Agents）
- [ ] 有模型专属 Harness 配置机制（不同模型家族有不同提示词/工具/中间件配置，非"一套配置适配所有模型"——参考 Tuning Deep Agents 的 Harness Profiles）
- [ ] Harness 扩展通过可组合中间件实现（每条规则是独立模块，新增/删除一行配置即可——参考 How to Build a Custom Agent Harness 的中间件式扩展范式）

**评估标准**：
- 1 星：无工作流，Agent 自由发挥
- 3 星：有基本流程但缺少验证/恢复环节，无自动化调度
- 5 星：完整工作流 + 验证 + 恢复 + 工具精简 + 多 Agent 协作 + 自动化调度 + 跨会话状态 + 目标驱动 + 高频任务确定性回放 + 模型专属配置 + 可组合中间件

---

### D9：Harness 透明度与可复现性

> 来源：Stop Comparing LLM Agents Without Disclosing the Harness（2026.05）+ How Good Is Your Harness?（2026.05，统计归因实证：切换最佳 Harness 的增益 ≈ 切换最佳 LLM 的增益）
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

#### D9.3 Harness 可迁移性

**检查项**：
- [ ] Harness 组件设计为可剥离（模型更新后可单独移除特定中间件）
- [ ] Harness 核心逻辑不绑定特定模型版本的已知行为模式
- [ ] Harness 在跨模型家族迁移时的性能变化有追踪

**评估标准**：
- 1 星：Harness 配置完全隐式，无法从文档或代码中重建
- 3 星：有基本 Harness 文档但缺少方差分解和可迁移性验证
- 5 星：Harness 规格完整可复现 + 模型/Harness 方差可独立测量 + 跨模型迁移验证 + 可剥离性设计

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
| Level 1：个人基础 | 大部分维度 2-3 星 | 有马具但缺缰绳 |
| Level 2：团队级 | 大部分维度 3-4 星 | 骑手可以安全骑行 |
| Level 3：生产级 | 大部分维度 4-5 星 | 赛马级装备，可控且快速 |

---

## 常见反模式（评估时重点排查）

> 来源：综合多个权威来源的行业教训

### 反模式 1：软硬失衡

指南和 Agent 分工很完善（软性层），但 Linter/CI/类型检查缺失（硬性层）。
软性 Harness 告诉 Agent "应该做什么"，硬性 Harness 确保即使 Agent 忽略指南也不会出错。
**后者才是生产级 Harness 的关键。**

### 反模式 2：测试覆盖偏科

认证模块 30+ 测试，核心业务流程 0 测试。涉及资金的操作反而最缺保护。

### 反模式 3：文档仅限人类

架构决策存在于 Confluence/Slack/脑中，Agent 无法访问。仓库外的知识对系统不可见。

### 反模式 4：Harness 静态化

从不审查和更新 Harness 组件。模型更新后，之前必要的中间件变成累赘。不活跃的 skills/agents 持续积累。

### 反模式 5：工具膨胀

给 Agent 越多工具越好？错。Vercel 删除 80% 的工具反而获得更好结果。每个工具都是决策分支，过多的分支导致 Agent 困惑。

### 反模式 6：全权限 Agent

Agent 拥有完整的文件系统访问和命令执行权限，无任何操作需要审批。同时满足致命三要素。

### 反模式 7：Harness 过度绑定模型版本

为当前模型版本的特定行为构建了大量定制化中间件（如"模型总是忘记 X，所以加一层提醒"）。当模型更新后，这些中间件不仅无效，还可能干扰新模型的自然能力。
**设计 Harness 时要保持可剥离性（rippable）——当模型足够聪明不需要某些逻辑时，应能轻松移除。**

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

在生成端大量投入（更强的 Agent、更激进的自动化 Loop），却让人工审查保持手动的、串行的旧流程。Anthropic 内部数据显示，当 80%+ 的代码由 Claude 编写、人均产出增长 8 倍后，**整体吞吐量开始被人工审查环节卡住**——Agent 产出的代码堆积在审查队列里，生成端的加速被审查端的瓶颈完全吞噬（Amdahl's law）。

**正确做法**：把审查环节也纳入 Harness 设计——自动化 reviewer（maker/checker 分离）、结构化审查清单、回归 eval 自动门禁。投资比例应随生成端自动化程度同步向审查端倾斜，否则"让 Agent 写得更多"只是把拥堵从写代码挪到了等审查。

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

# Harness Engineering 中文知识库

> **模型是发动机，Harness 是整车。最好的发动机没有方向盘和刹车，哪里也去不了。**

Agent Harness Engineering 的系统化中文资料，覆盖 Why / Measure / How 三个维度。

---

## 文件导航

| 我要…… | 看哪个 |
|---------|--------|
| 理解 Harness Engineering 是什么、从哪来、为什么重要 | [`harness-engineering-article.md`](./harness-engineering-article.md) |
| 评估现有项目的 Agent Harness 成熟度 | [`harness-principles.md`](./harness-principles.md) |
| 动手给我的项目配置 Harness | [`harness-implementation-guide.md`](./harness-implementation-guide.md) |

### 长文（Why）

`harness-engineering-article.md` — 从 Prompt Engineering → Context Engineering → Harness Engineering → Loop Engineering 的完整范式迁移，含 14 个真实案例（OpenAI 100 万行零手写代码、Anthropic 16 Agent 集群写 C 编译器、LangChain 纯 Harness 优化 Top 30→Top 5、Stripe Minion 周产 1000+ PR、Bayer 制药 Harness 等），覆盖核心组件解剖、安全防护、设计原则、未来展望。

### 评估框架（Measure）

`harness-principles.md` — v1.6，基于 48 个权威来源的 9 维度评估框架（Fowler 四象限、上下文工程、架构约束、熵管理、安全防护、测试验证、CI/CD、工作流编排、透明度），每维度 1-5 星制，含 11 个反模式和标准化评估输出模板。

### 实施指南（How）

`harness-implementation-guide.md` — 按四层成熟度组织：个人项目（1-2h）→ 团队项目（1-2d）→ 生产级（1-2w）→ 自动化层（Loop Engineering）。每层讲清楚配什么、为什么这样配、坑在哪。

---

## 核心论点

2026 年行业共识正在加速收敛：

> **模型是商品，Harness 是护城河，Loop 是引擎。**

- LangChain 零模型改动，纯 Harness 优化，Terminal Bench Top 30 → Top 5
- 密歇根大学统计归因：切换最佳 Harness ≈ 切换最佳 LLM
- Google DeepMind：小模型 + 自动合成 Harness > 大模型
- Anthropic 内部数据：Claude 编写 80%+ 合并代码，人均产出 8 倍增长

---

## 权威来源

基于 48 个权威来源，涵盖：

**学术论文**（26 篇）：RUCAIBox 系统综述（502 篇参考文献）、OpenReview 独立综述、CAR 框架、AHE 自动进化、Self-Harness 自愈闭环、SIA 权重联合优化、Harness-MU 多用户安全、Physical AI Harness、Categorical Architecture 等

**官方资源**（20 篇）：OpenAI、Anthropic、Google、Martin Fowler/ThoughtWorks、LangChain

---

## 维护

通过 `/harness-kb-update` skill 自动化更新。每次执行搜索最新论文和行业文章 → 去重 → 分类 → 更新文章和框架 → 检查指南同步 → git commit。

Powered by Claude Code.

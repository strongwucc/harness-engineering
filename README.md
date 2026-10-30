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

`harness-engineering-article.md` — 从 Prompt Engineering → Context Engineering → Harness Engineering → Loop Engineering 的完整范式迁移，含 22 个真实案例（OpenAI 100 万行零手写代码、Anthropic 16 Agent 集群写 C 编译器、LangChain 纯 Harness 优化 Top 30→Top 5、Stripe Minion 周产 1000+ PR、Bayer 制药 Harness、Marmelab 246 仓库普查、Karpathy agentic engineering 等），覆盖核心组件解剖、安全防护、设计原则、未来展望。

### 评估框架（Measure）

`harness-principles.md` — v1.19，基于 91 个权威来源的 9 维度评估框架（Fowler 四象限、上下文工程、架构约束、熵管理、安全防护、测试验证、CI/CD、工作流编排、透明度），每维度 1-5 星制，含 16 个反模式和标准化评估输出模板。

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

## 术语对照

| 易混叫法 | 首选表达 | 边界 |
|---|---|---|
| 脚手架 / 运行时基座 | Harness | 同一概念的不同叫法，库内统一用 Harness |
| Session / Context Window / 持久状态 | 会话 / 上下文窗口 / 持久状态 | 三者不等同；压缩上下文 ≠ 删除会话记录 |
| 回归集 / holdout / 保留集 | 回归集（驱动修复用）/ 保留集（独立验收用） | 失败案例进回归集；保留集独立少量访问，反复用于选型即视为泄漏 |
| Hooks / 中间件 | Hook（事件扩展点）/ 中间件（实现模式） | 中间件是 Hook 的一种实现方式，两者非同义 |
| Loop / 定时调度 | Loop（循环编排） | 不是所有循环都需要定时器；一次性任务用会话内循环即可 |

---

## 权威来源

基于 91 个权威来源（以 `harness-principles.md` 权威来源表为准），涵盖：

**学术论文**：RUCAIBox 系统综述（502 篇参考文献）、OpenReview 独立综述、CAR 框架、AHE 自动进化、Self-Harness 自愈闭环、SIA 权重联合优化、Harness-MU 多用户安全、Physical AI Harness、Categorical Architecture、Barbaste 83 页源码解剖等

**官方资源与深度解读**：OpenAI、Anthropic、Google、Martin Fowler/ThoughtWorks、LangChain、Addy Osmani、Lilian Weng、Andrej Karpathy 等

---

## 维护

通过 `/harness-kb-update` skill 自动化更新。每次执行搜索最新论文和行业文章 → 去重 → 分类 → 更新文章和框架 → 检查指南同步 → git commit。

Powered by Claude Code.

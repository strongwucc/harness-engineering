# Harness 工程实施指南

> 这份指南告诉你**具体怎么配**。想理解"为什么这么配"，先看 `harness-engineering-article.md`。
> 想评估现有项目做到什么程度了，看 `harness-principles.md`。

三个文件的关系：**文章讲 Why → 框架讲 Measure → 指南讲 How。**

---

## 核心思想

Harness Engineering 说到底是这么一件事：

**模型是发动机，你没得换。但你可以决定它装在哪辆车上、方向盘什么样、刹车怎么设计。**

所有配置围绕同一个目标：让 Agent 产出的东西靠谱，犯错了能自己拉回来。

下面按成熟度分层展开。**别试图一次性把四层全做出来。** 先从第一层开始，跑几天，碰到什么问题再加第二层。Mitchell Hashimoto 给的定义就是这个意思——"每次 Agent 犯错，就改变系统让这个错误在结构上不可能再发生"——是个迭代过程，不是一次性项目。

---

## 第一层：个人项目（1-2 小时）

要解决的问题：Agent 不知道你的项目长什么样。

### 1. 项目级指南文件

在项目根目录放一个 `CLAUDE.md` 或 `AGENTS.md`，100 行以内。写清楚四件事：

**技术栈**：语言、框架、构建工具。Agent 不需要翻遍 `package.json` 自己猜。

```
# CLAUDE.md
- 这个项目用 TypeScript + React 18 + Vite
- 包管理用 pnpm，不要用 npm
- 测试用 Vitest
```

**编码约定**：缩进、引号、命名——不写它就随缘。

```
- 缩进 2 空格
- 字符串用单引号
- 组件文件用 PascalCase，工具函数用 camelCase
```

**目录结构**：哪个目录放什么。Agent 经常自己发明奇怪的路径，因为没人告诉它。

```
- src/components/ 通用组件
- src/pages/ 页面入口
- src/hooks/ 自定义 hooks
- src/utils/ 工具函数
```

**禁止事项**：比正面指令更管用。直接告诉它别干什么。

```
- 不要引入新的依赖包
- 不要修改数据库 schema
- 不要删除已有代码，除非明确要求
```

指南文件是投入产出比最高的配置。一个下午写好，以后每次会话自动加载，零维护成本。

### 2. 自动化检查

光写文档不够——Agent 看了但不一定严格遵守。需要加**不会出错的机械性检查**。

**Linter**：ESLint、Pint、Prettier 随便哪个，配好规则挂到 pre-commit hook。

**类型检查**：TypeScript、PHPStan、Psalm。模型天然不擅长类型一致性，这层校验能抓大量低级错误。

**测试命令让 Agent 自己能跑**：Agent 写完代码自己跑 `npm test`，拿到错误信息自己修。你不是检查员，你是设计检查规则的。这就是 Mitchell Hashimoto 那句话的实操版："每次 Agent 犯错，改变系统让它不可能再犯"。

配置示例（`.claude/settings.json` 的 hooks 部分）：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx eslint --fix ${CLAUDE_TOOL_OUTPUT_FILE_PATH}"
          }
        ]
      }
    ]
  }
}
```

---

## 第二层：团队项目（1-2 天）

要解决的问题：多个人 + 多个 Agent 同时干活，互相不踩脚。

### 3. 分层指南 + 渐进式披露

`CLAUDE.md` 不能无限膨胀。一个 5000 行的指南，Agent 读到后面忘了前面。

**项目根目录**放总览，100 行以内：整体架构、技术栈、核心约定。

**各子系统**放各自的 `CLAUDE.md`：

```
src/
├── frontend/
│   └── CLAUDE.md    ← 前端约定
├── backend/
│   └── CLAUDE.md    ← 后端约定
└── tests/
    └── CLAUDE.md    ← 测试规范
```

**常见任务模板**：修 bug 的流程、加新功能的流程写下来。Agent 照着走。

这里的思路是：给 Agent **一张地图**，而不是一本 1000 页的手册。让它自己去找需要的部分。这就是上下文工程里的 "Select" 策略。

### 4. CI 强制执行的架构约束

到了团队级，架构规则光写在文档里已经不够了。必须上机械性强制。

**分层规则写成自动化检查**：比如"Controller 不能直接调 Model，必须走 Service 层"。用 ArchUnit（Java）、PHPStan 自定义规则（PHP）、dependency-cruiser（JavaScript）在 CI 里跑。

**依赖方向检查**：UI 层不能导入数据库层，domain 不能依赖 framework。

**自定义 Linter 规则**：团队特有的模式写成规则。报错信息里带上"怎么改"——Agent 拿到错误能自己修。

反直觉的一点：约束越多，Agent 产出反而越好。没边界的时候它在几万种可能性里乱撞，有边界了能快速收敛。

### 5. 工具权限按需分配

别给 Agent 所有工具。Vercel 删掉 80% 的工具后，Agent 反而更快更可靠。

- 修 bug 的 Agent 不需要数据库写入权限
- 写文档的 Agent 不需要执行 shell
- 永远不要给生产环境相关权限

每个工具都是一个决策分支，多了 Agent 会迷路。

### 6. Maker / Checker 分离

让 Agent 审查自己写的代码——跟让学生给自己的卷子打分一样，永远全对。

- **写代码的 Agent** 和 **审代码的 Agent** 不能是同一个
- 审查 Agent 有结构化检查清单（安全、架构、代码质量）
- 审查过了才能进主分支

Anthropic 的三 Agent 架构（Planner → Generator → Evaluator）就是这个思路。最简单落地：`.claude/agents/` 下分别定义 `coder` 和 `reviewer`，reviewer 只读、不写文件。

---

## 第三层：生产级（1-2 周）

要解决的问题：Agent 不是偶尔用，是持续跑。跑错了影响会累积。

### 7. 中间件层

在 Agent 循环的每一步插入钩子——每条钩子是一条独立规则，加一行配置就有，删一行配置就没了。

| 钩子点 | 干什么 |
|--------|--------|
| 模型调用前 | 检查权限、注入上下文、复杂任务切强模型、简单任务切便宜模型 |
| 模型输出后 | 验证格式、检测重复输出（防死循环）、触发上下文压缩 |
| 工具调用前 | 验证参数、检查 token 预算还剩多少 |
| 工具返回后 | 截断过长输出、写审计日志、失败重试 |
| 会话开始/结束 | 初始化资源、保存进度文件 |

核心好处是**可剥离**：模型升级后，某些中间件变累赘了，删掉就是一行配置。专为旧模型写的补救逻辑，不该在新模型上继续拖着。

### 8. 模型专属配置（Harness Profiles）

LangChain 在 2026 年 4 月发现：同一套提示词和工具，Claude Opus 上跑得好，GPT Codex 上效果平庸——反过来一样。

原因不同模型有不同"口味"：
- OpenAI Codex 喜欢 `apply_patch` / `shell_command` 这种工具名
- Anthropic Claude 偏好不同的约定
- 同一家族不同版本（Opus 4.6 → 4.7）也需要调整

做法是按模型维护独立的配置覆盖层：
- 提示词前缀/后缀（不同模型对指令格式敏感度不同）
- 工具名称和选择（投其所好）
- 中间件策略（推理越强，需要显式检查越少）

量化结果：tau2-bench 上 +10-20 个百分点。原则很简单——**为模型适配 Harness，不是反过来。**

### 9. 熵管理

AI 写得越多，代码库越容易乱。命名习惯分化、文档和代码脱节、死代码堆积。

需要一组**定期跑的清理 Agent**：
- 文档一致性检查：读文档 → 读代码 → 报告对不上的
- 约束违规扫描：找绕过早期检查的代码
- 模式执行：找跟团队写法偏离的代码
- 依赖审计：找循环依赖和不必要的大体积依赖

**Harness 自己也会积累熵。** 模型每更新一次大版本，审查一遍 Harness 配置，把没用的剥掉。AHE（Agentic Harness Engineering）证明这件事可以让 Agent 自己做了——Weakness Mining → Proposal → Validation，全自动。

### 10. 安全纵深

Simon Willison 总结过一个框架，很好记：**Agent 最多同时拥有下面三样东西里的两个**——

1. 处理不可信输入（外部网页、用户上传、邮件）
2. 访问敏感系统（数据库、密钥、用户隐私）
3. 能改状态（删文件、发请求、扣款）

落地做法：
- **Policy as Code**：一个 YAML 配置写清楚"什么事需要审批"，工具调用前钩子检查
- **生产/开发环境的 Agent 权限完全分开**
- **凭据不进仓库**，更不进 `CLAUDE.md`

---

## 第四层：自动化层（Loop Engineering）

> 前三层没做到位之前别上这一层。没缰绳的马，装自动跑圈程序只会跑得更远更危险。

前三层解决"Agent 单次干活靠谱吗"，这一层解决"Agent 能不能自己决定什么时候干活"。

六个零件：

- **定时器**：每天早上自动拉 CI 日志、issue 列表。不是人手动开 Agent。Claude Code 用 `/loop` 和 cron，Codex 有内置 Automations。
- **工作隔离区**：每个任务开独立 git worktree，Agent A 改的文件 Agent B 不会踩到。
- **技能注入**：前面写的 `CLAUDE.md` 就是 Skill。写一次，每次触发自动加载。
- **连接器**：MCP 协议让 Agent 能读 GitHub issue、查数据库、发 Slack 消息。
- **子 Agent 拆分**：maker/checker 的规模化版本。一个修一个审，审过了自动开 PR。
- **状态持久化**：Loop 跑三天，哪些做完了、哪些卡住了、哪些等人——落盘成结构化文件，下次从这儿继续。

完整流程长这样：

```
每天早上定时器触发
  → 拉取 issue 列表 / CI 失败日志
  → 每个值得处理的开独立 worktree
  → 派 worker agent 修
  → 派 reviewer agent 对照规范审查
  → 通过就自动开 PR、更新工单
  → 没通过的进 triage 等人
  → 进度写进 state 文件，明天继续
```

**你设计了一次，没有手动提示任何一个步骤。**

详细内容见 `harness-engineering-article.md` 的 3.6 节。

---

## 全局原则

不管做到哪一层，这两个原则通用。

### 可剥离

模型会升级。今天必要的中间件明天可能变死重。加一条配置很简单，删一条也应该很简单。不要为当前模型的特定缺陷写复杂补救逻辑——模型把那个缺陷修掉之后，你的补救逻辑就成了纯累赘。

### 迭代思维

别四层全做完才开始用。第一层先跑起来，碰到问题再加第二层。Mitchell Hashimoto 的原始定义是这个过程的最佳描述："每次 Agent 犯错，就改变系统让这个错误在结构上不可能再发生。"

---

## 与其他文件的关系

| 我要…… | 看哪个 |
|---------|--------|
| 理解 Harness Engineering 是什么、从哪来、为什么重要 | `harness-engineering-article.md` |
| 评估现有项目的 Harness 成熟度 | `harness-principles.md` |
| 动手给我的项目配置 Harness | 就这份 |
| 查某个具体配置的出处和理论依据 | `harness-principles.md` → 对应维度的"来源"字段 |

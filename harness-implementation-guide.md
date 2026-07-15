# Harness 工程实施指南（Claude Code 落地手册）

> 这份指南告诉你**在 Claude Code 里具体配什么、写在哪个文件**。想理解"为什么这么配"看 `harness-engineering-article.md`；想评估现有项目做到什么程度看 `harness-principles.md`。
> 三个文件的关系：**文章讲 Why → 框架讲 Measure → 本指南讲 How（Claude Code 版）。**

## 核心思想

模型是发动机，你基本换不了；但你能决定它装在哪辆车上、刹车怎么设计、仪表盘显示什么。所有配置围绕一个目标：**让 Agent 产出的东西靠谱，犯错了能自己拉回来。**

这是迭代过程不是一次性项目——**别一上来就做满四层**。第一层配好跑几天，碰到具体问题再加第二层。Mitchell Hashimoto 那句话就是纪律：*"每次 Agent 犯错，就改变系统让这个错误在结构上不可能再发生。"*

---

## Claude Code Harness 原语速查

动手前先建立心智地图：Claude Code 的 Harness 由这些零件组成。后续每一层就是把其中几个零件配起来。

| 原语 | 配在哪 | 常驻/按需 | 一句话用途 | 详见 |
|------|--------|----------|-----------|------|
| **CLAUDE.md** | 项目根 / 子目录 | 常驻上下文 | 项目规则、技术栈、禁令——每次会话自动加载 | §1, §3 |
| **Permissions** | `settings.json` 的 `permissions` | 常驻 | allow/ask/deny 控制工具与路径权限 | §2, §5, §10 |
| **Hooks** | `settings.json` 的 `hooks` | 事件触发 | 在工具调用前后/会话节点跑确定性脚本（=中间件） | §7, 附录 A |
| **Subagents** | `.claude/agents/*.md` | 按需调用 | 独立上下文+独立工具集的专用 Agent（=maker/checker） | §6 |
| **Skills** | `.claude/skills/<name>/SKILL.md` | 按需调用 | 可复用的流程能力包，按需进上下文 | §3, §9 |
| **Slash commands** | `.claude/commands/*.md` | 手动触发 | 一段可参数化的 prompt 模板 | §6 |
| **MCP servers** | `.mcp.json` | 常驻连接 | 让 Agent 读 GitHub/数据库/飞书等外部系统 | §4(Loop) |
| **statusline** | `settings.json` 的 `statusLine` | 常驻显示 | 自定义状态栏（可观测性的一个出口） | §10 |

> **两个最容易混的概念**：`CLAUDE.md` 是**常驻上下文**（每次都在），`Skill` 是**按需能力包**（用到才加载）。把流程性内容写成 Skill，把"永远适用的规则"写进 CLAUDE.md——别搞反。

---

## 第一层：个人项目（1-2 小时）

要解决的问题：**Agent 不知道你的项目长什么样。**

### 1. CLAUDE.md：常驻上下文

项目根放一个 `CLAUDE.md`（或 `AGENTS.md`），控制在 100 行内，写清四件事。这是投入产出比最高的配置——一个下午写好，以后每次会话自动加载。

```markdown
# CLAUDE.md

## 技术栈
- Laravel 11 + PHP 8.3（后端），Vue 3 + TypeScript + Vite（前端）
- 包管理：后端 composer，前端 pnpm（不要用 npm）
- 测试：后端 Pest，前端 Vitest

## 编码约定
- 缩进 2 空格，字符串用单引号
- PHP 类用 PascalCase，方法 camelCase；Vue 组件 PascalCase

## 目录结构
- app/Http/Controllers/ 只做参数校验和调用 Service
- app/Services/ 业务逻辑
- app/Models/ Eloquent 模型，不写业务逻辑
- resources/js/components/ 通用 Vue 组件

## 禁止事项
- 不要引入新的第三方依赖
- 不要修改数据库 migration（新建 migration）
- 不要删除已有代码，除非我明确要求
```

**心法是 ratchet（棘轮）**：每条规则都要能追溯到一次真实的 Agent 失败。Agent 把测试注释掉了？加一条"不许注释测试"。它绕过了 Service 层？加一条"Controller 不能直接调 Model"。**只在你见过真实失败时才加规则**——凭空想象的规则是噪音，会稀释真正重要的规则。把 CLAUDE.md 当飞行员的检查单（pilot's checklist，几十行），不是风格手册。

### 2. 自动化检查 + 放行测试命令

光写文档不够——Agent 看了但不一定遵守。要加**不会出错的机械性检查**，并让 Agent 自己能跑测试。

**第一步：把检查和测试命令放进 permissions 的 allow**，让 Agent 不用每次申请就能跑（`.claude/settings.json` 或 `~/.claude/settings.json`）：

```json
{
  "permissions": {
    "allow": [
      "Bash(php artisan test*)",
      "Bash(./vendor/bin/pint*)",
      "Bash(./vendor/bin/phpstan*)",
      "Bash(pnpm vitest*)",
      "Bash(pnpm eslint*)",
      "Bash(pnpm typecheck*)",
      "Bash(git diff*)",
      "Bash(git log*)"
    ]
  }
}
```

**第二步：配 Hook，让 Agent 每次改完文件自动跑 lint**（见 §7 的完整 Hook 说明）。最小可用版：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{ "type": "command", "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/lint.sh" }]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/lint.sh —— 按扩展名自动格式化改动的文件
# hook 的 stdin 是一段 JSON（含 tool_input.file_path），jq 直接读
FILE=$(jq -r '.tool_input.file_path // empty')
[ -z "$FILE" ] && exit 0

case "$FILE" in
  *.php)            ./vendor/bin/pint "$FILE" ;;
  *.ts|*.vue|*.js)  npx eslint --fix "$FILE" ;;
esac

exit 0   # 退出码 0 = 不阻塞；auto-fix 已就地改好文件
# 若要把"剩余报错"回灌给 Agent 让它继续修，用 exit 2 + stderr（见附录 A）
```

设计检查时记住一条原则：**success is silent, failures are verbose（成功静默，失败冗长）**。检查通过时 Agent 不需要听到任何声音；检查失败时把错误原文塞回循环让它自己改。常态零成本，出错直接可执行。

---

## 第二层：团队项目（1-2 天）

要解决的问题：**多个人 + 多个 Agent 同时干活，互相不踩脚。**

### 3. 分层 CLAUDE.md + 任务模板（Select 策略）

CLAUDE.md 不能无限膨胀——一个 5000 行的指南，Agent 读到后面忘了前面。给 Agent **一张地图**而非一本千页手册：根目录放总览（100 行内），各子系统放各自的 CLAUDE.md，Claude Code 进入子目录工作时会自动加载对应的。

```
project/
├── CLAUDE.md                 # 总览：整体架构、技术栈、核心约定
├── app/Http/CLAUDE.md        # 后端 Controller/Request 约定
├── app/Services/CLAUDE.md    # Service 层模式 + 事务边界
├── resources/js/CLAUDE.md    # 前端组件规范 + 状态管理
└── tests/CLAUDE.md           # 测试命名、mock 边界
```

**常见任务写成 Skill 或 command**（不是写进 CLAUDE.md）。例如修 bug 的流程、加新功能的流程——这种"按需调用的步骤"放进 `.claude/commands/fix-bug.md`，Agent 执行该任务时才进上下文，不污染常驻 CLAUDE.md。

### 4. CI 强制架构约束

团队级架构规则光写文档不够，必须上**机械性强制**。分层规则写成 CI 检查，Agent 拿到报错能自己改：

- **JavaScript/TypeScript**：`dependency-cruiser` 检查依赖方向（UI 层不能导数据库层）
- **PHP/Laravel**：PHPStan（+ Larastan）自定义规则 + `ArchTest`（如 `ajthinking/architest`）断言"Controller 不能直接 new Model"
- **通用**：自定义 ESLint 规则 / PHPStan 规则，**报错信息里带上"怎么改"**

```php
// tests/Architecture/ArchitectureTest.php —— 需要 pestphp/pest-plugin-arch
test('Controllers 不能直接依赖 Eloquent 查询构造器')
    ->expect('App\Http\Controllers')
    ->classes()
    ->not->toUse('Illuminate\Database\Eloquent\Builder');
```

### 5. 工具权限：allow / ask / deny 三档

Vercel 删掉 80% 工具后 Agent 反而更快更可靠——每个工具都是决策分支，多了会迷路。Claude Code 用 `permissions` 的三档控制，优先级 **deny > ask > allow**（deny 永远赢）：

```json
{
  "permissions": {
    "defaultMode": "default",
    "allow": [
      "Bash(php artisan test*)",
      "Bash(pnpm vitest*)",
      "Read(./app/**)",
      "Read(./resources/**)",
      "Edit(./app/**)"
    ],
    "ask": [
      "Bash(git push*)",
      "Bash(composer require*)",
      "Write(./composer.json)",
      "Write(./package.json)"
    ],
    "deny": [
      "Bash(rm -rf /*)",
      "Bash(rm -rf ~*)",
      "Read(./.env)",
      "Edit(./database/migrations/*)",
      "Bash(php artisan migrate*)"
    ]
  }
}
```

**规则语法要点**：
- `Bash(php artisan test*)` —— 前缀匹配，带不带参数都放行（最常用）
- `Bash(git push:*)` —— `:*` 形式匹配 `git push` 及其子命令；`Bash(cmd *)`（空格+`*`）则要求命令必须带参数
- `Read(./app/**)` —— gitignore 风格路径，`**` 递归
- `mcp__github__create_issue` —— MCP 工具名用双下划线
- `&&`/`||` 连接的复合命令，**每段都要单独匹配**才放行

### 6. Maker / Checker：Subagent 分离

让 Agent 审查自己写的代码，跟让学生给自己卷子打分一样——永远全对。在 `.claude/agents/` 下分别定义写代码的和审查的，**审查那个只读、不写文件**：

```yaml
# .claude/agents/coder.md
---
name: coder
description: 编写和修改代码。实现功能或修 bug 时委派给它。
tools: Read, Edit, Write, Bash
model: inherit
---
你是编码执行者。理解需求后直接实现，遵循 CLAUDE.md 的分层约定。
```

```yaml
# .claude/agents/reviewer.md
---
name: reviewer
description: 审查代码质量和安全。代码改完后委派给它。
tools: Read, Grep, Glob          # 注意：没有 Edit/Write → 只读
model: sonnet
---
你是代码审查者。按优先级检查：1) 安全漏洞  2) 分层违规  3) 可读性。
只报告问题，不改代码。
```

调用方式：Claude 根据 `description` 自动委派，或你显式 `@reviewer`。这对应 Anthropic 的 Planner→Generator→Evaluator 架构的简化版。

> **现实范例**：你本机 `.claude/agents/` 里的 `laravel-reviewer` + `vue-reviewer` + `security-auditor` + `code-quality-reviewer`，加上 `full-review` 命令编排出"四维并行审查 + 交叉验证"——就是这套模式的成熟实现，可以直接参考它的 frontmatter 写法。

---

## 第三层：生产级（1-2 周）

要解决的问题：**Agent 不是偶尔用，是持续跑。跑错了影响会累积。**

### 7. Hooks：中间件的 Claude Code 落地

前面讲的"中间件"在 Claude Code 里就是 **Hooks**——在生命周期的特定点跑确定性脚本。每条 hook 是一条独立规则，加一段配置就有，删一段就没。核心事件（完整列表见附录 A）：

| 事件 | 触发时机 | 能否阻断 | 典型用途 |
|------|---------|---------|---------|
| `SessionStart` | 会话启动/恢复 | 否 | 注入当前分支、加载记忆 |
| `UserPromptSubmit` | 用户提交 prompt | **是** | 拦截含密钥的 prompt、注入上下文 |
| `PreToolUse` | 工具调用前（matcher=工具名） | **是** | **权限/合规拦截、参数校验** |
| `PostToolUse` | 工具调用后（matcher=工具名） | 否 | 自动 lint、截断过长输出、审计日志 |
| `Stop` | Claude 响应完成 | **是** | 强制跑测试才能结束、通知 |
| `SubagentStop` | 子 agent 完成 | **是** | subagent 产出的质量门 |
| `PreCompact` | 上下文压缩前 | **是** | 压缩前抢救关键信息 |

**控制流协议**（关键）：hook 从 stdin 收到 JSON（含 `tool_name`/`tool_input` 等），用**退出码 + JSON stdout** 反馈：
- 退出码 `0` + JSON stdout → 解析 JSON（可注入上下文或改决策）
- 退出码 `2` → 阻断，stderr 作为错误展示给用户
- 其他退出码 → 非阻塞错误，继续

**例 1：PostToolUse 自动 lint**（已在 §2 给过脚本，这里聚焦配置）——失败时把错误回灌：

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{ "type": "command", "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/lint.sh" }]
    }]
  }
}
```

**例 2：PreToolUse 硬拦危险命令**（确定性合规——提示词做不到的事）：

```bash
#!/bin/bash
# .claude/hooks/block-dangerous.sh
INPUT=$(cat)
CMD=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

# 阻断删根、强制推送生产、改 migration
if echo "$CMD" | grep -qE 'rm -rf /|git push.*prod|artisan migrate'; then
  jq -n '{hookSpecificOutput:{hookEventName:"PreToolUse",permissionDecision:"deny",permissionDecisionReason:"危险操作被 Harness 拦截，需人类确认"}}'
  exit 0   # 退出码 0 + JSON 才生效；不能用 exit 2（那是错误，不是决策）
fi
exit 0
```

这就是"**你无法用提示词实现合规**"的落地——PII 脱敏、危险命令拦截、"每次都必须触发"的策略，全部活在 hook 里，用代码保证每次必中，不托付给模型的概率判断。

**坑**：
- 想阻断工具必须返回 `permissionDecision: "deny"`（退出码 0 + JSON），**不要用 exit 2**（exit 2 是"hook 出错"，不是"我决定拒绝"）
- `async: true` 的 hook **无法控制流**（决策字段被忽略）——要拦截就别开 async
- matcher 是**工具名**（如 `"Bash"`），不是命令内容；命令内容的判断在脚本里做
- JSON stdout 必须是**唯一输出**，别混 `echo` 调试信息

### 8. 多模型配置：/switch + env + subagent.model

Claude Code **没有原生的"Harness Profile"概念**，多模型适配靠三个机制组合：

**1. 全局模型映射**（`~/.claude/settings.json` 的 `env`）——把 sonnet/opus/haiku 槽位映射到你实际用的模型：

```json
{
  "env": {
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-5.2",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-5.2",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-4.5-air"
  }
}
```

**2. `/switch` skill 一键切**——写一个 `.claude/skills/switch/SKILL.md`，只改 `ANTHROPIC_*` 几个字段，不动其他配置（你本机已有）。

**3. subagent 级别配模型**——把重活给强模型、轻活给便宜模型，在 agent frontmatter 里写 `model: sonnet` / `model: haiku`。

**core vs profile 纪律**（保留这条，砍掉理论）：每条 harness 改动要么是**核心改进**（换任何模型都生效），要么是**模型专属**（只服务某个模型的口味，如 glm 偏好的工具命名）。模型专属的部分隔离到 `/switch` 切换的配置里，让核心 `settings.json` 保持干净——换模型时只动 profile 层。

**GLM5.2 / DeepSeek V4 profile 审计维度**——升级或 `/switch` 时按这张表逐项核对。Self-Harness 证明不同 base model 学到的是不同的 model-specific harness instructions，**两个模型弱点分布不同、profile 不能互换**；具体偏好留实测填充，勿臆测：

| 维度 | GLM5.2 | DeepSeek V4 | 说明 |
|------|--------|-------------|------|
| 提示词格式偏好 | TODO（实测） | TODO（实测） | 吃 XML 标签 / Markdown 结构 / 纯指令？ |
| 工具命名敏感度 | TODO | TODO | 偏好哪些工具名（apply_patch / edit_file 等） |
| 中间件策略 | TODO | TODO | 推理够强时哪些显式检查可去掉 |
| 推理预算（effort） | TODO | TODO | 默认 high/xhigh，简单任务降档省 token |
| 已知坑 | TODO | TODO | 各自的失败模式 |

填法：每次实测到一个偏好，填进对应格子并加日期。删改 profile 项时按 §9 的 holdout 流程验证。

### 9. 熵管理：定期清理 + /loop

AI 写得越多，代码库和 Harness 都会乱：命名分化、文档脱节、死代码堆积、为旧模型写的 hook 变成死重。需要**定期跑的清理 Agent**，用 `/loop` 或系统 cron 定时触发（§第四层）：

- **文档一致性**：读 CLAUDE.md → 读代码 → 报告对不上的
- **约束违规扫描**：找绕过 §4 架构检查的代码
- **依赖审计**：找循环依赖和没用的大依赖
- **Harness 自审**：模型每升一次大版本，审一遍 hooks/permissions，把没用的剥掉（AHE 证明这事能让 Agent 自己做——Weakness Mining→Proposal→Validation）

清理脚本本身写成 Skill（如 `.claude/skills/harness-audit/`），`/loop` 每周触发一次。

#### 模型升级时的 Harness 审计 SOP

「模型每升一次大版本，审一遍 Harness 把没用的剥掉」这句话的完整流程。核心：**把"该不该删"从感觉变成可证伪实验，且永不碰约束治理类配置**。

**先分两类——只有一类能审删**：

| 类别 | 例子 | 升级时 |
|------|------|--------|
| **例行支撑**（可剥离） | "模型总忘记 X"的提示词补丁、格式脚手架、循环检测、手动 reasoning 预算 | 候选删除，逐项验证 |
| **约束治理**（不可剥离） | PII 脱敏 hook、权限 deny、审批门、预算上限 | **永不因模型变强而删** |

这条边界来自模型-harness 共演化研究：例行能力支撑会被模型吸收进权重、随版本消失；约束性治理（"你无法用提示词实现合规"）必须永远留在外部。

**审计 6 步**（针对 `/switch` 在 GLM5.2 / DeepSeek V4 间切换）：

1. **冻结基线**：pin 当前版本，跑一遍回归型 eval 集记指标。
2. **先审 system prompt / CLAUDE.md**：AHE 消融证明收益来自工具/中间件/长期记忆，**而非系统提示词**——纠正性条款是最可能的死重，优先审。
3. **标 core / profile**：core（换任何模型都生效）留在 `settings.json`；profile（只服务 GLM5.2 或 DeepSeek V4 口味）隔离到 `/switch` 层。
4. **跨模型 holdout 验证**：删/改一项 → 在 holdout 任务集上跑，**两个模型都跑**。单模型上"没退化"可能是过拟合——Better Harness 就是跨 Sonnet / GLM-5 验证泛化的。
5. **ratchet 触发**：只有"强模型让某约束变多余"才删，删时记录它补的是什么旧缺陷。
6. **自审兜底**：让 Agent 跑 Self-Harness 的 Weakness Mining→Proposal→Validation，它提删改建议、你在 holdout 上验收。

**陷阱**：Harness 不会单纯收缩，只会移动——旧失败消失，天花板抬高冒出新失败模式。审计既要做减法，也要识别新失败补新约束。

> 旁证（非本文权威来源体系）：社区博客「The Harness Problem」的"弱模型探针"（强制用老/小模型跑通来逼出冗长提示）、论文「Removal-Based Attribution」的 LOO 归因（删一项看指标差），与上面 holdout 思路一致，作补充视角。

### 10. 安全纵深

Simon Willison 的框架很好记：**Agent 最多同时拥有下面三样里的两个**——① 处理不可信输入（外部网页、用户上传）② 访问敏感系统（数据库、密钥、用户隐私）③ 能改状态（删文件、发请求、扣款）。三个都要 → 强制人类审批。

落地（全是 §5 和 §7 的机制）：
- **Permissions deny/ask**：`.env`、secrets/、生产 migration、`rm -rf` 全进 deny；`git push`、装依赖进 ask
- **Policy as Code = PreToolUse hook**：§7 例 2 那种确定性拦截，不靠提示词
- **凭据不进仓库**，更不进 CLAUDE.md——密钥只在 `env` 或运行时注入
- **可观测性**：配 statusline 显示当前分支/token 用量/模型；PostToolUse hook 写审计日志

---

## 第四层：自动化（Loop Engineering）

> 前三层没做到位之前别上这层。**没缰绳的马，装自动跑圈程序只会跑得更远更危险。**

前三层解决"Agent 单次干活靠谱吗"，这一层解决"Agent 能不能自己决定什么时候干活"。六个零件，每个都对应 Claude Code 的具体命令：

| 零件 | Claude Code 落地 |
|------|----------------|
| **定时器** | `/loop 30m /fix-flaky-tests` 或系统 cron 触发 `claude -p` |
| **工作隔离区** | `git worktree add ../task-123 feature/task-123`，每个任务独立 worktree |
| **技能注入** | §3 的 Skill 自动按需加载；CLAUDE.md 常驻；**`loop.md` 定义裸 `/loop` 的默认维护循环** |
| **连接器** | MCP server（`.mcp.json`）让 Agent 读 GitHub issue / 数据库 / Slack |
| **子 Agent 拆分** | §6 的 maker/checker 规模化：worker 修、reviewer 审、审过自动开 PR |
| **状态持久化** | 进度写 `PROGRESS.md` 断点续跑；`/loop` 任务会话范围，`--resume` 恢复未过期任务 |

### 先选对调度方式

`/loop` 是**会话范围**的——只在 Claude Code 运行且空闲时触发，会话退出即停、启动新对话即清。无人值守的关键路径别用 `/loop`：

| | Cloud (Routines) | Desktop 计划任务 | `/loop` |
|---|---|---|---|
| 需机器/会话开机 | 否/否 | 是/否 | 是/**是** |
| 跨重启持久 | 是 | 是 | 仅 `--resume` 恢复未过期 |
| 本地文件 | 否（fresh clone） | 是 | 是 |
| MCP / 权限 | 每任务配 | 每任务配 | **继承会话** |
| 最小间隔 | 1 小时 | 1 分钟 | 1 分钟 |

**选型**：不依赖你的机器可靠跑 → Cloud Routines；要本地文件但不依赖会话 → Desktop；会话内快速轮询 → `/loop`。要对事件**实时反应而非轮询**（CI 直接推进会话）→ Channels。

### `/loop` 的三种形态

| 你给什么 | 行为 |
|---|---|
| 间隔+提示词 `/loop 5m check deploy` | 固定计划 |
| 仅提示词 `/loop check deploy` | Claude 每次迭代**动态选 1m–1h 延迟**（PR 活跃等短、无事等长），可能直接用 **Monitor tool** 流式跑后台脚本、避免轮询、更省 token |
| 仅间隔或裸 `/loop` | **内置维护提示词**：继续未完成工作 → 照顾当前分支 PR（评论/失败 CI/合并冲突）→ 无事时清理。不越界、不可逆操作仅续已授权内容 |

固定间隔 7 天自动过期；jitter 最多延 30 分钟（或间隔一半），精确时间别用 `:00`/`:30`。v2.1.196 起计划触发只跑允许调用的 skill，内置命令/禁用 skill/MCP prompts 作为纯文本不执行——防 loop 越权。

### `loop.md`：把维护循环固化成文件

裸 `/loop` 的内置维护提示词可被项目自定义替换——这是 Loop Engineering 在 Claude Code 的原生落点（Skills + State + Automations 三个原语交汇）：

| 路径 | 范围 |
|---|---|
| `.claude/loop.md` | 项目级，优先 |
| `~/.claude/loop.md` | 用户级，兜底 |

```markdown title=".claude/loop.md"
Check the `release/next` PR. If CI is red, pull the failing job log,
diagnose, push a minimal fix. If new review comments arrived, address
each and resolve the thread. If green and quiet, say so in one line.
```

纯 Markdown，像直接敲 `/loop` 提示词一样写；**编辑下次迭代即生效**（运行时可优化）；超 25000 字节截断；命令行给了提示词则忽略 `loop.md`。

### 最小可跑示例

一个最小的 cron + `/loop` 示例（每天早 9 点处理 CI 失败）：

```bash
# crontab -e
0 9 * * * cd /path/to/project && claude -p "/loop 修复昨晚 CI 失败的测试，每个开独立 worktree，修完让 reviewer agent 审，过了开 PR" >> .claude/loop.log 2>&1
```

> 注意：`claude -p` 每次起独立 session，`/loop` 任务会话范围、session 退出即停——这个示例靠系统 cron 次日重新触发，不是单个 loop 持续跑。要真正的无人值守持久调度，用 Routines 或 Desktop 计划任务。会话放后台（agent-view）可让 `/loop` 免终端继续跑。

完整流程：

```
定时器触发
  → MCP 拉 issue 列表 / CI 失败日志
  → 每个任务 git worktree add 开独立区
  → 派 worker subagent 修
  → 派 reviewer subagent 对照 CLAUDE.md 审
  → PreToolUse hook 把关危险操作
  → 通过就自动开 PR、更新工单
  → 没过的进 PROGRESS.md 等 人
  → 明天从 PROGRESS.md 继续
```

**你设计了一次，没有手动提示任何步骤。** Loop 的可靠性上限由底层 Harness（§1-§10）决定——验证"Loop 产出是否正确"的机制，必须比 Loop 本身先就位。

---

## 全局原则

### 可剥离

模型会升级。今天必要的 hook/中间件明天可能变死重。**加一段配置很简单，删一段也应该很简单。** 不要为当前模型的特定缺陷写复杂补救逻辑——模型把那个缺陷修掉之后，你的补救就成了纯累赘。每次模型大版本升级，审一遍 Harness 把没用的剥掉（操作步骤见 §9「模型升级时的 Harness 审计 SOP」）。

### signal placement：规则放哪才生效

同一条指令写在 CLAUDE.md、工具描述、工具返回结果里，效果可能相反。"读到整页就继续读"写进工具描述没用，原样搬进工具返回结果就生效——因为它出现在模型正在读的数据旁边。**调 harness 时别只问"规则该说什么"，要问"规则该出现在哪里才会被读到"。** 这条是贯穿四层的微观心法。

---

## 附录 A：Hooks 事件与 JSON 协议速查

**常用事件**（v2.1.200+，节选；标 ✓ 的能用退出码 2 或 `decision` 阻断）：

| 事件 | matcher | 阻断 |
|------|---------|------|
| `SessionStart` / `SessionEnd` | startup/resume/clear/compact | 否 |
| `UserPromptSubmit` ✓ | 无 | 是 |
| `PreToolUse` ✓ | 工具名 | 是 |
| `PostToolUse` | 工具名 | 否 |
| `Stop` ✓ / `SubagentStop` ✓ | 无 / agent_type | 是 |
| `Notification` | permission_prompt/idle_prompt | 否 |
| `PreCompact` ✓ | manual/auto | 是 |

> 另有 `PermissionRequest`、`PostToolBatch`、`TaskCreated/Completed`、`PreCompact`、`FileChanged`、`WorktreeCreate/Remove`、`Elicitation` 等更多事件，按需查官方文档。

**matcher 语法**：只含字母数字/空格/`|,` 时按字面量匹配（`"Bash"`、`"Edit|Write"`）；含其他字符按正则（`"mcp__.*__write.*"`）。

**JSON 输出字段**（退出码 0 + 纯 JSON stdout）：

```json
{
  "continue": false,                  // 停止整个处理流程
  "stopReason": "给用户看的原因",
  "systemMessage": "警告",             // 展示给用户
  "suppressOutput": true,              // 隐藏 hook 自己的 stdout
  "additionalContext": "注入上下文",    // 回灌给 Agent
  "hookSpecificOutput": {              // 事件专用决策
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow|deny|ask",
    "permissionDecisionReason": "为什么",
    "updatedInput": {}                 // 改写工具入参
  }
}
```

---

## 附录 B：配置文件放哪里

| 文件 | 位置 | 作用范围 |
|------|------|---------|
| `settings.json` | `~/.claude/` | 全局（所有项目） |
| `settings.json` | 项目 `.claude/` | 本项目（进 git，团队共享） |
| `settings.local.json` | 项目 `.claude/` | 本项目本机（进 .gitignore，个人覆盖） |
| `CLAUDE.md` | 项目根 / 子目录 | 所在目录及子目录 |
| `agents/*.md` | `~/.claude/` 或项目 `.claude/` | 全局 / 本项目 |
| `skills/<name>/SKILL.md` | `~/.claude/` 或项目 `.claude/` | 全局 / 本项目 |
| `commands/*.md` | `~/.claude/` 或项目 `.claude/` | 全局 / 本项目 |
| `.mcp.json` | 项目根 | 本项目（进 git） |

**原则**：团队共享的（CLAUDE.md、架构 hook、permissions 策略）放项目 `.claude/` 进 git；个人的（本地路径、私钥相关、个人偏好）放 `settings.local.json` 或 `~/.claude/`。

---

## 与其他文件的关系

| 我要…… | 看哪个 |
|---------|--------|
| 理解 Harness Engineering 是什么、为什么重要 | `harness-engineering-article.md` |
| 评估现有项目的 Harness 成熟度 | `harness-principles.md` |
| 动手给我的项目配 Claude Code Harness | 就这份 |
| 查某个配置的理论依据 | `harness-principles.md` → 对应维度的"来源"字段 |

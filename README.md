下面给你一份真正工程级的：Codex + Claude Code + cc-switch 三者协同工作流（Linux CLI 实战架构版）。

这不是“怎么用工具”，而是怎么把它们当一个系统来用。

🧠 0. 总体架构（你要先理解这个）

三者不是并列工具，而是三层系统：

            ┌────────────────────┐
            │     Claude Code    │
            │  🧠 架构 / 推理层   │
            └─────────┬──────────┘
                      │
                      ▼
            ┌────────────────────┐
            │     cc-switch      │
            │  🔀 状态 / 配置层   │
            │  (路由/恢复/同步)   │
            └─────────┬──────────┘
                      │
                      ▼
            ┌────────────────────┐
            │      Codex CLI     │
            │ ⚙️ 执行 / agent层   │
            └────────────────────┘
🧭 1. 三者定位（必须死记）
🧠 Claude Code（大脑）

负责：

架构设计
代码理解
重构方案
系统拆解
debug 思路

👉 一句话：

“告诉我怎么做”

⚙️ Codex CLI（执行器）

负责：

写代码
改文件
跑 shell
修 bug
自动 patch

👉 一句话：

“把它做出来”

🔀 cc-switch（中枢）

负责：

切 provider（Claude / Codex）
恢复状态
管理模型 cache
防止环境炸
WSL/CLI 启动协调

👉 一句话：

“保证系统能跑”

🔥 2. 标准工程工作流（核心）
🟦 Phase 1：Claude 做“规划”
claude -C . "分析这个项目并给出重构方案"

输出你要保存三样东西：

1. ARCHITECTURE.md
2. TASK_PLAN.md
3. REFACTOR_STEPS.md
📌 关键原则

Claude 不写代码，只做：

分模块
定接口
定目录结构
定风险点
🟨 Phase 2：cc-switch 固化环境

在切换 Codex 前：

cc-switch backup
cc-switch use codex

或者你当前系统等价操作：

确认 models_cache.json
确认 provider
确认 proxy
⚠️ 为什么这一步重要

因为 Codex 很容易出现：

model not found
provider mismatch
cache 丢失
🟩 Phase 3：Codex 执行实现
codex -C . "按照 ARCHITECTURE.md 实现 auth 模块"

或：

codex exec "实现 TASK_PLAN 第3步"
📌 Codex 的正确用法

不要：

“帮我写项目”

要：

“实现 auth/login API（按照 Claude 的设计）”
🟥 Phase 4：Claude 做 Review
claude -C . "/review"

或者：

claude "检查 Codex 修改是否符合架构设计"
🔁 3. 经典循环（工程主循环）

这是核心：

Claude（设计）
   ↓
cc-switch（状态锁定）
   ↓
Codex（实现）
   ↓
Claude（审查）
   ↓
Codex（修正）
🧩 4. 高级模式（工程级）
🟣 模式 A：Planner / Worker 模式
Claude = Planner
claude "拆解这个系统成10个任务"
Codex = Workers
codex "实现 task-03"
codex "实现 task-04"
🟠 模式 B：并行开发（强力）

用 git worktree：

git worktree add ../feature-a
git worktree add ../feature-b

然后：

cd ../feature-a
codex "实现支付模块"
cd ../feature-b
claude "设计支付架构"
🟡 模式 C：cc-switch failover 模式（关键）

当 Codex 出问题：

codex doctor

或者：

codex debug models --bundled > ~/.codex/models_cache.json

然后：

cc-switch restart
🧠 5. 文件协同规范（非常重要）

建议项目必须有：

/AGENTS.md        ← Codex 用
/CLAUDE.md        ← Claude 用
/ARCHITECTURE.md  ← Claude 输出
/TASKS.md         ← 分发任务
🔥 6. 分工黄金规则（核心）
❌ 错误方式
Claude 写代码
Codex 决策架构

👉 一定会乱

✅ 正确方式
工具	职责
Claude	想清楚
Codex	做出来
cc-switch	保持系统稳定
⚠️ 7. 最常见坑（你已经遇到）
❌ 1. Codex model not found

→ cache 丢

codex debug models --bundled > ~/.codex/models_cache.json
❌ 2. cc-switch 启动异常

→ WSL / systemd race

ExecStartPre=/bin/sleep 3
❌ 3. Claude 和 Codex 输出冲突

→ 没有统一 spec

👉 解决：

CLAUDE.md = spec
AGENTS.md = execution rules
🚀 8. 最佳工程级配置（推荐）
shell alias
alias cplan='claude -C .'
alias cexec='codex -C .'
alias creview='claude /review'
启动顺序
cc-switch restore
claude
codex
🧠 9. 一句话总结

Claude 负责“想清楚”，Codex 负责“做出来”，cc-switch 负责“别炸掉”。

🚀 如果你要再进一层

我可以帮你升级成：

👉 “三-agent 自动化系统（带任务分发 + 自恢复 + CI联动）”

那已经是：

自动 planner
自动 executor
自动 reviewer
自动 rollback

属于真正 production AI dev system 了。

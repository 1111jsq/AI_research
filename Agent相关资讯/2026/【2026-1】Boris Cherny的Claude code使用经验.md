Boris Cherny 也公开了自己的使用经验，希望给正在使用 Claude Code 的开发者带来参考：

我的配置可能会让你觉得很“普通”！Claude Code 开箱即用效果就很好，所以我个人几乎不做自定义。使用 Claude Code 并没有唯一正确的方式：我们故意把它设计成你可以随意使用、定制，甚至自由改造。Claude Code 团队的每个人用法都各不相同。

那我直接说说我的做法吧：

1. 我在终端里同时运行 5 个 Claude。我给标签页编号 1–5，并用系统通知来知道哪个 Claude 需要输入。https://code.claude.com/docs/en/terminal-config#iterm-2-system-notifications
    
2. 我也会在 http://claude.ai/code 上同时运行 5–10 个 Claude，与本地的 Claude 并行。当我在终端写代码时，经常会把本地会话交给网页端（用 &），或者手动在 Chrome 中启动新会话，有时我会在两者之间“瞬移”。每天早上和工作中，我也会用手机（Claude iOS 应用）启动几个会话，并稍后查看。
    
3. 我用 Opus 4.5 并开启思考模式处理所有事情。这是我用过的最强大的编程模型。虽然它比 Sonnet 更大、更慢，但因为需要干预更少、工具使用能力更强，最终效率通常比用小模型更高。
    
4. 我们团队共享一个 Claude Code 仓库的 http://CLAUDE.md。我们会将它提交到 git，团队每周多次贡献内容。每当发现 Claude 做错事情，我们就记录到 http://CLAUDE.md，这样 Claude 下次就不会再犯同样错误。其他团队会维护自己的 http://CLAUDE.md，每个团队都有责任保持文件更新。
    
5. 代码审查时，我经常在同事的 PR 上标记 @.claude，把需要记录的内容加入 http://CLAUDE.md。我们使用 Claude Code 的 Github Action（/install-github-action）来操作。这是我们对 @danshipper 的 Compounding Engineering 的一种实现方式。
    
6. 大多数会话从 Plan 模式开始（shift+tab 两次）。如果我的目标是写一个 Pull Request，我会先用 Plan 模式，并和 Claude 反复确认计划直到满意。然后我切换到自动接受修改模式，Claude 通常可以一次性完成。一个好的计划非常重要！
    
7. 我对每天会多次执行的“内部循环”工作流都会使用斜杠命令（slash commands）。这样可以避免重复提示，也让 Claude 能够使用这些工作流。命令会提交到 git，保存在 .claude/commands/ 下。例如，Claude 和我每天会用 /commit-push-pr 斜杠命令几十次。这个命令用内联 bash 预先计算 git status 和其他一些信息，使命令运行快速，并避免和模型来回交互（https://code.claude.com/docs/en/slash-commands#bash-command-execution）。
    
8. 我还经常使用一些子智能体：code-simplifier 会在 Claude 完成工作后简化代码，verify-app 有详细说明用于端到端测试 Claude Code，等等。和斜杠命令类似，我把子智能体看作是自动化我大多数 PR 中最常用的工作流。
    
9. 我们使用 PostToolUse hook 来格式化 Claude 生成的代码。Claude 通常开箱就能生成格式良好的代码，hook 处理最后 10%，避免 CI 中出现格式错误。
    
10. 我不使用 --dangerously-skip-permissions。相反，我用 /permissions 预先允许一些我确认在环境中安全的常用 bash 命令，避免不必要的权限提示。这些设置大多记录在 .claude/settings.json，并与团队共享。
    
11. Claude Code 会替我使用所有工具。它经常会在 Slack 上搜索和发消息（通过 MCP 服务器）、运行 BigQuery 查询以回答分析问题（使用 bq CLI）、从 Sentry 获取错误日志等。Slack MCP 配置提交在 .mcp.json，团队共享。
    
12. 对于耗时很长的任务，我会采取以下做法：
    
    (a) 任务完成后，用后台智能体提示 Claude 验证其工作；
    
    (b) 使用 agent Stop hook 更确定性地完成验证；
    
    (c) 使用 ralph-wiggum 插件（最初由 @GeoffreyHuntley 想出的）。
    
    我还会在沙箱环境中使用 --permission-mode=dontAsk 或 --dangerously-skip-permissions 来避免权限提示，让 Claude 可以自由运行而不被我阻挡。
    
13. 最后一个提示：想要从 Claude Code 得到优秀结果，最重要的事情之一就是——给 Claude 一种验证其工作的方式。如果 Claude 有这个反馈回路，最终结果的质量可以提升 2–3 倍。
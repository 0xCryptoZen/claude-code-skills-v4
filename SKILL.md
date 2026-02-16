---
name: claude-code
description: |
  Claude Code CLI 完整使用指南。用于代码编写、调试、重构、测试、提交等开发任务。
  支持终端、VS Code、桌面应用、Web、JetBrains 等多种环境。
version: 1.0.0
author: 0xCryptoZen
tags: [claude, code, cli, development, ai, coding]
triggers:
  - claude code
  - claude cli
  - code assistant
  - claude coding
---

# Claude Code CLI 使用指南

Claude Code 是一个强大的 AI 编码工具，支持读取代码库、编辑文件、运行命令。

## 安装

### macOS / Linux / WSL
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### Windows PowerShell
```powershell
irm https://claude.ai/install.ps1 | iex
```

### Homebrew (macOS)
```bash
brew install --cask claude-code
```

### npm
```bash
npm install -g @anthropic-ai/claude-code
```

## 某本命令

| 命令 | 说明 |
|------|------|
| `claude` | 启动交互式 REPL |
| `claude "query"` | 带初始提示启动 |
| `claude -p "query"` | 单次查询 |
| `claude -c` | 继续最近的会话 |
| `claude update` | 更新到最新版本 |

## CLI Flags

| Flag | 说明 |
|------|------|
| `--continue`, `-c` | 继续最近的会话 |
| `--dangerously-skip-permissions` | 跳过所有权限提示 |
| `--permission-mode plan` | 计划模式（只读分析） |
| `--allowedTools` | 允许的工具列表 |

## 常见工作流

### 1. 理解新代码库
```bash
cd /path/to/project
claude
> give me an overview of this codebase
```

### 2. 修复 Bug
```bash
claude "I'm seeing an error when I run npm test"
claude "suggest ways to fix it"
```

### 3. 重构代码
```bash
claude "find deprecated API usage"
claude "refactor utils.js to use modern JavaScript features"
```

### 4. 编写测试
```bash
claude "write tests for the auth module"
claude "run tests and fix any failures"
```

### 5. 创建 PR
```bash
claude "commit my changes with a descriptive message"
claude "create a PR for this feature"
```

### 6. 代码审查
```bash
claude "review my recent code changes for security issues"
claude "check for type errors"
```

## Plan Mode（计划模式）

用于复杂重构或多文件修改前的计划。

### 启动计划模式
```bash
claude --permission-mode plan
```

### 常见计划模式任务

```bash
claude --permission-mode plan -p "分析项目结构，但不要修改任何代码"
```

## 子代理（Subagents）

创建专用子代理处理特定任务：

### 查看可用子代理
```bash
> /agents
```

### 使用特定子代理
```bash
> use code-reviewer subagent to check auth module
```

### 创建自定义子代理
```bash
# 选择 "Create New subagent"
```

## MCP 集成

Model Context Protocol (MCP) 用于连接外部数据源。

### 配置 MCP
```bash
claude mcp
```

### 常见 MCP 服务器

- Google Drive
- Jira
- Slack
- 自定义工具

## 最佳实践

1. 明确描述任务
2. 提供上下文
3. 分步骤执行
4. 验证结果
5. 使用 CLAUDE.md 配置项目规范

## 调试技巧

```bash
# 启用调试
claude --debug "api,mcp"

# 查看 API 请求
claude --debug "api"

# 排除某些调试
claude --debug "!statsig,!file"
```

## 故障排除

| 问题 | 解决方案 |
|------|----------|
| 登录失败 | 运行 `claude` 重新登录 |
| 权限问题 | 检查 `--allowedTools` 设置 |
| 性能慢 | 使用更具体的提示 |

## 版本历史

### v1.0.0 (2026-02-16)
- 初始版本
- 基础命令和工作流
- Plan Mode
- 子代理系统

---

## 进阶：使用 tmux 运行 Claude Code

### Ralph 工作流（推荐）

参考 [Ralph for Claude Code](https://github.com/frankbria/ralph-claude-code) 项目：

```bash
# 安装 Ralph
curl -fsSL https://raw.githubusercontent.com/frankbria/ralph-claude-code/main/install.sh | bash

# 在项目中启用
cd your-project
ralph-enable

# 运行自动开发循环
ralph "你的任务描述"
```

### 核心功能

1. **智能退出检测** - 检测完成指标 + EXIT_SIGNAL
2. **tmux 集成** - 实时监控会话
3. **速率限制** - 防止 API 过度使用
4. **会话连续性** - 使用 --resume 保持上下文

---

### 手动 tmux 工作流

如果不用 Ralph，可以手动创建：

```bash
# 1. 创建 tmux 会话运行 Claude Code
SESSION_NAME="claude-$(date +%s)"
tmux new -d -s "$SESSION_NAME" "claude -p '你的任务'"

# 2. 等待执行
sleep 60

# 3. 查看输出
tmux capture-pane -t "$SESSION_NAME" -p | tail -50

# 4. 10分钟后删除
(sleep 600; tmux kill-session -t "$SESSION_NAME") &
```

### 交互式模式

```bash
# 创建交互式会话
tmux new -d -s claude-interact "claude"

# 附加查看
tmux attach -t claude-interact

# 发送命令到会话
tmux send-keys -t claude-interact "你的任务" Enter

# 捕获输出
tmux capture-pane -t claude-interact -p
```

### 检测任务完成

Ralph 使用双重条件退出：
1. **完成指标** - 如 `✅`, `完成2. **EXIT`, `completed`
_SIGNAL** - Claude 明确输出 `EXIT_SIGNAL: true`

```bash
# 检查输出是否包含完成信号
if echo "$output" | grep -q "EXIT_SIGNAL: true"; then
    echo "任务完成"
fi
```

### 注意事项

- tmux 会话名称要唯一
- 打印模式 (-p) 更适合自动化
- 使用 --resume 保持会话上下文
- 10分钟保留期便于调试

---

## 贡献

欢迎提交 PR 来添加更多 Claude Code CLI 使用技巧！

## 资源

- 文档: https://code.claude.com/docs
- CLI 参考: https://code.claude.com/docs/en/cli-reference
- 最佳实践: https://code.claude.com/docs/en/best-practices

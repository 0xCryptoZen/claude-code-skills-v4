---
name: claude-code
description: |
  Claude Code CLI 完整使用指南 - 持续迭代中
  包含安装、命令、工作流、Skills、子代理、MCP 集成等
version: 2.0.0
author: 0xCryptoZen
tags: [claude, code, cli, development, ai, coding]
triggers:
  - claude code
  - claude cli
  - code assistant
---

# Claude Code CLI 完整指南 v2.0

> 持续迭代中 - 基于官方文档和实践

## 目录

1. [安装](#安装)
2. [基础命令](#基础命令)
3. [工作流](#工作流)
4. [Skills](#skills)
5. [子代理](#子代理)
6. [MCP 集成](#mcp-集成)
7. [管道和自动化](#管道和自动化)
8. [OpenClaw 集成](#openclaw-集成)

---

## 安装

### macOS / Linux
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### Windows PowerShell
```powershell
irm https://claude.ai/install.ps1 | iex
```

### 验证
```bash
claude --version
claude auth status
```

---

## 基础命令

| 命令 | 说明 |
|------|------|
| `claude` | 启动交互式 REPL |
| `claude "query"` | 带初始提示启动 |
| `claude -p "query"` | 单次查询后退出 |
| `claude -c` | 继续最近的会话 |
| `claude -r "session" "query"` | 恢复指定会话 |
| `claude update` | 更新到最新版本 |

### CLI Flags

| Flag | 说明 |
|------|------|
| `--continue`, `-c` | 继续最近的会话 |
| `--dangerously-skip-permissions` | 跳过所有权限提示 |
| `--permission-mode plan` | 计划模式（只读分析） |
| `--allowedTools` | 允许的工具列表 |
| `--add-dir` | 添加额外工作目录 |
| `--debug` | 启用调试模式 |
| `--agent` | 指定子代理 |
| `--agents` | 定义自定义子代理 |

---

## 工作流

### 1. 理解新代码库
```bash
claude
> give me an overview of this codebase
> explain the main architecture patterns
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
claude "run them and fix any failures"
```

### 5. 创建 PR
```bash
claude "commit my changes with a descriptive message"
claude "create a PR for this feature"
```

### 6. Plan Mode（计划模式）
```bash
# 启动计划模式
claude --permission-mode plan

# 单次查询
claude -p --permission-mode plan "Analyze the auth system"
```

---

## Skills

Skills 扩展 Claude 的能力，可以自定义指令和命令。

### 创建 Skill

在 `~/.claude/skills/<skill-name>/SKILL.md` 创建：

```yaml
---
name: my-skill
description: 当需要做某事时使用
---

你的指令内容...
```

### Skill 位置

| 位置 | 路径 | 适用范围 |
|------|------|----------|
| 个人 | `~/.claude/skills/<name>/SKILL.md` | 所有项目 |
| 项目 | `.claude/skills/<name>/SKILL.md` | 当前项目 |

### 调用 Skill

```bash
# 自动调用
"帮我做某事" (匹配 description)

# 手动调用
/my-skill
```

---

## 子代理

子代理是专门处理特定任务的 AI 助手。

### 内置子代理

| 子代理 | 模型 | 用途 |
|--------|------|------|
| Explore | Haiku | 快速代码搜索（只读） |
| Plan | 继承主对话 | 研究和规划 |
| General-purpose | 继承主对话 | 复杂多步骤任务 |

### 使用子代理
```bash
# 让 Explore 子代理搜索代码
claude "use explore to find auth related files"

# 使用 /agents 命令查看和创建子代理
/agents
```

### 创建自定义子代理

```yaml
---
name: code-reviewer
description: 检查代码安全问题
model: sonnet
tools:
  - Read
  - Bash
---

你是一个代码审查员...
```

---

## MCP 集成

Model Context Protocol (MCP) 连接外部数据源。

### 配置 MCP
```bash
claude mcp
```

### 常用 MCP 服务器

- Google Drive
- Jira
- Slack
- GitHub
- 数据库
- 更多...

### 添加 MCP 服务器

```bash
# 使用 claude mcp 命令添加
claude mcp add <server-name>
```

---

## 管道和自动化

### 管道用法
```bash
# 处理管道内容
cat logs.txt | claude -p "explain these errors"

# 监控日志
tail -f app.log | claude -p "alert if you see anomalies"

# 批量操作
git diff main --name-only | claude -p "review for security issues"
```

### 自动化脚本
```bash
# 单次任务
claude -p "write tests for auth module"

# 继续会话
claude -c "finish the feature"
```

---

## OpenClaw 集成

通过 Claude Adapter 远程控制 Claude CLI。

### Adapter 仓库
- https://github.com/0xCryptoZen/adapter-everything

### 启动 Adapter
```bash
cd claude-adapter
npm install
npm run build
node server.js
```

### WebSocket 控制
```javascript
const WebSocket = require('ws');
const ws = new WebSocket('ws://127.0.0.1:8080');

ws.on('open', () => {
  // 创建会话
  ws.send(JSON.stringify({
    method: 'session.create',
    params: { workingDirectory: '/path/to/project' },
    id: 'create'
  }));
});

ws.on('message', (data) => {
  const msg = JSON.parse(data);
  if (msg.event?.type === 'output_delta') {
    console.log(msg.event.data);
  }
});
```

### 演示脚本
```bash
node demo-control.js
```

---

## 最佳实践

1. **明确描述任务** - 越具体越好
2. **提供上下文** - 包含错误信息、文件路径
3. **分步骤执行** - 复杂任务分解为小步骤
4. **验证结果** - 让 Claude 运行测试验证
5. **使用 CLAUDE.md** - 设置项目规范
6. **使用 Skills** - 封装常用工作流
7. **使用子代理** - 分离探索和实现

---

## 版本历史

### v2.0.0 (2026-02-16)
- 添加 Skills 系统
- 添加子代理详解
- 添加 MCP 集成
- 添加 OpenClaw 集成
- 添加管道和自动化

### v1.0.0 (2026-02-16)
- 初始版本
- 基础命令和工作流
- Plan Mode

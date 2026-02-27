# Copowers

基于 [Superpowers](https://github.com/obra/superpowers) 的开发工作流Skill集合，集成 [mcp-codex-dev](https://github.com/FYZAFH/mcp-codex-dev) 进行实际执行。

Superpowers 通过 composable skills 定义了一套完整的软件开发流程（头脑风暴 → 设计 → 计划 → 实现 → 审查 → 完成）。Copowers 在此基础上做了一个改动：**将 TDD 实现和代码审查完全委托给 mcp-codex-dev**，不再依赖子代理分派模式。

## 与 Superpowers 的区别

| | Superpowers | Copowers |
|---|---|---|
| 实现方式 | 分派 subagent 执行每个任务 | 调用 mcp-codex-dev `tdd` 工具 |
| 代码审查 | 分派 reviewer subagent（两阶段） | 调用 mcp-codex-dev `review` 工具（一次性 spec + quality） |
| 会话管理 | 每个 subagent 独立上下文 | Codex CLI session 支持 resume |
| 修复循环 | 重新分派 subagent | resume 同一 session 修复 |
| 并行代理 | 支持（dispatching-parallel-agents） | 移除（避免文件冲突） |

## 工作流程

```
brainstorming → writing-plans → using-git-worktrees
  → codex-driven-development:
      for each task:
        1. tdd tool（实现 + 测试）
        2. review tool（spec + quality 审查）
        3. 有问题 → resume session 修复 → 重新审查
  → finishing-a-development-branch
```

## 安装

### 前置条件

- Node.js（用于运行 mcp-codex-dev）
- [Codex CLI](https://github.com/openai/codex)

### 作为插件安装

```bash
# 添加 marketplace
/plugin marketplace add FYZAFH/copowers

# 安装
/plugin install copowers@copowers
```

## Skills 一览

**开发流程**
- **brainstorming** — 需求澄清与设计探索
- **writing-plans** — 生成分步实现计划（每步 2-5 分钟）
- **codex-driven-development** — 按计划逐任务执行 TDD + review
- **executing-plans** — 分批执行，带人工检查点
- **finishing-a-development-branch** — 验证测试、合并/PR/保留/丢弃

**质量保证**
- **test-driven-development** — RED-GREEN-REFACTOR，铁律：没有失败的测试就没有生产代码
- **requesting-code-review** — 通过 mcp-codex-dev review 工具审查
- **receiving-code-review** — 如何响应审查反馈
- **verification-before-completion** — 完成前验证

**工具与调试**
- **using-codex-dev** — mcp-codex-dev 工具参考（tdd / review / exec / session 管理）
- **using-git-worktrees** — 隔离工作区
- **systematic-debugging** — 四阶段根因分析

**元技能**
- **writing-skills** — 如何创建新 skill
- **using-superpowers** — skill 系统入门

## 致谢

- [Superpowers](https://github.com/obra/superpowers) by Jesse Vincent — 本项目的基础框架和 skill 体系

## License

MIT

---
name: using-codex-dev
description: Use when implementing code via TDD, reviewing code changes, or managing Codex development sessions
---

# Using mcp-codex-dev

MCP server wrapping Codex CLI for TDD execution, code review, and session management.

**Core principle:** Delegate implementation to Codex TDD, delegate review to Codex review. The orchestrator coordinates, Codex executes.

## Prerequisites

mcp-codex-dev must be configured as an MCP server. Verify with `mcp__mcp-codex-dev__health`.

Example `.mcp.json` (in project root or `~/.claude/.mcp.json`):

```json
{
  "mcpServers": {
    "mcp-codex-dev": {
      "command": "npx",
      "args": ["-y", "mcp-codex-dev"]
    }
  }
}
```

## When to Use Each Tool

| Situation | Tool |
|-----------|------|
| Implementing a task following TDD | `mcp__mcp-codex-dev__tdd` |
| Reviewing code changes (spec + quality) | `mcp__mcp-codex-dev__review` |
| Running a raw instruction (refactoring, config) | `mcp__mcp-codex-dev__exec` |
| Checking environment readiness | `mcp__mcp-codex-dev__health` |
| Listing active/completed sessions | `mcp__mcp-codex-dev__session_list` |
| Cleaning up old sessions | `mcp__mcp-codex-dev__session_discard` |

**Default to `tdd` for implementation.** Only use `exec` when TDD structure doesn't apply (e.g., config changes, refactoring without behavior change).

## Tool Reference

### tdd (Test-Driven Development)

| Parameter | Required | Description |
|-----------|----------|-------------|
| instruction | Yes | Full task description with context |
| planReference | No | Plan file path or content summary |
| taskContext | No | Position: "Task 3 of 5: Implement validation" |
| testFramework | No | jest, vitest, pytest, go test, etc. |
| sessionId | No | Resume existing session (UUID) |
| workingDirectory | No | Working directory path |

Returns: `sessionId`, `output.summary`, `output.filesModified`, `output.filesCreated`, `status`

### review (Code Review)

| Parameter | Required | Description |
|-----------|----------|-------------|
| instruction | Yes | Original task instruction |
| whatWasImplemented | Yes | Brief summary of implementation |
| baseSha | Yes | Base commit SHA |
| headSha | No | Target commit (default: HEAD) |
| reviewType | No | `spec`, `quality`, or `full` (default: `full`) |
| planReference | No | Plan file path or summary |
| taskContext | No | Task position within plan |
| sessionId | No | Resume existing review |
| workingDirectory | No | Git repo directory |
| testFramework | No | Test framework used |
| additionalContext | No | Additional review context |

Returns: `review` (text), `specSessionId`, `qualitySessionId` (in full mode)

### exec (Plain Execution)

| Parameter | Required | Description |
|-----------|----------|-------------|
| instruction | Yes | Raw instruction for Codex CLI |
| sessionId | No | Resume existing session |
| workingDirectory | No | Working directory path |

### session_list / session_discard / health

- `session_list`: Filter by `type` (write/review/exec/all) and `status` (active/completed/abandoned/all)
- `session_discard`: Pass `sessionIds` array, use `force: true` for active sessions
- `health`: Pass `workingDirectory`, optionally `checkGit` and `ensureTrackingDir`

## Session Management

1. **Store sessionId** after each `tdd` call
2. **Resume session** when review finds issues: pass stored `sessionId` + fix instructions to `tdd`
3. **Discard sessions** after task completion (optional cleanup)
4. **List sessions** to check for abandoned work: `session_list` with `status: "abandoned"`

## Review Fix Loop

```
tdd (implement) → review (full)
  │
  ├─ Approved → Done
  │
  └─ Issues found → Extract specific issues from review text
       → Resume tdd session: sessionId + "Fix: [issues]"
       → Re-review (same baseSha)
       → Loop (max 3 attempts)
       → 3 failures → Escalate to human
```

## Red Flags

**Never:**
- Skip review after tdd (every implementation needs review)
- Use `exec` when `tdd` is appropriate (bypasses TDD discipline)
- Ignore Critical or Important review issues
- Proceed past a failed review without fixing
- Call `tdd` in parallel for tasks that touch the same files

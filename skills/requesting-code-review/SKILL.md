---
name: requesting-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
---

# Requesting Code Review

Call mcp-codex-dev review tool to catch issues before they cascade.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**
- After each task in codex-driven-development
- After completing major feature
- Before merge to main

**Optional but valuable:**
- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

## How to Request

**1. Get baseSha:**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main, or recorded taskBaseSha
```

**2. Call review tool:**

```
mcp__mcp-codex-dev__review
  instruction: task/feature description (what should have been built)
  whatWasImplemented: brief summary of what was actually built
  baseSha: starting commit
  reviewType: "full"  (or "spec" / "quality" for targeted review)
  planReference: plan file path (if available)
  taskContext: "Task N of M: [name]" (if within a plan)
```

**Review types:**
- `full` (default) — spec compliance + code quality in one pass
- `spec` — only checks: did implementation match requirements?
- `quality` — only checks: is code well-built, tested, maintainable?

**3. Act on feedback:**
- Fix Critical issues immediately
- Fix Important issues before proceeding
- Note Minor issues for later
- Push back if reviewer is wrong (with reasoning)

## Example

```
[Just completed Task 2: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git rev-parse HEAD~3)  # commit before Task 2
HEAD_SHA=$(git rev-parse HEAD)

[Call mcp__mcp-codex-dev__review]
  instruction: "Task 2: Verification and repair functions for conversation index, support 4 issue types"
  whatWasImplemented: "Added verifyIndex() and repairIndex() with 4 issue types, 8 tests passing"
  baseSha: a7981ec
  reviewType: "full"
  planReference: "docs/plans/deployment-plan.md"
  taskContext: "Task 2 of 5: Verification function"

[Review returns]:
  Spec: Compliant
  Quality:
    Strengths: Clean architecture, real tests
    Issues:
      Important: Missing progress indicators
      Minor: Magic number (100) for reporting interval
    Assessment: Ready with fixes

You: [Fix progress indicators, extract constant]
[Re-review or continue to Task 3]
```

## Integration with Workflows

**Codex-Driven Development:**
- Review after EACH task (automatic in workflow)
- Review fix loop: fix → re-review until approved
- Final full-range review after all tasks

**Executing Plans:**
- Review after each batch (3 tasks)
- Get feedback, apply, continue

**Ad-Hoc Development:**
- Review before merge
- Review when stuck

**REQUIRED BACKGROUND:** superpowers:using-codex-dev for tool parameters

## Red Flags

**Never:**
- Skip review because "it's simple"
- Ignore Critical issues
- Proceed with unfixed Important issues
- Argue with valid technical feedback

**If reviewer wrong:**
- Push back with technical reasoning
- Show code/tests that prove it works
- Request clarification

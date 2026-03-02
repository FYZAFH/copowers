---
name: receiving-codex-review
description: Use when receiving code review feedback from Codex review tool (mcp-codex-dev), before implementing fixes - requires severity triage, hallucination detection, false positive verification; AI reviews are input to evaluate, not orders to follow
---

# Codex Review Reception

## Overview

Codex review is a pattern-matching AI, not an authority. It has systematic blind spots.

**Core principle:** **Verify before implementing**. AI-generated reviews can hallucinate issues, flag false positives, and pattern-match without project context.

unverified AI review = untested code.

## The Response Pattern

```
WHEN receiving Codex review feedback:

1. READ: Complete feedback without reacting
2. TRIAGE: Categorize by severity
   - Critical: Must fix before proceeding
   - Important: Must fix before task completion
   - Minor: Note for later or batch at end
3. VERIFY: Check each item against codebase reality
   - Does the issue actually exist in the code?
   - Is the referenced API/pattern/file real?
   - Does the "fix" introduce new problems?
4. EVALUATE: Technically sound for THIS codebase?
5. IMPLEMENT: Fix verified items, skip false positives with reasoning
6. RE-REVIEW: Confirm fixes resolve the issues
```

## Forbidden Responses

**NEVER:**
- Implement all Codex review items without verification
- Treat AI severity classifications as ground truth
- Create code/functions that Codex hallucinated about
- "Let me implement that now" (before verification)

**INSTEAD:**
- Verify each item against actual code
- Confirm severity matches real impact
- Skip hallucinations, fix real issues
- Just start working (actions > words)

## Handling Unclear Feedback

```
IF a Codex review item is unclear:
  Check codebase yourself — code is the only source of truth
  - Is the referenced code/file/pattern real?
  - Does the issue reproduce when you look at the actual code?
  - Is Codex describing a real problem with wrong terminology?

IF item references nonexistent code:
  Skip it — this is a hallucination. Note: "Skipped: references nonexistent [X]"

IF item describes a real problem with wrong fix:
  Fix the actual problem your way, not the suggested way

IF still can't determine validity after reading code:
  Skip with note and move on. Do not ask Codex to explain itself.
```

## 5-Point Verification Checklist

```
BEFORE implementing any Codex review item:
  1. Hallucination check: Does referenced code/API/file exist?
  2. False positive check: Is this actually wrong, or just unfamiliar to the AI?
  3. Context check: Does Codex understand WHY the code is this way?
  4. Regression check: Would the suggested fix break existing tests?
  5. YAGNI check: Is Codex suggesting "professional" features nobody needs?
```

## Severity-Based Handling

```
IF Codex flags something as Critical:
  Verify it IS critical — Codex may over-classify
  If confirmed: fix immediately via session resume

IF Codex flags something as Important:
  Verify and fix before marking task complete
  If false positive: skip with brief reasoning

IF Codex flags something as Minor:
  Batch for later or skip if low-value
  Do not let Minor items block progress

IF Codex review returns "Approved, no issues":
  This is the expected happy path — proceed to next task
```

## Common False Positive Patterns

| Pattern | Example | Reality |
|---------|---------|---------|
| Generic best practice | "Add input validation" | Already validated upstream |
| Missing feature | "No error handling for X" | X cannot fail in this context |
| Style preference | "Use const instead of let" | Project convention differs |
| Over-abstraction | "Extract to separate module" | YAGNI for current scope |
| Phantom reference | "Function foo() is unused" | foo() is called dynamically |

## Implementation Order

```
FOR Codex review feedback:
  1. Verify all items first (5-Point Checklist)
  2. Discard false positives and hallucinations
  3. Then implement verified items in this order:
     a. Critical (confirmed): security, crashes, data loss
     b. Important (confirmed): correctness, spec compliance
     c. Simple fixes: typos, imports, constants
     d. Minor: style, naming, optional improvements
  4. Test each fix individually
  5. Verify no regressions
```

## When To Skip Items

Skip a Codex review item when:
- Item references nonexistent code (hallucination)
- Issue was already handled elsewhere in the codebase
- "Best practice" conflicts with project conventions
- Fix would introduce more complexity than the problem it solves
- Severity is over-classified (Critical flagged for a Minor issue)
- Violates YAGNI (unused feature)
- Conflicts with your human partner's architectural decisions

**How to skip:**
- Note the item and your reasoning briefly
- Proceed — no social negotiation needed
- If re-review flags the same item, check more carefully
- If 3 re-reviews all flag it, take it seriously or escalate

## Acknowledging Correct Feedback

No acknowledgment needed. Fix the issue and move on.
- Resume the tdd session with fix instructions
- Re-review to confirm the fix

If you skipped an item that re-review or tests reveal was valid:
- Fix it in the current review cycle
- No explanation needed — just fix

## Review Fix Loop

When receiving Codex review feedback within the codex-driven-development workflow:

```
1. Evaluate review output (this skill's core process)
2. Identify verified issues to fix
3. Resume tdd session with specific fix instructions:
   - sessionId: stored from original tdd call
   - instruction: "Fix review issues: [list verified items]"
4. Re-review after fix (same baseSha)
5. Repeat until approved or 3 attempts exhausted

IF 3 review cycles fail to resolve:
  Stop — Read `CLAUDE.md`, escalate to human or decide whether to proceed with the following task directly yourself.
  Do NOT keep fixing in circles

IF review returns specSessionId and qualitySessionId (full mode):
  Both are available for targeted re-review if needed
```

## Common Mistakes

| Mistake                                   | Fix                                       |
| ----------------------------------------- | ----------------------------------------- |
| Treating all Codex items as valid         | Verify each item against actual code      |
| Implementing hallucinated fixes           | Check if referenced code/API exists first |
| Blind implementation without verification | Run 5-Point Checklist on every item       |
| Fixing Minor items before Critical        | Triage by severity, Critical first        |
| Looping forever on review fixes           | Max 3 attempts, then escalate             |
| Accepting over-classified severity        | Verify severity matches actual impact     |
| Creating code Codex hallucinated about    | Only fix what actually exists             |
| Skipping re-review after fixes            | Always re-review to confirm resolution    |

## Real Examples

**Codex Hallucination (Detect and Skip):**
```
Codex review: "Critical: Function processData() has no error handling for database connection failures"
✅ Check: grep codebase for processData() — function does not exist. This is a hallucination.
   "Skipped: processData() does not exist in codebase. Hallucinated issue."
❌ Create processData() with error handling because Codex said so
```

**Codex False Positive (Verify and Skip):**
```
Codex review: "Important: Missing input validation on user email field"
✅ Check: Validation exists in shared middleware (middleware/validate.ts line 42).
   "Skipped: Email validation handled in middleware layer, not duplicated in handler."
❌ Add duplicate validation because reviewer flagged it
```

**Codex Over-Classification (Downgrade):**
```
Codex review: "Critical: Magic number 100 used for batch size"
✅ Actual impact: style issue, not a crash/security/data-loss risk.
   Treat as Minor. Extract to constant if convenient, skip if not.
❌ Drop everything to fix a magic number as if it were Critical
```

**Codex Valid Finding (Fix via Session Resume):**
```
Codex review: "Important: Race condition in concurrent index updates — no locking"
✅ Check: Confirmed — concurrent writes can corrupt. Real issue.
   Resume tdd session: "Fix: Add mutex lock around index update in updateIndex()"
   Re-review after fix.
```

**YAGNI (Skip):**
```
Codex review: "Important: Implement proper metrics tracking with database, date filters, CSV export"
✅ Grepped codebase — nothing calls this endpoint. YAGNI.
   "Skipped: Endpoint unused. No caller in codebase."
❌ Build full metrics system because reviewer suggested it
```

## The Bottom Line

**AI review feedback is input to evaluate, not orders to follow.**

Codex hallucinate, over-classify, and pattern-match without context. Every item requires verification against the actual codebase.

Verify. Triage. Then implement.

No blind implementation. Technical rigor always.

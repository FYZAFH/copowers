---
name: requesting-human-help
description: Use when human participation would improve the outcome - visual/ui verification and test, physical interaction, hardware observation, subjective judgment, or any task where human involvement produces better results than AI acting alone
---

# Requesting Human Help

## Overview

Some tasks are better with human participation. This isn't escalation — it's collaboration.

**Core principle:** Request human help when their involvement improves the outcome, not only when you're stuck.

**Availability:** Your human partner is available by default. If CLAUDE.md declares them unavailable, record what you need and continue.

<extremely_important>
ALWAYS read CLAUDE.md before making any human request. Your human partner may have declared themselves unavailable, left standing instructions, or specified preferred contact methods. Requesting without reading CLAUDE.md first wastes their time or gets no response.
</extremely_important>

## When To Request

**Visual verification:**
- UI layout, styling, animation — does it look right?
- Design consistency across screens
- Responsive behavior at different sizes

**Physical interaction:**
- Click, scroll, type in a running application
- Reproduce a user-reported bug
- Test a flow that requires manual input

**Hardware observation:**
- Device behavior after connecting/disconnecting
- Mechanical movement (robotic arm, motor, servo)
- Sensor readings, LED states, physical indicators

**Environment operations:**
- Network configuration, firewall rules
- System settings that require GUI
- Physical connections (cables, USB devices)

**Subjective judgment:**
- "Is this good enough?"
- Priority when multiple valid approaches exist
- UX feel that metrics can't capture

## When NOT To Request

- You can verify by reading code or running tests
- You can run a command to check the result yourself
- You're uncertain about direction (use brainstorming instead)
- You want validation for a decision you can make

## Request Format

Be specific. Not "help me check this", but a concrete ask.

```
1. STATUS: What I've done so far
2. REQUEST: What I need you to do (specific steps)
3. FEEDBACK: What I need you to tell me
4. NEXT: What I'll do with your feedback
```

**Good:**
```
I've implemented the dashboard layout.
Please open http://localhost:3000/dashboard and check:
  1. Does the sidebar collapse cleanly on mobile width?
  2. Are the chart labels readable?
  3. Does the color scheme feel consistent?
Tell me which items need adjustment and I'll fix them.
```

**Bad:**
```
Can you check if the UI looks okay?
```

## Scenario Examples

**UI Review:**
```
STATUS: Completed responsive layout for settings page.
REQUEST: Open localhost:3000/settings on both desktop and phone.
FEEDBACK: Any elements that feel misaligned, overlapping, or hard to read.
NEXT: Fix reported issues, then continue to next task.
```

**Debug with Manual Steps:**
```
STATUS: Added logging for the checkout crash. Logs write to console.
REQUEST: Open the app, add 3 items to cart, click checkout, then paste the console output here.
FEEDBACK: The console output after clicking checkout.
NEXT: Analyze logs to identify the crash root cause.
```

**Hardware Observation:**
```
STATUS: Uploaded new firmware to the servo controller.
REQUEST: Power on the device and trigger the calibration sequence (hold button 3s).
FEEDBACK: Does the arm move to home position smoothly, or does it jerk/stall?
NEXT: Adjust PID parameters if movement is not smooth.
```

**Environment Setup:**
```
STATUS: Service needs port 8443 open for HTTPS.
REQUEST: Add firewall rule allowing inbound TCP 8443.
FEEDBACK: Confirm the rule is active.
NEXT: Run the integration test suite.
```

## If Human Is Unavailable

```
IF CLAUDE.md declares human unavailable:
  1. Record what you need (in TodoWrite or plan notes)
  2. Continue with what you CAN do independently
  3. Mark the task as blocked on human input
  4. Revisit when human becomes available
```

## After Human Responds

- Act on the feedback immediately
- If feedback is unclear, ask a specific follow-up — not "can you elaborate?"
- If the feedback changes your plan, update TodoWrite accordingly

## The Bottom Line

**Human involvement is a feature, not a fallback.**

Request help when it improves the outcome. Be specific. Act on the response.

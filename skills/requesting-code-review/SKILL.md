---
name: requesting-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
---

# Requesting Code Review

Dispatch `code-quality-reviewer` subagent to catch issues before they cascade. The reviewer checks both task/spec correctness and code quality. The reviewer gets precisely crafted context for evaluation — never your session's history. This keeps the reviewer focused on the work product, not your thought process, and preserves your own context for continued work.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**
- After completing major feature
- Before merge to main

**Optional but valuable:**
- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

## How to Request

**1. Gather review scope:**

Prefer reviewing the current task's workspace diff or changed files. Use git SHAs only if commits already exist and the user has explicitly chosen a commit-based workflow.

**2. Dispatch code-quality-reviewer subagent:**

Use Task tool with `code-quality-reviewer`, fill template at `code-quality-reviewer.md`

**Placeholders:**
- `{WHAT_WAS_IMPLEMENTED}` - What you just built
- `{PLAN_OR_REQUIREMENTS}` - What it should do / the task or spec it must satisfy
- `{DIFF_OR_CHANGED_FILES}` - Workspace diff, patch, or changed files for this review
- `{DESCRIPTION}` - Brief summary

**3. Act on feedback:**
- **REQUIRED SUB-SKILL:** Use superpowers:receiving-code-review before deciding whether to apply reviewer feedback
- Fix Critical issues immediately
- Fix Important issues before proceeding
- Note Minor issues for later
- Push back if reviewer is wrong (with reasoning)

## Example

```
[Just completed Task 2: Add verification function]

You: Let me request code review before proceeding.

[Dispatch code-quality-reviewer subagent]
  WHAT_WAS_IMPLEMENTED: Verification and repair functions for conversation index
  PLAN_OR_REQUIREMENTS: Task 2 from docs/plan/deployment-plan.md
  DIFF_OR_CHANGED_FILES: workspace diff for Task 2
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types

[Subagent returns]:
  Strengths: Clean architecture, real tests
  Issues:
    - [Important] Missing progress indicators
    - [Minor] Magic number (100) for reporting interval
  Testing Considerations: Existing tests pass, but progress reporting should be validated
  Recommendations: Add progress reporting and consider extracting the interval constant
  Assessment:
    - Ready to pass review? With fixes
    - Reasoning: Core implementation is solid, but the follow-up items should be addressed first.

You: [Fix progress indicators]
[Re-dispatch code-quality-reviewer]

[Subagent returns]:
  Strengths: Clean architecture, real tests, progress reporting added
  Issues: None
  Testing Considerations: Updated behavior is now covered
  Recommendations: None
  Assessment:
    - Ready to pass review? Yes
    - Reasoning: The follow-up fix resolves the remaining review issues.

[Continue to Task 3]
```

## Integration with Workflows

**Subagent-Driven Development:**
- Use this as the standard review step after implementation, or for final/cross-task review when needed
- Catch issues that span multiple tasks or affect the overall implementation
- Use `superpowers:receiving-code-review` to evaluate feedback before applying it

**Executing Plans:**
- Review after each batch (3 tasks)
- Get feedback, apply, continue
- Use `superpowers:receiving-code-review` when handling returned review comments

**Ad-Hoc Development:**
- Review before merge
- Review when stuck

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

See template at: requesting-code-review/code-quality-reviewer.md

**Related skill:** `superpowers:receiving-code-review` - use when review feedback comes back and you need to decide what to accept, reject, or defer

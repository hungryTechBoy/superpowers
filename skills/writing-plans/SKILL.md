---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write the single execution document for the approved design assuming the engineer has zero context for our codebase and questionable taste. The document must contain `Spec`, `Implementation Plan`, and `Todo`. Document everything they need to know: what the source of truth is, which files to touch for each task, code, testing, docs they might need to check, how to test it, and how to track progress. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the execution document."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Save output to:** `docs/plan/YYYY-MM-DD-<feature-name>.md`
- (User preferences for location override this default)

## Scope Check

If the approved design covers multiple independent subsystems, it should have been broken into sub-projects during brainstorming. If it wasn't, suggest breaking this into separate execution documents — one per subsystem. Each one should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Prepare the changes for review" - step

## Execution Document Structure

The output file is a single document with three top-level sections in this order:

1. `## Spec`
2. `## Implementation Plan`
3. `## Todo`

The `Spec` section is the source of truth and MUST use this structure:

```markdown
## Spec

### Goal
[What this change achieves]

### Scope
- [What is in scope]

### Non-Goals
- [What is explicitly out of scope]

### Solution
- [Core design and detailed change points]

### Error Handling
- [Failure modes and handling strategy]

### Acceptance Criteria
- [Core scenario or success criterion]

### Interfaces / Contract Changes
[Optional. Required if any internal/external API, RPC, contract, schema, or input/output shape changes. Use JSON for input/output examples.]
```

The `Implementation Plan` section MUST start with this header:

```markdown
# [Feature Name] Spec and Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Implementation Plan Task Structure

````markdown
### Task N: [Component Name]

**Execution:** Serial | Parallelizable

**Depends on:** None | Task N | Task N, Task M

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Prepare changes for review**

Summarize changed files, test results, and any review notes needed before code review.
````

## Todo Section

The document MUST end with a `## Todo` section that tracks task-level progress only:

```markdown
## Todo

- [ ] Task 1: [Component Name]
- [ ] Task 2: [Component Name]
```

The controller updates these checkboxes. Do not ask implementer or reviewer subagents to edit the todo list directly.

## Parallelization Guidance

When decomposing work, prefer tasks that can be executed in parallel when dependencies, interfaces, and file ownership allow it.

- Mark tasks `Parallelizable` only when they do not depend on unfinished work and are unlikely to create file conflicts.
- Mark tasks `Serial` when they define interfaces, shared contracts, migrations, or other prerequisites.
- Prefer independent tasks, but do not force parallelism when it increases merge or coordination risk.

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — the engineer may be reading tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Remember
- Exact file paths always
- Complete code in every step — if a step changes code, show the code
- Exact commands with expected output
- DRY, YAGNI, TDD

## Self-Review

After writing the complete document, look at the spec and plan with fresh eyes. This is a checklist you run yourself — not a subagent dispatch.

**1. Spec quality:** Check the `Spec` section for ambiguity, missing change points, incomplete error handling, or missing acceptance criteria.

**2. Plan coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**3. Placeholder scan:** Search the document for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**4. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

**5. Parallelization check:** Are tasks that could safely run in parallel marked accordingly? Are serial dependencies clearly called out?

**6. Todo sync:** Does every task appear once in the `Todo` section with matching numbering/title?

If you find issues, fix them inline. No need to re-review — just fix and move on. If you find a spec requirement with no task, add the task.

## Review Loop

After writing the complete document:

1. Dispatch `plan-document-reviewer` using `plan-document-reviewer-prompt.md`
2. The reviewer checks both the `Spec` and `Implementation Plan`
3. Treat reviewer feedback as advisory: validate it against the current document, accept sound findings, and push back on incorrect or out-of-scope feedback with explicit reasoning
4. If issues are found, fix them in the same document and re-dispatch
5. Repeat until approved or until you need human guidance

## Execution Handoff

After saving the document, offer execution choice:

**"Execution document complete and saved to `docs/plan/<filename>.md`. Two execution options:**

**1. Subagent-Driven (recommended)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** - Execute tasks without subagents using executing-plans, with review checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Fresh subagent per task + unified review

**If Inline Execution chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:executing-plans
- Batch execution with checkpoints for review

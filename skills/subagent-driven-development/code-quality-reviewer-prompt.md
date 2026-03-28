# Code Quality Reviewer Prompt Template

Use this template when dispatching a code quality reviewer subagent.

**Purpose:** Verify implementation both matches the task/spec and is well-built (clean, tested, maintainable)

```
Task tool (code-quality-reviewer):
  description: "Review code quality for Task N"
  prompt: |
    **Task / Requirement Context:** [Task N from plan-file]
    **What Was Implemented:** [from implementer's report]
    **Changed Files or Diff:** [changed files, patch, or workspace diff]
    **Working Directory:** [directory]
    **Description:** [task summary]
```

**In addition to standard code quality concerns, the reviewer should check:**
- Does the implementation actually satisfy the task/spec without major missing or extra behavior?
- Does each file have one clear responsibility with a well-defined interface?
- Are units decomposed so they can be understood and tested independently?
- Is the implementation following the file structure from the plan?
- Did this implementation create new files that are already large, or significantly grow existing files? (Don't flag pre-existing file sizes — focus on what this change contributed.)

**Code reviewer returns:** Strengths, Issues (Critical/Important/Minor), Testing Considerations, Recommendations, Assessment

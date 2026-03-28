# Plan Document Reviewer Prompt Template

Use this template when dispatching a plan document reviewer subagent.

**Purpose:** Verify the execution document is complete, the spec is a usable source of truth, and the implementation plan is executable.

**Dispatch after:** The complete execution document is written.

```
Task tool (plan-document-reviewer):
  description: "Review execution document"
  prompt: |
    **Document to review:** [DOCUMENT_FILE_PATH]

    ## Context

    [Optional scene-setting: feature scope, constraints, repository conventions]

    ## What to Check

    | Category | What to Look For |
    |----------|------------------|
    | Spec Quality | Goal, scope, non-goals, solution, error handling, and acceptance criteria are clear enough to serve as the source of truth |
    | Solution Soundness | The proposed solution is internally coherent, consistent with scope, and capable of satisfying the goal and acceptance criteria |
    | Contract Clarity | If APIs/contracts change, `Interfaces / Contract Changes` exists and uses JSON input/output examples |
    | Plan Alignment | Plan covers spec requirements, no major scope creep |
    | Task Decomposition | Tasks have clear boundaries, steps are actionable, parallelizable work is identified where appropriate |
    | Todo Quality | `Todo` has one entry per task and can be used by the controller to track completion |
    | Buildability | Could an engineer follow this document without getting stuck? |

    ## Calibration

    **Only flag issues that would cause real problems during implementation.**
    An implementer building the wrong thing or getting stuck is an issue.
    Minor wording, stylistic preferences, and "nice to have" suggestions are not.

    Approve unless there are serious gaps — unclear source-of-truth sections,
    unsound solution design, missing requirements from the spec, contradictory steps,
    placeholder content, missing contract details, or tasks so vague they can't be acted on.

    ## Controller Handling

    Your feedback is advisory. The controller must validate your findings against the
    current document and surrounding context. Do not assume every issue you raise will
    be applied mechanically.

    ## Output Format

    Status: Approved | Issues Found

    Summary:
    - [overall judgment of the execution document]

    Issues:
    - [Spec / Plan / Todo, section or task]: [specific issue] - [why it matters for implementation]

    Recommendations:
    - [suggestions for improvement]
```

**Reviewer returns:** Status, Summary, Issues, Recommendations

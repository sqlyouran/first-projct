---
name: QA: Review
description: |
  Trigger the QA agent to perform product acceptance on a completed
  OpenSpec change. Verifies the change is fully implemented before
  dispatching QA.
category: Workflow
tags: [workflow, agent, qa, review]
---

# /qa:review

Trigger the QA agent to perform product acceptance on a completed change.

**Usage**: `/qa:review <change-name>`

**Examples**:
- `/qa:review seo-basics-and-env-isolation`
- `/qa:review add-user-auth`

**Steps**

1. **Validate change exists**
   Verify `openspec/changes/<change-name>/` exists. If not:
   - List active changes: `openspec list --json`
   - Report error and stop

2. **Check completion status**
   Read `openspec/changes/<change-name>/tasks.md` and verify ALL checkboxes are `[x]`.
   If any tasks remain unchecked:
   - Report which tasks are incomplete
   - Refuse to proceed with QA review
   - Suggest: "Complete remaining tasks first, then re-run /qa:review"

3. **Dispatch QA agent**
   Invoke the `qa` agent with `change=<change-name>`.

4. **Present report**
   Display the QA report path (`docs/qa-reports/<YYYY-MM-DD>-<change-name>.md`)
   and summarize key findings (Critical/Medium/Low counts, top issues).

5. **Next steps**
   - If Critical issues found: recommend creating a fix change
   - If only Low issues: suggest accepting with follow-up changes
   - Ask user whether to create fix changes or accept

**Guardrails**
- Do NOT dispatch QA if tasks are incomplete
- Do NOT modify the change's code or specs during QA review
- QA reports are append-only; do not overwrite existing reports

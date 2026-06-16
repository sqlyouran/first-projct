---
name: PM
description: |
  Trigger the Product Manager agent to draft an OpenSpec change for a
  specific module. Validates the module exists, dispatches the PM agent,
  and presents the draft for user review.
category: Workflow
tags: [workflow, agent, pm, proposal]
---

# /pm

Trigger the Product Manager agent to draft an OpenSpec change.

**Usage**: `/pm <module> "<requirement>"`

**Examples**:
- `/pm patient-community "add AMA feature"`
- `/pm medical-directory "add hospital photo gallery"`

**Steps**

1. **Validate module exists**
   Check that `docs/modules/<module>.md` exists. If not:
   - List available modules: `ls docs/modules/*.md`
   - Report error and stop

2. **Dispatch PM agent**
   Invoke the `product-manager` agent with:
   - `module=<module>`
   - `requirement=<user's requirement text>`

3. **Present draft for review**
   Display the PM agent's output (proposal draft + spec draft + tasks draft)
   to the user. Do NOT create files yet.

4. **Wait for user approval**
   - If approved: proceed to create the change with `/opsx:propose` or manual file creation
   - If changes requested: relay feedback to PM agent for revision
   - If rejected: discard draft, report cancellation

**Guardrails**
- Do NOT create files without user approval
- Do NOT auto-execute `openspec new change`
- If the requirement is vague, ask clarifying questions before dispatching PM

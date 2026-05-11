# Skill: Feature Development

## Trigger
Activated when starting implementation of a new feature from an approved spec.

## Process

### Hard Gates
1. **NEVER** start coding without an approved proposal
2. **NEVER** deviate from the design without updating the spec
3. **ALWAYS** break work into small, testable increments

### Workflow
1. **Verify Spec**: Confirm proposal and design are approved
2. **Plan Tasks**: Break into small, atomic tasks (each < 1 hour of work)
3. **Implement Incrementally**:
   - For each task:
     a. Write failing test (invoke TDD skill)
     b. Implement minimum code
     c. Refactor if needed
     d. Mark task complete
4. **Integration**: Verify all components work together
5. **Review**: Invoke code-review skill
6. **Archive**: Move spec to archive when feature is complete

### Principles
- One logical change per commit
- Keep PRs small and focused
- Update spec if requirements change mid-implementation
- Communicate blockers early

# Skill: Test-Driven Development (TDD)

## Trigger
Automatically activated when implementing any new feature or fixing bugs.

## Process

### Hard Gates
1. **NEVER** write implementation code before writing a failing test
2. **NEVER** mark a task complete without all tests passing
3. **ALWAYS** follow Red → Green → Refactor cycle

### Workflow
1. **Red**: Write a failing test that describes the desired behavior
2. **Green**: Write the minimum code to make the test pass
3. **Refactor**: Clean up the code while keeping tests green

### Enforcement
- Before writing any implementation, ask: "Where is the failing test?"
- After implementation, run the full test suite
- If tests fail, fix before proceeding

## Quality Criteria
- Test coverage for all new code paths
- Tests are readable and serve as documentation
- No test interdependencies

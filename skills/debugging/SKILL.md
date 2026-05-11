# Skill: Debugging

## Trigger
Activated when encountering bugs, errors, or unexpected behavior.

## Process

### Hard Gates
1. **NEVER** apply random fixes without understanding root cause
2. **NEVER** suppress errors without handling them
3. **ALWAYS** write a regression test for the bug

### Workflow
1. **Reproduce**: Confirm the bug with a minimal reproduction case
2. **Isolate**: Narrow down the source using bisection/logging
3. **Root Cause**: Identify WHY the bug exists, not just WHERE
4. **Fix**: Apply minimal, targeted fix
5. **Verify**: Confirm fix resolves the issue without side effects
6. **Prevent**: Add regression test and consider systemic improvements

### Diagnostic Tools
- Read error logs and stack traces carefully
- Add targeted logging/breakpoints
- Check recent changes (git log/diff)
- Verify assumptions about data state

## Output
Document the bug with:
- Symptoms observed
- Root cause identified
- Fix applied
- Regression test added

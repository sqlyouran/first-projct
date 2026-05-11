# Skill: Code Review

## Trigger
Activated after completing any implementation task, before merging.

## Process

### Hard Gates
1. **NEVER** merge without review
2. **NEVER** skip security checks
3. **ALWAYS** verify spec compliance

### Checklist
- [ ] Code matches the approved design/spec
- [ ] No security vulnerabilities (OWASP Top 10)
- [ ] Error handling is appropriate
- [ ] No unnecessary complexity
- [ ] Tests cover the changes adequately
- [ ] No hardcoded secrets or credentials
- [ ] Performance implications considered

### Review Dimensions
1. **Correctness**: Does the code do what the spec says?
2. **Security**: Are there injection points, auth bypasses, data leaks?
3. **Performance**: Any N+1 queries, memory leaks, blocking calls?
4. **Maintainability**: Is the code readable and well-structured?
5. **Test Coverage**: Are edge cases tested?

## Output
Provide structured feedback with severity levels:
- 🔴 **Critical**: Must fix before merge
- 🟡 **Warning**: Should fix, acceptable to defer
- 🟢 **Suggestion**: Nice-to-have improvement

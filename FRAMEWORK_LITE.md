# Framework Lite: Streamlined Development for Small Tasks

## When to Use Framework Lite

Use this lightweight version for:
- ✅ Small bug fixes (< 50 lines of code)
- ✅ Simple feature additions
- ✅ Documentation updates
- ✅ Configuration changes
- ✅ Routine maintenance tasks

**Escalate to Full Framework when:**
- ❌ Task involves security-sensitive code
- ❌ Task affects system architecture
- ❌ Task touches multiple components
- ❌ Task requires database changes
- ❌ You're unsure about complexity

---

## Lite Task Prompt

**Task:** [Brief description]

**Estimated Complexity:** [Small/Medium - escalate to full framework if Large]

### Quick Workflow

**1. Understand & Plan (2 minutes)**
- [ ] Restate task in your own words
- [ ] Create simple plan with `todo_write`
- [ ] Get human approval if needed

**2. Gather Context (5 minutes)**
- [ ] Use `codebase_search` to find relevant files
- [ ] Use `read_file` to understand what you're changing
- [ ] Use `grep_search` if changing function/variable names

**3. Implement with Core Standards**
- [ ] **Code Quality:** Keep it clean, readable, and documented
- [ ] **Testing:** Add tests if logic changes
- [ ] **Error Handling:** Handle obvious failure cases

**4. Verify & Complete**
- [ ] Run relevant tests
- [ ] Check that change works as expected
- [ ] Present work for review

---

## Escalation Triggers

**Stop and use Full Framework if you encounter:**

🚨 **Security Concerns**
- Authentication/authorization logic
- Data validation/sanitization
- External API interactions
- User input processing

🚨 **Architecture Impact**
- Database schema changes
- New service dependencies
- Performance implications
- Breaking API changes

🚨 **Cross-System Effects**
- Changes affecting multiple services
- Deployment configuration changes
- Environment-specific logic
- Monitoring/logging changes

---

## Quick Reference

**Need Full Framework?** Use [`AI_ORCHESTRATION_PROMPT.md`](AI_ORCHESTRATION_PROMPT.md)

**Need Navigation Help?** Check [`FRAMEWORK_INDEX.md`](FRAMEWORK_INDEX.md)

**Core Standards:**
- [`CODE_QUALITY_STRATEGY.md`](CODE_QUALITY_STRATEGY.md) - Keep code clean
- [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md) - Test your changes

**Framework Not Working?** Add insights to [`LEARNINGS.md`](LEARNINGS.md) 
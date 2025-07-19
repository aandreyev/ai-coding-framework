# AI Orchestration Prompt & Task Guide

## Objective
This is your **active workflow controller**. Copy the template below, fill in the task, and use it to start every development activity. This prompt ensures structured, principled development while managing cognitive load.

---

## Master Task Prompt Template

**Task:** [One-sentence description of what you need to accomplish]

**Task Type:** [Select one: Feature Development / Bug Fix / Refactoring / Architecture Change / Security Enhancement / Performance Optimization / Other]

---

### Phase 1: Context & Planning
- [ ] **Understand:** Restate the task in your own words
- [ ] **Business Context:** Check [`PRODUCT_REQUIREMENTS_DOCUMENT.md`](PRODUCT_REQUIREMENTS_DOCUMENT.md) for relevant requirements
- [ ] **Technical Context:** Review [`TECHNICAL_SPECIFICATION.md`](TECHNICAL_SPECIFICATION.md) for architectural guidelines
- [ ] **Create Plan:** Use `todo_write` tool to create step-by-step plan
- [ ] **Get Approval:** Present plan and wait for human confirmation

### Phase 2: Information Gathering
- [ ] **Explore:** Use `list_dir` and `codebase_search` to understand existing code
- [ ] **Read:** Use `read_file` to understand files you'll modify
- [ ] **Dependencies:** Use `grep_search` to find all related code

### Phase 3: Implementation
**Core Standards (Always Apply):**
- [ ] **Code Quality:** Follow [`CODE_QUALITY_STRATEGY.md`](CODE_QUALITY_STRATEGY.md) standards
- [ ] **Testing:** Apply [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md) practices

**Context-Sensitive Strategies (Apply Based on Task Type):**

**🔐 Security-Critical Tasks** (Authentication, Data Handling, External APIs)
- [ ] Review [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md)
- [ ] Review [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md)
- [ ] Review [`LOGGING_STRATEGY.md`](LOGGING_STRATEGY.md)

**🛠 System Architecture Tasks** (Database Changes, Service Integration)
- [ ] Review [`DEPLOYMENT_STRATEGY.md`](DEPLOYMENT_STRATEGY.md)
- [ ] Review [`DATA_MIGRATION_STRATEGY.md`](DATA_MIGRATION_STRATEGY.md)
- [ ] Review [`MONITORING_STRATEGY.md`](MONITORING_STRATEGY.md)

**📊 Data-Intensive Tasks** (Database Operations, Data Processing)
- [ ] Review [`DATA_MIGRATION_STRATEGY.md`](DATA_MIGRATION_STRATEGY.md)
- [ ] Review [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md)
- [ ] Review [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md)

**🚀 Production Tasks** (Deployment, Performance, Monitoring)
- [ ] Review [`DEPLOYMENT_STRATEGY.md`](DEPLOYMENT_STRATEGY.md)
- [ ] Review [`MONITORING_STRATEGY.md`](MONITORING_STRATEGY.md)
- [ ] Review [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md)

### Phase 4: Verification
- [ ] **Test:** Run appropriate tests per [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md)
- [ ] **Verify:** Check that all requirements are met
- [ ] **Review:** Present completed work for human review

### Phase 5: Knowledge Capture
- [ ] **Learn:** Any insights for [`LEARNINGS.md`](LEARNINGS.md)?
- [ ] **Document:** Update relevant documentation if needed

---

## Quick Reference

**Feeling Overwhelmed?** Start with just the core standards (Code Quality + Testing) and escalate to context-sensitive strategies only when needed.

**Can't Find Something?** Check [`FRAMEWORK_INDEX.md`](FRAMEWORK_INDEX.md) for navigation help.

**Framework Not Working?** Log the issue in [`LEARNINGS.md`](LEARNINGS.md) for continuous improvement. 
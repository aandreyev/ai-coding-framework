# AI Coding Framework Index

This document serves as the master navigation and reference guide for the AI Coding Framework. Use this index to understand the framework structure, locate relevant documents, and determine the appropriate guidance for your development tasks.

## Framework Overview

### Core Philosophy
**Human as Architect, AI as Builder** - A collaborative approach where humans provide strategic direction and AI handles detailed implementation following established patterns and principles.

### Framework Tiers
The framework is organized in three tiers to manage cognitive load and provide appropriate guidance:

**Tier 1: Essential Core (Always Referenced)**
- [`AI_ORCHESTRATION_PROMPT.md`](AI_ORCHESTRATION_PROMPT.md) - Master prompt for all development tasks
- [`coding_principles.md`](coding_principles.md) - Foundational principles and collaboration model
- [`FRAMEWORK_LITE.md`](FRAMEWORK_LITE.md) - Lightweight version for simple tasks

**Tier 2: Project Foundation (Referenced by Context)**  
- [`README.md`](README.md) - Framework introduction and getting started guide
- [`framework_implementation_guide.md`](framework_implementation_guide.md) - Setup and usage instructions
- [`DEVELOPMENT_TOOLKIT.md`](DEVELOPMENT_TOOLKIT.md) - Development environment setup
- [`FOUNDATION_SETUP.md`](FOUNDATION_SETUP.md) - Core environment principles and validation

**Tier 3: Specialized Strategies (Referenced on Demand)**
- [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md) - Security patterns and implementation
- [`CODE_QUALITY_STRATEGY.md`](CODE_QUALITY_STRATEGY.md) - Code quality standards and practices
- [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md) - Comprehensive testing approaches  
- [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md) - Error handling patterns
- [`DEPLOYMENT_STRATEGY.md`](DEPLOYMENT_STRATEGY.md) - CI/CD and deployment practices

## Context-Sensitive Navigation

### Starting a New Task
1. **Begin with:** [`AI_ORCHESTRATION_PROMPT.md`](AI_ORCHESTRATION_PROMPT.md)
2. **Review principles:** [`coding_principles.md`](coding_principles.md) 
3. **Reference strategies:** Based on task requirements (see guide below)

### Task-Type to Strategy Mapping

**Feature Development:**
- Primary: [`CODE_QUALITY_STRATEGY.md`](CODE_QUALITY_STRATEGY.md), [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md)
- Secondary: [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md), [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md)

**Security Implementation:**
- Primary: [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md)
- Secondary: [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md), [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md)

**Bug Fixes & Debugging:**
- Primary: [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md), [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md)
- Secondary: [`CODE_QUALITY_STRATEGY.md`](CODE_QUALITY_STRATEGY.md)

**Deployment & Infrastructure:**
- Primary: [`DEPLOYMENT_STRATEGY.md`](DEPLOYMENT_STRATEGY.md)
- Secondary: [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md), [`ENVIRONMENT_SETUP.md`](ENVIRONMENT_SETUP.md)

**Environment Setup:**
- Primary: [`DEVELOPMENT_TOOLKIT.md`](DEVELOPMENT_TOOLKIT.md), [`FOUNDATION_SETUP.md`](FOUNDATION_SETUP.md)
- Secondary: [`MCP_STRATEGY.md`](MCP_STRATEGY.md), [`ENVIRONMENT_SETUP.md`](ENVIRONMENT_SETUP.md)

## Framework Validation

### Quality Indicators
- **Consistent Velocity:** Development tasks complete efficiently with predictable timelines
- **Low Defect Rate:** Minimal bugs reaching production due to systematic quality practices
- **High Code Quality:** Maintainable, readable code that follows established patterns
- **Security Compliance:** No security vulnerabilities in production code
- **Team Satisfaction:** Developers report positive experience with AI collaboration

### Dependency Mapping
- **All Tasks** → [`AI_ORCHESTRATION_PROMPT.md`](AI_ORCHESTRATION_PROMPT.md) → [`coding_principles.md`](coding_principles.md)
- **Strategy Documents** → Context-dependent based on task type and requirements
- **Environment Setup** → [`DEVELOPMENT_TOOLKIT.md`](DEVELOPMENT_TOOLKIT.md) → [`FOUNDATION_SETUP.md`](FOUNDATION_SETUP.md)

### Escalation Triggers
Switch from Framework Lite to Full Framework when:
- Project complexity exceeds simple CRUD operations
- Multiple team members involved
- Security or compliance requirements present
- Integration with external systems required
- Performance optimization needed

## Document Quick Reference

| Document | Lines | Purpose | When to Use |
|----------|-------|---------|-------------|
| AI_ORCHESTRATION_PROMPT | 71 | Master prompt template | Every development task |
| coding_principles | 151 | Core principles & standards | Framework onboarding, documentation updates |
| FRAMEWORK_LITE | 84 | Lightweight framework | Simple tasks, solo development |
| SECURITY_STRATEGY | 218 | Security implementation | Security features, compliance requirements |
| CODE_QUALITY_STRATEGY | 250 | Code standards & practices | Code reviews, refactoring |
| TESTING_STRATEGY | 230 | Testing approaches | Test implementation, QA processes |
| ERROR_RESILIENCE_STRATEGY | 240 | Error handling patterns | Bug fixes, system reliability |
| DEPLOYMENT_STRATEGY | 230 | CI/CD & deployment | Infrastructure, release management |

## Framework Maintenance

### Documentation Governance
**Owner:** Framework maintainers and contributing developers
**Standards:** Defined in [`coding_principles.md`](coding_principles.md) - Documentation Standards section

### Document Update Protocol

**Before Making Changes:**
1. **Review Impact:** Assess if changes affect multiple documents or framework consistency
2. **Check Standards:** Ensure compliance with documentation standards from [`coding_principles.md`](coding_principles.md)
3. **Validate Length:** Confirm document stays within 150-250 line target for medium complexity
4. **Test Examples:** Verify all code examples are functional and current

**During Updates:**
1. **Use AI-Directed Language:** Follow Rule/Pattern/Implementation format
2. **Maintain Cross-References:** Update related document links and dependencies  
3. **Preserve Structure:** Maintain consistent section organization
4. **Focus on Actionability:** Emphasize practical patterns over theoretical discussion

**After Changes:**
1. **Update Index:** Modify this document if new dependencies or relationships created
2. **Validate Links:** Ensure all cross-references remain functional
3. **Test Integration:** Verify changes work within overall framework workflow
4. **Document Learnings:** Record insights in [`LEARNINGS.md`](LEARNINGS.md)

### Quality Gates for Documentation Changes
All documentation updates must pass these gates:

- [ ] **Length Compliance:** Document remains within 150-250 line target
- [ ] **Language Standards:** Uses consistent AI-directed language patterns
- [ ] **Cross-Reference Integrity:** All links are valid and necessary
- [ ] **Practical Focus:** Emphasizes actionable guidance over theory
- [ ] **Framework Consistency:** Aligns with core principles and overall architecture
- [ ] **Example Validation:** All code examples are tested and functional

### Regular Maintenance Schedule

**Monthly Review:**
- Check for documents exceeding length targets
- Validate code examples remain current
- Review and address feedback from [`LEARNINGS.md`](LEARNINGS.md)

**Quarterly Assessment:**  
- Complete framework effectiveness review
- Update cross-references and dependencies
- Optimize document relationships and navigation
- Plan framework improvements based on usage patterns

**Annual Framework Audit:**
- Comprehensive review of all documentation
- Major framework updates and improvements
- Performance metrics analysis and optimization
- Long-term strategic planning for framework evolution

### Change Management Process

**Minor Updates (< 25% of document):**
- Direct updates following documentation standards
- Update cross-references as needed
- Record changes in [`LEARNINGS.md`](LEARNINGS.md)

**Major Updates (> 25% of document):**
- Create proposal documenting rationale and scope
- Review impact on dependent documents
- Coordinate updates across related documents
- Validate framework integrity after changes

**Framework-Wide Changes:**
- Document comprehensive change plan
- Identify all affected documents and dependencies
- Implement changes systematically with validation at each step
- Update this index and navigation guidance as needed

For detailed documentation standards and principles, see [`coding_principles.md`](coding_principles.md) - Documentation Standards section. 
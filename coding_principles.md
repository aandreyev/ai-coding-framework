# Core Coding Principles

This document establishes the foundational principles for AI-assisted development collaboration. These principles guide all development decisions and ensure consistent, high-quality outcomes.

## Human-AI Collaboration Model

### Human Role: Architect & Strategist
**You are the architect.** Your role is to:
- Define business requirements and user needs
- Make strategic technical decisions 
- Review and validate AI-generated solutions
- Ensure code meets business objectives
- Guide overall system design and architecture

### AI Role: Builder & Implementer  
**AI is the builder.** The AI's role is to:
- Implement solutions based on your specifications
- Generate code following established patterns
- Research and suggest technical approaches
- Handle detailed implementation tasks
- Provide comprehensive documentation and testing

## Core Development Principles

### 1. Start with Why
**Principle:** Every development task begins with understanding the business purpose and user value.

**Implementation:**
- Define clear user stories before writing code
- Validate that technical solutions address real business needs
- Prioritize features based on user impact
- Document the reasoning behind technical decisions

### 2. Iterate and Verify
**Principle:** Build incrementally with continuous validation at each step.

**Implementation:**
- Break large features into small, testable increments
- Validate each increment with stakeholders
- Use automated testing to catch regressions
- Deploy frequently to get real user feedback

### 3. Environment Parity
**Principle:** Development, staging, and production environments should be as similar as possible.

**Implementation:**
- Use containerization for consistent environments
- Maintain identical configuration patterns across environments
- Test deployment processes in staging before production
- Use Infrastructure as Code for reproducible environments

### 4. Security by Design
**Principle:** Security considerations are integrated from the beginning, not added later.

**Implementation:**
- Include security requirements in initial design
- Use secure coding practices and frameworks
- Implement defense in depth strategies
- Regular security testing and code reviews

### 5. Simple Solutions First
**Principle:** Choose the simplest solution that meets the requirements.

**Implementation:**
- Avoid over-engineering for uncertain future needs
- Use proven technologies and patterns
- Minimize dependencies and complexity
- Refactor when requirements actually change

## AI-Specific Guidelines

### For AI Assistants
When implementing solutions, always:

1. **Follow the Orchestration Process**
   - Use [`AI_ORCHESTRATION_PROMPT.md`](AI_ORCHESTRATION_PROMPT.md) for structured task handling
   - Reference appropriate strategy documents based on task type
   - Maintain context awareness throughout development phases

2. **Maintain Code Quality Standards**
   - Follow patterns from [`CODE_QUALITY_STRATEGY.md`](CODE_QUALITY_STRATEGY.md)
   - Implement comprehensive error handling per [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md)
   - Include security measures from [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md)

3. **Document Everything**
   - Provide clear explanations for all technical decisions
   - Include usage examples and test cases
   - Update relevant documentation when making changes
   - Follow documentation standards (see below)

### For Human Architects
When working with AI:

1. **Provide Clear Context**
   - Define business requirements clearly
   - Specify constraints and priorities
   - Share relevant background information
   - Review and validate AI-generated solutions

2. **Guide Strategic Decisions**
   - Make architectural and technology choices
   - Set quality and performance requirements
   - Define security and compliance needs
   - Validate business logic and user experience

## Documentation Standards

### Core Documentation Principles
**Purpose:** Maintain consistent, usable documentation that serves both humans and AI assistants effectively.

### 1. Optimal Length Standard
**Rule:** Target 150-250 lines per document for medium complexity guidance.

**Rationale:**
- AI context window limitations require manageable document sizes
- Sufficient depth for complex codebases without cognitive overload
- Consistent experience across all framework documents

**Implementation:**
- **Under 150 lines:** Expand with practical examples and implementation details
- **Over 250 lines:** Condense by removing verbose explanations, focusing on actionable patterns
- **For extremely complex topics:** Split into focused sub-documents rather than exceeding limits

### 2. AI-Directed Language
**Rule:** Write documentation as clear directives for AI assistants.

**Language Patterns:**
- **"Rule:"** for non-negotiable requirements
- **"Pattern:"** for recommended implementation approaches  
- **"Implementation:"** for specific steps and examples
- **"Checklist:"** for validation and verification steps

**Examples:**
```markdown
# DO: Clear, actionable directive
**Rule:** Validate all inputs at system boundaries before processing.

# DON'T: Vague, discussion-oriented
**Consideration:** Input validation is something to think about.
```

### 3. Practical Focus Over Theory
**Rule:** Emphasize actionable patterns with working code examples.

**Structure:**
- Lead with practical code examples
- Provide minimal context, maximum implementation guidance
- Include "DO/DON'T" comparison patterns
- End with quick reference summaries

### 4. Consistent Document Structure
**Rule:** Follow standardized sections across all strategy documents.

**Standard Structure:**
1. **Core Principles** - 3-5 fundamental rules with examples
2. **Implementation Patterns** - Practical code patterns and examples  
3. **Guidelines/Checklists** - Actionable verification steps
4. **Quick Reference** - Summary for rapid lookup

### 5. Document Maintenance Guidelines
**Rule:** Regular review and optimization to prevent documentation bloat.

**Maintenance Process:**
- **Monthly:** Review documents exceeding 250 lines for condensing opportunities
- **Quarterly:** Validate all examples and code snippets are current
- **After Major Changes:** Update cross-references and ensure consistency
- **Annual:** Complete framework review and optimization

### 6. Cross-Reference Management
**Rule:** Maintain clear connections between related documents without duplication.

**Implementation:**
- Link to related documents rather than duplicating content
- Use consistent reference format: `[DOCUMENT_NAME.md](DOCUMENT_NAME.md)`
- Avoid deep nesting of document dependencies
- Ensure all links remain valid during updates

### Documentation Update Checklist
When updating any framework document:

- [ ] **Length Check:** Document stays within 150-250 line target
- [ ] **Language Check:** Uses AI-directed language patterns (Rule/Pattern/Implementation)
- [ ] **Code Examples:** All examples are tested and functional
- [ ] **Cross-References:** All links are valid and necessary
- [ ] **Quick Reference:** Summary section updated to reflect changes
- [ ] **Consistency:** Changes align with overall framework principles

### Documentation Quality Gates
Before accepting any documentation changes:

- [ ] **Cognitive Load:** Can an AI assistant process this document effectively in context?
- [ ] **Actionability:** Does every section provide clear, implementable guidance?  
- [ ] **Completeness:** Are all essential patterns covered without unnecessary detail?
- [ ] **Consistency:** Does the document follow framework language and structure standards?

## Framework Evolution

### Continuous Improvement Process
1. **Capture Learnings** - Document gaps and improvements in [`LEARNINGS.md`](LEARNINGS.md)
2. **Regular Review** - Quarterly assessment of framework effectiveness  
3. **Incremental Updates** - Small, focused improvements rather than major rewrites
4. **Validation** - Test changes in real development scenarios

### Maintaining Framework Integrity
- All changes must align with core principles
- Maintain backward compatibility where possible
- Document breaking changes and migration paths
- Preserve the human-as-architect, AI-as-builder philosophy

For detailed implementation guidance, start with [`AI_ORCHESTRATION_PROMPT.md`](AI_ORCHESTRATION_PROMPT.md) and refer to specific strategy documents as needed.

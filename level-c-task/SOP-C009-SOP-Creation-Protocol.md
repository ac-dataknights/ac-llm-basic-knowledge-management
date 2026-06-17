# SOP-C009: SOP Creation Protocol

## Purpose
Provide systematic approach for creating new Standard Operating Procedures that maintain consistency, quality, and effectiveness across the SOP framework.

## SOP Creation Process (REQUIRED)

### 1. **Planning and Scoping** (FIRST STEP)
- Document the specific problem or inefficiency the SOP will address
- Define clear purpose statement
- Determine appropriate SOP level (A/B/C) based on scope and applicability
- List trigger keywords for SOP discovery
- Outline major process steps and decision points
- Plan quality gates between each major step category

### 2. **Content Creation**
- Follow SOP Creation template `templates/SOP-Template.md` 
- Reference SOP-C011 as an excellent example of the template in use
- Include clear success and failure criteria for each step
- Add quality gates checklist between major steps
- Document anti-patterns to avoid
- Ensure actionable, specific guidance rather than generic advice

### 3. **Integration and Documentation**
- Conduct quality review using SOP-C010: SOP Quality Review Protocol
- Address any quality deficiencies before proceeding
- Add entry to `sop-metadata-registry.yaml` with appropriate triggers
- Update relevant documentation (README.md if needed)
- Record in `memory-management/daily-memory/working-memory.yaml` that new SOP requires real-world testing
- Follow working memory SOP for proper documentation (when available)

## SOP Format Requirements
- **Title**: SOP-[Level][Number]: [Descriptive Name]
- **Purpose**: Clear statement of what the SOP accomplishes
- **Process Steps**: Numbered sections with clear headings
- **Quality Gates**: Checklist format for validation between major steps
- **Anti-Patterns**: Common mistakes to avoid
- **Success/Failure Criteria**: Clear outcomes for each major step

## Content Quality Standards
- **Actionable**: Each step should have clear, executable actions
- **Specific**: Avoid vague guidance; provide concrete examples
- **Testable**: Include validation criteria that can be objectively assessed
- **Complete**: Cover the full process from start to finish
- **Consistent**: Use established terminology and format conventions

## Quality Gates
- [ ] Problem/inefficiency clearly documented?
- [ ] Appropriate SOP level (A/B/C) determined?
- [ ] Major process steps outlined with clear boundaries?
- [ ] Quality gates planned between each major step category?
- [ ] Success and failure criteria defined for each step?
- [ ] SOP format requirements followed?
- [ ] Content meets quality standards (actionable, specific, testable)?
- [ ] Quality review completed using SOP-C010?
- [ ] Quality deficiencies addressed before integration?
- [ ] Registry entry created with appropriate triggers?
- [ ] Working memory updated to record testing requirement?

## Anti-Patterns
❌ Creating SOPs without clear problem definition
❌ Missing quality gates between major process steps
❌ Vague or untestable success criteria
❌ Inconsistent format or terminology
❌ Forgetting to update registry and working memory
❌ Skipping validation steps before integration

## Post-Creation Requirements
**Important**: After creating any new SOP, update `memory-management/daily-memory/working-memory.yaml` to record that the SOP requires real-world testing and validation. Follow the working memory documentation protocol (when available) to ensure this testing requirement is properly tracked and not forgotten. 
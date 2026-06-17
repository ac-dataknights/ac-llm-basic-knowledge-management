# SOP-A001: Master SOP Protocol

## Purpose
Ensure SOPs are discovered, applied, and maintained for all work.

## Every Task Protocol (REQUIRED)

### 1. **SOP Discovery** (FIRST STEP)
- **Complete Scope Checklist** for user request only marking yes if it is definitely part of the scope (any maybes turn to No):
  - [ ] IAC
  - [ ] ETL (discard if the scope is IaC related) 
  - [ ] Documentation
  - [ ] Development (discard if scope is deployment related)
  - [ ] Deployment
  - [ ] Debugging
  - [ ] SOP creation
  - [ ] Rules Violation
  - [ ] Memory Management
- Check `Custom-Cursor-Management/sop-metadata-registry.yaml`
- Match **checked scope items** to SOP triggers
- While a trigger might match, scope always overrides triggers so discard any SOPs which match on trigger but not on scope.  
- Load Level A (always) + relevant Level B/C SOPs

### 2. **SOP Application**
- Reference loaded SOPs throughout task
- Use SOP checklists as quality gates
- Document SOP compliance in deliverables

### 3. **SOP Evolution**
- Flag gaps where SOPs needed but missing
- Note SOP improvements during work
- Trigger SOP-C009 for new SOP creation

## SOP Level Structure
- **Level A**: Universal (always apply)
- **Level B**: Domain-specific (data-lake, iac, assessment)
- **Level C**: Task-specific (implementation, review, design)

## Context Management
- **Tight Focus**: Only load task-relevant SOPs
- **Progressive Loading**: A → B → C as needed
- **Active Reference**: Keep SOPs in working context

## Quality Gates
- [ ] SOPs checked before starting?
- [ ] Applicable SOPs loaded?
- [ ] SOP guidance applied throughout?
- [ ] SOP compliance documented?
- [ ] SOP gaps noted?

## Anti-Patterns
❌ Starting work without checking SOPs
❌ Ignoring loaded SOP guidance
❌ Missing SOP compliance documentation 
# Custom Cursor Management

This folder contains Standard Operating Procedures (SOPs) that provide guardrails and best practices for AI-assisted development work on the data lake project.

## SOP Framework Structure

### **Level A: Universal SOPs** (Always Apply)
- `SOP-A001`: Master SOP Protocol

### **Level B: Domain SOPs** (Context-Triggered)
- `SOP-B001`: Data Lake Documentation Compliance

### **Level C: Task SOPs** (Task-Triggered)
- `SOP-C001`: IaC New Resource Creation Process
- `SOP-C004`: Systematic Debugging Protocol
- `SOP-C005`: Deployment Debugging
- `SOP-C006`: AI Challenge Protocol
- `SOP-C007`: RDS Credentials Secret Deployment
- `SOP-C008`: New Source Table Integration
- `SOP-C009`: SOP Creation Protocol
- `SOP-C010`: SOP Quality Review Protocol
- `SOP-C011`: Data Lake Deployment Strategy
- `SOP-C012`: Rule Violation Log Management

## Usage

1. **Every Task**: Start with SOP-A001 (Master Protocol)
2. **Load Relevant SOPs**: Based on task keywords and triggers
3. **Apply Throughout**: Use SOPs as quality gates and guidance
4. **Evolve**: Create new SOPs when gaps identified

## File Structure

```
Custom-Cursor-Management/
├── README.md                   # This file
├── sop-metadata-registry.yaml  # SOP discovery metadata
├── rule-violation-log.yaml     # Rule adherence tracking
├── memory-management/
│   ├── daily-memory/
│   │   └── working-memory.yaml  # Session working memory
├── level-a-universal/          # Universal SOPs (always apply)
├── level-b-domain/             # Domain-specific SOPs
├── level-c-task/               # Task-specific SOPs
└── templates/                  # SOP templates and examples
```

## Quality Assurance Features

### Rule Violation Tracking
- `rule-violation-log.yaml`: Tracks instances where development practices are not followed
- Used to identify patterns and improve rule adherence
- Helps evolve SOPs based on real-world challenges

### Working Memory
- `memory-management/daily-memory/working-memory.yaml`: Session-based working memory for ongoing tasks
- Maintains context across development sessions
- Supports continuity without context bloat

## Quick Reference

**Before any task**: Check `sop-metadata-registry.yaml` for applicable SOPs
**During task**: Reference loaded SOPs as quality gates
**After task**: Note any SOP gaps or improvements needed 
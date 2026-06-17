# SOP-C012: Rule Violation Log Management

## Purpose
Provide systematic approach for documenting, tracking, and analyzing development practice violations to improve rule adherence and identify systemic issues.

## Scope
This SOP covers the complete process of logging rule violations in `rule-violation-log.yaml`, from initial violation identification through analysis and follow-up actions.

## Prerequisites
- Access to `Custom-Cursor-Management/rule-violation-log.yaml`
- Understanding of development practices and rules being violated
- Context about the session where violation occurred

---

## Step 1: Violation Identification and Initial Documentation
**Purpose**: Capture the essential details of the rule violation when it occurs  
**Rationale**: Immediate documentation prevents loss of context and ensures accurate recording

### Step 1.a: Assign Violation ID
Create unique identifier following the pattern `RV###` (e.g., RV001, RV002, etc.)
- Check existing entries to determine next sequential number
- Use zero-padded 3-digit format for consistency

### Step 1.b: Document Basic Information
Record the fundamental violation details:
```yaml
- id: "RV###"
  datetime: "YYYY-MM-DDTHH:MM:SSZ"
  model: "[AI Model Name]"
  session_context: "[Tool/Environment context]"
```
(DATE TIME SHOULD BE FETCH WITH A COMMAND, NOT GUESSED)

**Note**: When creating working memory entries that reference chat sessions, **ask the user for the chat_reference name** - AI cannot discover chat tab names automatically.

### Step 1.c: Capture User Prompt and Response
Document what triggered the violation:
```yaml
user_prompt: |
  "[Exact user request that led to violation]"

assistant_response_summary: |
  "[Brief summary of what the assistant did wrong]"
```

**Outcomes:**
- **Success**: Basic violation record created with unique ID and essential context
- **Failure**: Missing context or unclear violation - gather more information before proceeding

---

## Step 2: Rule Violation Analysis
**Purpose**: Identify specific rules violated and analyze the violation pattern  
**Rationale**: Detailed analysis enables targeted improvements and pattern recognition

### Step 2.a: Identify Violated Rules
Document each rule that was broken:
```yaml
rules_violated:
  - rule_id: "[rule_identifier]"
    rule_name: "[descriptive rule name]" 
    description: "[what the rule requires]"
    violation: "[how it was violated]"
```

### Step 2.b: Conduct Root Cause Analysis
Analyze why the violation occurred:
```yaml
analysis:
  primary_cause: "[main reason for violation]"
  contributing_factors:
    - "[factor 1]"
    - "[factor 2]"
  environmental_factors:
    - "[context that may have influenced the violation]"
  should_have_done:
    - "[correct action 1]"
    - "[correct action 2]"
```

**Outcomes:**
- **Success**: Clear understanding of what rules were violated and why
- **Failure**: Unclear analysis - review session context and development practices documentation

---

## Step 3: Impact Assessment and User Feedback Integration
**Purpose**: Assess the severity and capture user feedback for learning  
**Rationale**: Impact assessment helps prioritize fixes, user feedback provides real-world validation

### Step 3.a: Document User Feedback
Record the user's response to the violation:
```yaml
user_feedback: |
  "[Exact user feedback about the violation]"
```

### Step 3.b: Assess Impact and Severity
Evaluate the violation's consequences:
```yaml
severity: "[HIGH/MEDIUM/LOW]"
impact: "[description of actual consequences]"
```

### Step 3.c: Identify Patterns to Monitor
Note what to watch for in future sessions:
```yaml
patterns_to_watch:
  - "[pattern or trigger to monitor]"
  - "[environmental correlation to track]"
```

**Outcomes:**
- **Success**: Complete violation record with impact assessment and user feedback
- **Failure**: Incomplete assessment - gather additional information about consequences

---

## Quality Gates

### Pre-Documentation Gates
- [ ] Violation clearly identified and understood?
- [ ] Session context and user prompt captured?
- [ ] Access to rule-violation-log.yaml confirmed?
- [ ] Next sequential violation ID determined?
- [ ] Timestamp in correct ISO format?

### Post-Documentation Gates  
- [ ] All required fields completed?
- [ ] Rules violated clearly documented?
- [ ] Root cause analysis thorough and accurate?
- [ ] User feedback captured verbatim?
- [ ] Severity and impact appropriately assessed?
- [ ] Patterns to watch identified?
- [ ] YAML syntax valid and properly formatted?

---

## Final State Validation

### Success Criteria
- All quality gates passed
- Violation record complete and accurate
- Analysis provides actionable insights
- User feedback preserved for learning
- Patterns identified for future monitoring
- File syntax valid and committed

### Failure Recovery
- Review incomplete or unclear entries
- Gather additional session context if needed
- Consult user for clarification on feedback
- Validate YAML syntax before saving
- Reference existing entries for format consistency

---

## Real-World Examples

For practical examples of rule violation documentation, see:
- **RV001 - Implementation Without Clarification**: HIGH severity process violation - `Custom-Cursor-Management/rule-violation-log.yaml` (first incident)
- **RV002 - Unauthorized Code Changes**: MEDIUM severity scope violation - `Custom-Cursor-Management/rule-violation-log.yaml` (second incident)

*These examples demonstrate complete documentation format, different violation types, severity assessment, and comprehensive analysis patterns. Reference them when learning the expected format and level of detail.*

---

## Anti-Patterns
❌ Logging violations without sufficient context
❌ Generic or vague violation descriptions  
❌ Missing root cause analysis
❌ Ignoring user feedback or impact assessment
❌ Inconsistent ID numbering or formatting
❌ Delayed documentation leading to lost context
❌ **Modifying existing entries when adding new violations** - only add new entries, never edit existing ones

## Related SOPs
- **SOP-A001**: Master SOP Protocol
- **SOP-C006**: AI Challenge Protocol
- **SOP-C009**: SOP Creation Protocol
- **SOP-C010**: SOP Quality Review Protocol 
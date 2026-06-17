# SOP-C004: Systematic Debugging Protocol

## Overview
**Purpose**: Provide a systematic approach to debugging complex technical issues  
**Scope**: Code synthesis errors, deployment failures, integration issues  
**Trigger Keywords**: error, failure, debugging, troubleshooting, synthesis, deployment  

## Pre-Debugging Setup

### 1. Check for Previous Similar Issues
- **Search Location**: `IaC/Documentation/DEBUG_LOG_*.md`
- **Search for**: Similar error messages, component names, or failure patterns
- **Purpose**: Learn from previous debugging sessions and avoid repeating solved problems
- **Action**: If similar issue found, review the solution approach before proceeding

### 2. Create Debug Log
- **Location**: `IaC/Documentation/DEBUG_LOG_[FEATURE_NAME].md`
- **Purpose**: Track all changes, tests, and findings to avoid circular debugging
- **Format**: 
  ```markdown
  # Debug Log: [Issue Description]
  
  ## Problem Statement
  [Clear description of the error/issue]
  
  ## Previous Similar Issues Checked
  - [List any similar debug logs reviewed]
  - [Note any relevant patterns or solutions found]
  
  ## Systematic Testing Log
  
  ### Test #1: [Test Description]
  **Date**: [Session date]
  **Change**: [What was changed]
  **Lines**: [Specific line numbers affected]
  **Expected Result**: [What should happen]
  **Result**: [✅/❌ What actually happened]
  **Finding**: [Key insight from this test]
  ```

## Debugging Decision Tree

### Phase 1: Error Analysis
**Question**: Can we clearly identify the specific line of code causing the issue from the error message?

**YES** → Go to Phase 4 (Direct Fix)  
**NO** → Continue to Phase 2

### Phase 2: Process Element Identification
**Question**: Can we identify the general element/component of the process causing the issue?

**YES** → Continue to Phase 3  
**NO** → Add console logging/debug statements to trace execution flow, then retry Phase 2

### Phase 3: Systematic Isolation
**Approach**: Remove elements systematically and retest to isolate the working solution

#### 3.1 Individual Component Testing
- Test each major component independently
- Document results in debug log
- Identify which components work in isolation

#### 3.2 Dependency Testing  
- Test components with minimal dependencies
- Gradually add dependencies back
- Identify dependency-related failures

#### 3.3 Holistic Testing
- Test all working components together
- Ensure no integration issues between working components
- Confirm the complete working baseline

### Phase 4: Problem Element Analysis
**Question**: Does the solution work without the problematic element?

**YES** → Continue to Phase 5  
**NO** → Seek user guidance on alternative approaches

### Phase 5: Systematic Re-Integration
**Approach**: Add elements back systematically to identify the exact failure point

#### 5.1 Single Issue vs Multiple Issues
- Add the problematic element back in isolation
- Test if it's a single issue or multiple cascading issues
- Document the specific failure pattern

#### 5.2 Precision Isolation
- If multiple issues exist, isolate each one individually
- Test sub-components of the problematic element
- Identify the exact line/logic causing the failure

### Phase 6: Root Cause Analysis
**Question**: Do we have a clear understanding of the exact issue, what caused it, and what is impacted?

**YES** → Proceed to Phase 7 (Implementation)  
**NO** → Follow this escalation sequence:
1. **Analyze current debug log** for patterns and insights
2. **Review other debug logs** (`IaC/Documentation/DEBUG_LOG_*.md`) for similar issues or patterns
3. **Cross-reference error messages** and component names across all debug logs
4. **Seek user guidance** if still unclear after historical analysis

### Phase 7: Solution Implementation
1. Implement the fix based on root cause analysis
2. Test the complete solution end-to-end
3. Verify no regressions in previously working components

## Post-Debugging Cleanup

### 1. Remove Debug Artifacts
- Remove all console.log statements
- Remove temporary debug code
- Clean up any test-only modifications

### 2. Update Documentation
- **Debug Log**: Mark as RESOLVED with final solution summary
- **Main Documentation**: Update if significant architectural changes were made
- **README/Architecture Docs**: Update if patterns or approaches changed

### 3. Knowledge Capture
- Document the root cause and solution for future reference
- Update relevant SOPs if new debugging patterns were discovered
- Share insights with team if applicable

## Best Practices

### Debug Log Discipline
- **Always** log each test before running it
- **Never** skip documenting a test result
- **Use consistent formatting** for easy pattern recognition
- **Number tests sequentially** to track progression
- **Check previous debug logs** before starting new debugging sessions
- **Cross-reference solutions** from similar past issues

### Systematic Approach
- **Test one variable at a time** whenever possible
- **Always establish a working baseline** before adding complexity
- **Document expected results** before running tests
- **Avoid assumptions** - verify each hypothesis with tests

### Communication
- **Be specific** about what was changed and why
- **Use precise language** (✅/❌, line numbers, exact error messages)
- **Ask for guidance** when stuck rather than continuing circular debugging
- **Explain your reasoning** when proposing solutions

## Example Application
*Reference: cleanse-raw-etl Token construct ID error debugging*

**Problem**: "You cannot use a Token as the id of a construct" error  
**Approach**: 10 systematic tests isolating each component  
**Result**: Identified EventBridge target creation using `rule.id` (Token) as construct ID  
**Solution**: Replace dynamic Token with static rule name in construct ID generation  

## Success Metrics
- ✅ Clear identification of root cause
- ✅ Surgical precision in problem isolation  
- ✅ Complete solution with no regressions
- ✅ Comprehensive debug log for future reference
- ✅ Clean codebase with no debug artifacts remaining

## When to Escalate
- After 3 systematic test cycles with no progress
- When multiple interconnected issues are discovered
- When the root cause requires architectural changes
- When time constraints require immediate user guidance 
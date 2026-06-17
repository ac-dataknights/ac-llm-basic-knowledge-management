# SOP-C006: AI Challenge Protocol

## Purpose
This SOP defines the protocol for challenging AI responses and handling technical disagreements during development work. It establishes a framework for productive collaboration between human developers and AI assistants.

## Scope
- All development work involving AI assistance
- Technical decision-making processes
- Code review and debugging sessions
- Architecture and implementation discussions

## Background
AI assistants are powerful tools that can significantly accelerate development, but they can make errors or provide incorrect information. A structured approach to challenging and verifying AI responses is essential for maintaining code quality and building trust.

## Protocol

### When Challenging AI Responses

#### 1. Trust Your Technical Instincts
- **If something feels wrong, it probably is**
- Don't dismiss your expertise in favor of AI confidence
- Technical intuition built from experience is valuable
- Challenge responses that contradict established patterns

#### 2. Demand Evidence and Verification
- **Ask for documentation links or code examples**
- Request specific references to official documentation
- Ask for concrete examples that demonstrate the claim
- Don't accept "trust me" responses without backing evidence

#### 3. Accept Uncertainty as Valid
- **"I'm not certain" is a valid and valuable response**
- Prefer honest uncertainty over confident incorrectness
- Appreciate when AI acknowledges knowledge limitations
- Use uncertainty as a signal to verify independently

#### 4. Prefer Verification Over Defense
- **Focus on finding the truth, not winning arguments**
- Encourage collaborative fact-finding
- Value learning over being right
- Document findings for future reference

### When AI is Challenged

#### 1. Acknowledge Uncertainty Rather Than Defending
- **Admit when uncertain about technical details**
- Don't double-down on potentially incorrect information
- Say "Let me verify that" instead of defending
- Recognize that confidence doesn't equal correctness

#### 2. Verify Claims with Documentation
- **Provide specific references to official documentation**
- Link to authoritative sources when possible
- Show concrete code examples
- Admit when documentation contradicts the claim

#### 3. Admit Errors When Found
- **Acknowledge mistakes clearly and directly**
- Explain what was incorrect and why
- Update the approach based on correct information
- Thank the challenger for catching the error

#### 4. Update Approach Based on Feedback
- **Incorporate lessons learned into future responses**
- Adjust confidence levels appropriately
- Reference the learning in similar future situations
- Document patterns for improved accuracy

## Implementation Guidelines

### For Development Sessions
1. **Establish challenge-friendly environment** at start of session
2. **Encourage questions** on any technical claims
3. **Pause to verify** when challenged rather than continuing
4. **Document learnings** in session notes or code comments

### For Code Reviews
1. **Question patterns** that seem unusual or complex
2. **Verify architectural decisions** against project standards
3. **Challenge performance or security claims** with evidence
4. **Test assumptions** with small proof-of-concept implementations

### For Architecture Discussions
1. **Reference established patterns** and principles
2. **Ask for trade-off analysis** on proposed solutions
3. **Challenge scalability claims** with concrete scenarios
4. **Verify compliance** with project SOPs and standards

## Success Metrics

### Positive Indicators
- ✅ Technical disagreements lead to better solutions
- ✅ Errors are caught early in the development process
- ✅ Trust is built through honest uncertainty acknowledgment
- ✅ Documentation and verification become standard practice
- ✅ Learning from mistakes improves future accuracy

### Warning Signs
- ❌ Defensive responses to technical challenges
- ❌ Overconfidence in uncertain areas
- ❌ Reluctance to verify claims with documentation
- ❌ Dismissing human expertise and intuition
- ❌ Repeating the same errors without learning

## Related SOPs
- **SOP-A001**: Master Protocol (for SOP discovery and application)
- **SOP-C004**: Systematic Debugging (for technical problem-solving)
- **SOP-C005**: Deployment Debugging (for deployment-specific issues)

## Examples

### Good Challenge Example
```
Human: "I'm not sure data sources should have tags in Terraform"
AI: "Let me verify that with the documentation. You're right to question it."
Result: Error discovered and fixed
```

### Poor Response Example
```
Human: "I'm not sure data sources should have tags in Terraform"
AI: "No, they're needed for state management, trust me on this."
Result: Error perpetuated and defended
```

## Revision History
- **v1.0** (2025-06-23): Initial creation based on VPC discovery debugging session 
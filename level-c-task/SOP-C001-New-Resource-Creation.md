# SOP-C001: IaC New Resource Creation Process

## Purpose
Systematic approach to adding new resources to data lake infrastructure following three-tier architecture.

## Triggers
- "new resource", "add resource", "create resource", "infrastructure"

## Procedure

### **Step 1: Confirm Feature Stack**
- **Question**: Which feature stack is being updated?
- **Validation**: Ensure feature stack exists and is appropriate for the resource
- **Reference**: Check `IaC/feature-stacks/` directory structure

### **Step 2: Identify Resource Type**
- **Question**: What AWS resource type is needed?
- **Examples**: S3, DynamoDB, Lambda, Glue, IAM, EventBridge, etc.
- **Validation**: Confirm resource type is appropriate for the feature stack

### **Step 3: Check Service Stack Availability**

#### **Option A: Service Stack EXISTS** ✅
- **Action**: Check if user provided complete information for resource creation
- **Required Info**: All parameters needed by service stack function
- **Implementation**: Add resource to loopable object in feature stack `index.ts`
- **Pattern**: Follow existing resource creation patterns in the feature stack

#### **Option B: Service Stack MISSING** ❌
- **Prompt**: "No service stack exists for [resource type]. Would you like me to create one?"

##### **User Says NO**
- **Result**: Dead end - cannot proceed
- **Action**: Ask user what they want to do instead
- **Options**: Choose different resource type, modify existing service stack, or abandon

##### **User Says YES**
- **Action**: Build new service stack using established patterns
- **Reference**: Use `IaC/Documentation/Solution_Docs/3-SERVICE_PATTERNS.md`
- **Examples**: Study existing service stacks for patterns and structure
- **Implementation**: Create new service stack following three-tier architecture

## Quality Gates
- [ ] Feature stack confirmed and appropriate?
- [ ] Resource type identified and valid?
- [ ] Service stack availability checked?
- [ ] If service exists: User provided complete information?
- [ ] If service missing: User decision on creation obtained?
- [ ] Implementation follows three-tier architecture?
- [ ] Loopable object pattern maintained?

## Anti-Patterns
❌ Adding resources without confirming feature stack context
❌ Mixing service and feature concerns
❌ Creating resources outside of loopable object patterns
❌ Bypassing service stack abstraction layer
❌ Not following established three-tier architecture

## Reference Documentation
- `IaC/Documentation/Solution_Docs/3-SERVICE_PATTERNS.md`
- `IaC/Documentation/Solution_Docs/4-FEATURE_PATTERNS.md`
- Existing service stack implementations in `IaC/service-stacks/` 
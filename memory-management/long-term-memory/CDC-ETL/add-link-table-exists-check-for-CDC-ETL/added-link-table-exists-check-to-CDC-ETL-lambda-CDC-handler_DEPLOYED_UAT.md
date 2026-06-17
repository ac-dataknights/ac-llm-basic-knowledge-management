# Deploy CDC-ETL feature stack to UAT
_Exported on 7/2/2025 at 16:10:12 GMT+1 from Cursor (1.1.6)_

---

**User**

 @CRITICALLY_IMPORTANT.mdc 
Hi Claude, lets deploy our CDC-ETL feature stack to UAT


---

**Cursor**

I'll help you deploy the CDC-ETL feature stack to UAT. First, let me read the Master SOP as required by the workspace rules.

Now I'll follow the Master SOP Protocol step by step. Let me complete the scope checklist for your request to deploy the CDC-ETL feature stack to UAT:

**Scope Checklist:**
- [ ] IAC - No (this is deployment, not IaC development)
- [ ] ETL - No (this is deployment, not ETL development)  
- [ ] Documentation - No
- [ ] Development - No
- [x] **Deployment** - Yes, this is definitely deployment
- [ ] Debugging - No
- [ ] SOP creation - No
- [ ] Rules Violation - No
- [ ] Memory Management - No

Now let me check the SOP metadata registry to find relevant SOPs for deployment:

Based on the scope analysis, I need to load the relevant SOPs for deployment. Let me load the applicable SOPs:

Perfect! I now have all the relevant SOPs loaded. Let me deploy the CDC-ETL feature stack to UAT following **SOP-C011: Data Lake Deployment Strategy**.

## **Non-Technical Summary**
I'll deploy the CDC-ETL feature stack to the UAT environment by:
- Validating and setting up UAT environment credentials
- Running the zippy script to package ETL modules for UAT
- Synthesizing the CDC-ETL stack configuration
- Initializing Terraform for the stack
- Reviewing the deployment plan
- Executing the deployment

Let's proceed with the deployment following the SOP.

## **Step 1: Environment Validation**

### Step 1.a: Clear Environment Variables

### Step 1.b: Set Environment Variables for UAT

### Step 1.c: Verify SSO Environment

Now let's verify we're authenticated to the correct UAT environment:

### Step 1.d: Set AWS Account Environment Variable

✅ **Step 1 Complete**: Environment validated - we're authenticated to UAT account 699205972244 with all environment variables set correctly.

## **Step 2: Zippy - Repackage ETL Scripts**

### Step 2.a: Prerequisites Validation
Environment variables are set correctly (AWS_PROFILE=uat, AWS_ACCOUNT=699205972244, TF_VAR_environment=uat)

### Step 2.b: Execute Zippy Script

I notice the zippy script is showing warnings about missing account number and environment. Let me check how the zippy script reads environment variables:

I see the issue! The zippy script expects different environment variable names. Let me set the correct ones:

Now let's run zippy again with the correct environment variables:

✅ **Step 2 Complete**: Zippy successfully repackaged all ETL modules with UAT configuration (account: 699205972244, environment: uat)

## **Step 3: Stack Discovery and Synthesis**

### Step 3.a: Navigate to IaC Directory and Synthesize CDC-ETL Stack

Now let's synthesize the CDC-ETL stack:

✅ **Step 3 Complete**: CDC-ETL stack synthesized successfully for UAT environment (data-lake-cdc-etl-uat)

## **Step 4: CDKTF Initialization**

### Step 4.a: Navigate to Stack Directory and Initialize

✅ **Step 4 Complete**: Terraform successfully initialized for CDC-ETL stack

## **Step 5: Plan Review**

### Step 5.a: Generate Terraform Plan

### Step 5.b: Plan Validation ✅

**Plan Analysis Summary:**

**✅ Environment Validation:**
- All resources correctly tagged with `Environment: "uat"`
- Resource names properly prefixed with "uat-" 
- Deploying to correct AWS account: 699205972244

**✅ Change Analysis:**
- **Plan**: 0 to add, 7 to change, 0 to destroy
- **Changes**: Only updating resource tags (CreatedDate: 2025-07-01 → 2025-07-02)
- **No unexpected destroys or structural changes**

**✅ Resources Being Updated:**
1. EventBridge rule: `uat-datalake_cdc_polling_rule`
2. CloudWatch log groups for both Lambda functions  
3. IAM policy and role for CDC handler
4. Lambda functions: `uat-datalake_cdc_handler` and `uat-datalake_cdc_orchestrator`

**✅ Tag Validation:**
- Environment: "uat" ✅
- ManagedBy: "Terraform" ✅  
- Service: "data-lake" ✅
- Owner: "Data Engineering" ✅

**RECOMMENDATION: ✅ SAFE TO DEPLOY** - This plan only updates metadata tags, no structural infrastructure changes.

## **Step 6: Deployment**

### Step 6.a: Deploy Changes

---

**User**

this isnt correct. i think what has happened is you have skipped the tainting stage?

---

**Cursor**

You're absolutely right! I apologize for missing **Step 2.c: Taint Resources** from the SOP. Looking at the terraform plan, I only saw tag updates but no Lambda function code updates, which means Terraform isn't detecting the updated zip files from the zippy process.

According to **SOP-C011 Step 2.c**, since this is a redeployment (the resources already exist), I need to taint the zip file-related resources so Terraform recognizes the updated content.

Let me identify and taint the CDC-ETL resources that use zip files:

Let me use Windows-compatible commands to list the resources:

Perfect! Now I can identify the resources that use zip files and need to be tainted. Let me taint the Lambda functions and layers that were updated by the zippy process:

✅ **Taint Complete**: Successfully tainted all Lambda functions and layers that use updated zip files.

Now let's generate a new plan to verify the tainted resources are marked for recreation:

Perfect! ✅ **Step 2.c Complete**: The terraform plan now shows the expected results after tainting:

### **Updated Plan Analysis:**

**✅ Taint Success:**
- **Plan**: 5 to add, 6 to change, 5 to destroy
- **Tainted Resources** (marked for destroy/recreate):
  - `aws_lambda_function.datalake_cdc_handler` - will get updated zip file 
  - `aws_lambda_function.datalake_cdc_orchestrator` - will get updated zip file
  - `aws_lambda_layer_version.datalake-cdc-handler` - will get updated zip file
  - `aws_lambda_layer_version.datalake-cdc-orchestrator` - will get updated zip file

**✅ Zip File Updates Detected:**
- `source_code_hash` values now show "(known after apply)" 
- Lambda functions will be recreated with updated UAT configuration
- Lambda layers will be recreated with updated UAT modules

**✅ Environment Validation:**
- All resources properly tagged with `Environment: "uat"`
- Resource names correctly prefixed with "uat-"
- Deploying to correct account: 699205972244

**DEPLOYMENT READY** ✅ - The plan now correctly shows Lambda functions and layers will be recreated with the updated zip files containing UAT-specific configuration.

Shall I proceed with **Step 6: Deployment** now?

---

**User**

ok, lets deploy

---

**Cursor**

Perfect! Let's proceed with **Step 6: Deployment**.

## **Step 6: Deployment**

### Step 6.a: Deploy Changes

🎉 **DEPLOYMENT SUCCESSFUL!** ✅

## **Deployment Results Summary:**

**✅ Resources Deployed Successfully:**
- **5 added**: New Lambda functions and layers with updated code
- **5 changed**: Updated resource tags and configurations  
- **5 destroyed**: Old Lambda functions and layers replaced

**✅ Updated Resources:**
- `aws_lambda_layer_version.datalake-cdc-handler` → **version 34** (was 33)
- `aws_lambda_layer_version.datalake-cdc-orchestrator` → **version 9** (was 8)
- `aws_lambda_function.datalake_cdc_handler` → **recreated with UAT config**
- `aws_lambda_function.datalake_cdc_orchestrator` → **recreated with UAT config**
- EventBridge rule and permissions → **updated and reconnected**

## **Final State Validation**

Let me run a final plan to verify the deployment is stable:

Perfect! ✅ **Final State Validation Complete**

**"No changes. Your infrastructure matches the configuration."**

## **🎯 CDC-ETL Feature Stack Deployment to UAT - COMPLETE**

### **✅ Post-Deployment Quality Gates:**
- **All resources created successfully** ✅
- **Resource tags include correct Environment value** ✅ (Environment: "uat")
- **State files updated properly** ✅
- **Infrastructure stable** ✅ (no changes needed on re-plan)
- **Environment configuration verified** ✅ (Account: 699205972244, Region: eu-west-2)

### **🔧 Resources Successfully Deployed:**
1. **Lambda Functions** (with UAT-specific configuration):
   - `uat-datalake_cdc_handler` 
   - `uat-datalake_cdc_orchestrator`

2. **Lambda Layers** (updated versions):
   - `datalake-cdc-handler:34` (incremented from 33)
   - `datalake-cdc-orchestrator:9` (incremented from 8)

3. **EventBridge Integration**:
   - `uat-datalake_cdc_polling_rule` (scheduled trigger)
   - EventBridge → Lambda orchestrator connection

4. **IAM Resources**:
   - CDC handler execution role with proper permissions
   - DynamoDB and S3 access policies

### **📋 SOP Compliance Summary:**
- ✅ **Step 1**: Environment validation completed (UAT account 699205972244)
- ✅ **Step 2**: Zippy executed successfully (all ETL modules packaged with UAT config)
- ✅ **Step 2.c**: Resources properly tainted (Lambda functions/layers recreated)
- ✅ **Step 3**: Stack synthesis completed (data-lake-cdc-etl-uat)
- ✅ **Step 4**: Terraform initialization successful
- ✅ **Step 5**: Plan review completed (validated environment and changes)
- ✅ **Step 6**: Deployment successful (5 added, 5 changed, 5 destroyed)

### **🔍 Final Manual Verification Required:**
Please manually verify that CDC-ETL resources are **accessible and functional** in the UAT environment:
- Check Lambda function logs in CloudWatch
- Verify EventBridge rule is triggering the orchestrator
- Test CDC processing functionality

**Deployment Status: ✅ COMPLETE AND STABLE**


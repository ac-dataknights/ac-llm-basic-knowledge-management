# SOP-C011: Data Lake Deployment Strategy


# Step 0.1: Why are we deploying?
Check with the user what the rationale for the deployment is and if deploying to prod ask if these changes have been deployed into UAT first? A no shouldnt be a blocker but we should challenge the user if they are trying to deploy straight into prod.


## Step 1: Environment Validation
**Purpose**: Ensure we are in the correct environment for deployment.  
**Rationale**: Deploying into the wrong environment could be catastrophic.  


### Step 1.a: Clear Environment Variables
Remove any existing AWS credential environment variables to prevent conflicts:
```powershell
Remove-Item Env:AWS_PROFILE -ErrorAction SilentlyContinue;
Remove-Item Env:ACCOUNT_NUMBER -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_ACCOUNT_NUMBER -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_ACCESS_KEY_ID -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_SECRET_ACCESS_KEY -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_SESSION_TOKEN -ErrorAction SilentlyContinue;
Remove-Item Env:TF_VAR_environment -ErrorAction SilentlyContinue;
```
### Step 1.b: Validate Environment Variables
    Set  environmental variables `AWS_PROFILE` and `TF_VAR_environment` to both be  {dev | uat | prod} dependent upon the users request.
    Ensure this is set in IaC/ and if AWS_PROFILE is not set then this will cause SSO issues down the line.
```powershell
$env:AWS_PROFILE="[target_profile]"
$env:TF_VAR_environment="[target_environment]"
```

### Step 1.c: Verify SSO Environment
    Use command `aws sso login --profile {dev | uat | prod}`
    Use command `aws sts get-caller-identity` to check SSO'd profile and compare with user required environment to determine this is the right environment.

**Outcomes:**
- **Success**: SSO'd environment and environmental variables match with user required environment for deployment.
- **Failure**: Mismatch in SSO'd environment or environmental variables. Re-SSO into correct environment, flatten environment variables and reset with correct values.

### Step 1.d: Validate Environment Variables
    Set  environmental variables `AWS_ACCOUNT` based on the aws sts get-caller-identity results.
```powershell
$env:AWS_ACCOUNT="[target_aws_account_number]"
$env:ACCOUNT_NUMBER="[target_aws_account_number]"
```

## Quality Gates

### Pre-Deployment Gates
- [ ] Working directory is `IaC/`
- [ ] SSO session active and authenticated
- [ ] AWS profile matches target environment
- [ ] Environment variables set correctly
- [ ] Target stack(s) exist in feature-stacks directory
- [ ] No conflicting state files


---


## Step 2: Stack Discovery and Synthesis
**Purpose**: Generate Terraform configuration for target stack(s).  
**Rationale**: CDKTF must synthesize TypeScript into Terraform before deployment.  

### Step 2.a: Synthesize Stacks
Run synthesis for target stack(s):
```powershell
# Single stack
npx ts-node main.ts [stack_name]

# Multiple stacks
npx ts-node main.ts [stack_name_1] [stack_name_2]
```

**Outcomes:**
- **Success**: Terraform files generated in `cdktf.out/stacks/` with correct environment naming.
- **Failure**: Synthesis errors. Check stack names, environment variables, and working directory.

---

## Step 3: Zippy
**Purpose**: Zippy is used to repackage all the ETL scripts into ZIP files. Additionally, it updates all the config file with the correct environment and account number we are deploying into. 
**Rationale**: If not done, the assets will point at resources which dont exist in this account.

### Step 3.a: Check if the feature stack deploys in zip files as S3 assets or deploys any lambda layers. If not then Zippy can be skipped. 

### Step 3.b: Validate Prerequisites
CD to the Root folder and then confirm AWS_PROFILE, AWS_ACCOUNT_ID, and AWS_ENVIRONMENT environment variables are set
```powershell
echo $AWS_PROFILE, $AWS_ACCOUNT_ID, $AWS_ENVIRONMENT;
```

## Quality Gates

### Pre-Packaging Gates
- [ ] $AWS_PROFILE is set
- [ ] $AWS_ACCOUNT_ID is set
- [ ] $AWS_ENVIRONMENT is set


Verify working directory is project root
AWS_environment
### Step 3.c: Execute Zippy Script
Run the zippy.py script as described - note Zippy must be ran in root.
```powershell
python ETL/Modules/Packaged/zippy.py
```
**Outcomes:**
- **Success**: Zippy script runs without error (Zip files in ETL\Modules\Packaged should have bee updated).
- **Failure**: Zippy errors or the zip files do not update.

## Quality Gates

### Post-Packaging Gates
- [ ] Zippy has ran successfully
- [ ] No warning messages received.
- [ ] Files zipped for correct environment


## Step 4: CDKTF Initialization
**Purpose**: Initialize CDKTF backend and providers for each target stack.  
**Rationale**: CDKTF requires initialization before planning or deploying.  

### Step 4.a: Navigate to Stack Directory and Initialise
For each target stack, navigate to generated directory and initalise:
```powershell
cd cdktf.out/stacks/data-lake-[stack]-[env]
terraform init
```
## for new stacks use terraform init -reconfigure

**Outcomes:**
- **Success**: CDKTF initialized successfully with backend configured.
- **Failure**: Backend or provider initialization errors. Check S3 backend configuration and AWS permissions.

---

Step 5: Taint Resources (Redeployment Only)
**Purpose**: Any .zip or .py files will need tainting. 
**Rationale**: Terraform can not see when asset files have changes. If we dont manually taint the files then terraform will not deploy the asset file even though we have just updated the content.
Look in the cdktf.out.json we have just snythed. any resoruce which references a .zip or .py file will need to have the taint command ran against it.

**Outcomes:**
- **Success**: When we run the Terraform plan we should see the zip files marked to be destoryed and then recreated.
- **Failure**: Zip file is not included in the terraform plan.

--- 



## Step 6: Plan Review
**Purpose**: Review planned changes before deployment.  
**Rationale**: Plan review prevents unintended resource changes or environment misconfigurations.  

### Step 6.a: Generate Plan
Generate CDKTF plan:
```powershell
terraform plan
```

### Step 6.b: Validate Plan Output
Review plan for:
- Correct environment in resource names
- Expected resource changes (has the user advised that they are expecting any specific resources to change as part of the deployment)
- No unexpected destroys
- Correct resource tags (Environment, Service, ManagedBy)
- Summarise these findings for the end user

**Outcomes:**
- **Success**: Plan shows expected changes with correct environment configuration.
- **Failure**: Unexpected changes or wrong environment. STOP deployment and investigate.

---

## Step 7: Deployment
**Purpose**: Apply CDKTF configuration to create/update infrastructure.  
**Rationale**: Actual deployment of infrastructure resources.  

### Step 7.a: Get explicit user permission!
Always ask the user if they are ready to deploy? This is critical as it create an artifical break in any autorun process prior to changes to resources being actioned. 

### Step 7.b: Deploy Changes
Deploy infrastructure changes:
```powershell
terraform apply --auto-approve
```
---

## Quality Gates

### Post-Deployment Gates
- [ ] All resources created successfully
- [ ] Resource tags include correct Environment value
- [ ] State files updated in respective stack directories
- [ ] Can run synthesis + plan again with no changes
- [ ] Expected resources accessible and functional

---

## Final State Validation

### Success Criteria
- All quality gates passed
- No deployment errors or warnings
- Resources functional and accessible (prompt user to manually check)
- Environment configuration verified
- State files properly maintained in S3backend Share State Solution.

---
<!-- 
## Step 7: Document
Record the deployment summary in a deployment document  -->



---

## Troubleshooting
For any issues encountered during deployment, refer to **SOP-C005: Deployment Debugging** for comprehensive troubleshooting guidance. 
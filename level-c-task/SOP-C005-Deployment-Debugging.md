# SOP-C005: Deployment Debugging

## Purpose
Comprehensive troubleshooting guide for data lake infrastructure deployment issues using the Single Entry Point CDKTF architecture.

## Scope
This SOP covers debugging and resolution of common deployment issues encountered during data lake infrastructure deployment.

## Error Resolution

### **Import Existing Resources**
If deployment fails due to existing resources:
```powershell
cd cdktf.out/stacks/data-lake-[stack]-[env]
terraform import [resource_type].[resource_name] [resource_id]
terraform apply -auto-approve
```

### **Rate Limiting (DynamoDB)**
If AWS rate limits tag updates:
- [ ] Wait 5 minutes and retry
- [ ] Resources are functional even with tag update failures
- [ ] Complete tag updates in follow-up deployment

### **Working Directory Issues**
If synthesis fails with "no such file or directory, scandir 'feature-stacks'":
- [ ] Ensure you're running commands from `IaC/` directory, not project root
- [ ] Verify `feature-stacks/` directory exists in current location
- [ ] Run `pwd` to confirm current directory
- [ ] Use `cd IaC` to navigate to correct directory

### **CDKTF vs Terraform Commands**
- [ ] Use `npx ts-node main.ts [stacks]` for synthesis only
- [ ] Use `terraform init` and `terraform plan/apply` from generated stack directories
- [ ] Do NOT use `cdktf plan` or `cdktf deploy` commands
- [ ] Always run `terraform init` before `terraform plan` in each stack directory

### **AWS Profile Issues**
If synthesis fails with "config profile could not be found":
- [ ] Check current AWS profile: `aws configure list`
- [ ] Clear conflicting environment variables (see step 0)
- [ ] Set correct profile: `$env:AWS_PROFILE="[profile_name]"` (Windows PowerShell)
- [ ] Verify profile works: `aws sts get-caller-identity`
- [ ] Ensure SSO session is active for the target profile
- [ ] Re-run synthesis after profile is set correctly

### **AWS SSO Permissions Issues**
If `aws sts get-caller-identity` fails with "No access" after SSO login:
- [ ] Verify SSO role has `sts:GetCallerIdentity` permission
- [ ] Check if multiple SSO roles are available and select correct one
- [ ] Contact AWS administrator to verify role permissions
- [ ] Ensure SSO session is for the correct account and role

### **AWS Credential Authentication Issues**
If terraform init/plan fails with "InvalidClientTokenId" or "security token included in the request is invalid":

#### **Root Cause**
Expired cached AWS credentials in `~/.aws/credentials` file interfering with profile-based authentication.

#### **Solution Steps**
1. **Check for conflicting environment variables:**
   ```powershell
   # Windows PowerShell
   Get-ChildItem Env: | Where-Object {$_.Name -like "*AWS*"}
   
   # Remove any conflicting credential variables
   Remove-Item Env:AWS_ACCESS_KEY_ID -ErrorAction SilentlyContinue
   Remove-Item Env:AWS_SECRET_ACCESS_KEY -ErrorAction SilentlyContinue
   Remove-Item Env:AWS_SESSION_TOKEN -ErrorAction SilentlyContinue
   ```

2. **Verify AWS CLI authentication:**
   ```powershell
   aws sts get-caller-identity
   ```

3. **If AWS CLI works but Terraform fails, clear cached credentials:**
   ```powershell
   # Remove cached credentials file
   Remove-Item "$env:USERPROFILE\.aws\credentials" -Force
   
   # Perform fresh SSO login
   aws sso login --profile [profile_name]
   
   # Verify authentication
   aws sts get-caller-identity
   ```

4. **Retry Terraform operations:**
   ```powershell
   terraform init -reconfigure
   terraform plan
   ```

#### **Quality Gates for AWS Authentication**
- [ ] No conflicting AWS credential environment variables set?
- [ ] AWS CLI authentication working: `aws sts get-caller-identity`?
- [ ] Fresh SSO session obtained after clearing cached credentials?
- [ ] Terraform can authenticate and access S3 backend?
- [ ] Environment variable `AWS_PROFILE` set in same shell session as Terraform?

## Common Error Patterns

### **Synthesis Errors**
- **"Cannot find module"**: Check npm dependencies are installed
- **"Stack not found"**: Verify stack name matches feature-stacks directory
- **"Environment variable not set"**: Ensure TF_VAR_environment is set for non-dev environments

### **Terraform Init Errors**
- **"Backend configuration error"**: Check S3 backend configuration
- **"Provider plugin error"**: Run `terraform init -upgrade`
- **"State file locked"**: Check for concurrent deployments

### **Terraform Plan Errors**
- **"Resource already exists"**: Use terraform import or destroy existing resource
- **"Permission denied"**: Verify IAM permissions for deployment role
- **"Invalid configuration"**: Check resource configuration in stack files

### **Terraform Apply Errors**
- **"Rate limiting"**: Wait and retry, or implement exponential backoff
- **"Resource dependency error"**: Check resource dependencies and order
- **"Timeout error"**: Increase timeout values or check network connectivity

## Debugging Checklist

### **Pre-Debugging Steps**
- [ ] Confirm working directory is `IaC/`
- [ ] Clear all AWS environment variables
- [ ] Set correct AWS profile and environment variables
- [ ] Verify SSO session is active
- [ ] Check AWS CLI authentication works

### **Synthesis Debugging**
- [ ] Run `npx ts-node main.ts` without arguments to see available stacks
- [ ] Check stack discovery output for errors
- [ ] Verify target stack exists in feature-stacks directory
- [ ] Confirm environment variable is set correctly

### **Terraform Debugging**
- [ ] Navigate to generated stack directory
- [ ] Run `terraform init -reconfigure` if backend issues
- [ ] Check terraform plan output for specific errors
- [ ] Verify resource names and tags are correct
- [ ] Check for state file conflicts

### **AWS Authentication Debugging**
- [ ] Clear cached credentials if authentication fails
- [ ] Perform fresh SSO login
- [ ] Verify role permissions
- [ ] Check for conflicting environment variables
- [ ] Test AWS CLI commands before Terraform

## Escalation Path

### **When to Escalate**
- Multiple failed deployment attempts
- Authentication issues persist after following all steps
- Resource import failures
- Backend configuration issues
- Permission-related errors

### **Information to Gather**
- Complete error messages and stack traces
- AWS profile and environment configuration
- Terraform plan output
- AWS CLI authentication test results
- Working directory and file structure

### **Escalation Contacts**
- Infrastructure team lead
- AWS administrator (for permission issues)
- DevOps team (for CI/CD issues)

## Reference
- Master SOP: `SOP-A001-Master-Protocol.md`
- Deployment Strategy: `SOP-C011-Data-Lake-Deployment-Strategy.md`
- Systematic Debugging: `SOP-C004-Systematic-Debugging.md` 
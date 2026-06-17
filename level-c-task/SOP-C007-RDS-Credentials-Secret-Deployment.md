# SOP-C007: RDS Credentials Secret Deployment

## Step 1: Environment Validation
**Purpose**: Ensure we are in the correct environment for secret deployment.  
**Rationale**: Deploying secrets to the wrong environment could expose credentials or cause authentication failures.  

### Step 1.a: Clear Environment Variables
Remove any existing AWS credential environment variables to prevent conflicts:
```powershell
Remove-Item Env:AWS_PROFILE -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_ACCESS_KEY_ID -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_SECRET_ACCESS_KEY -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_SESSION_TOKEN -ErrorAction SilentlyContinue;
Remove-Item Env:ENVIRONMENT -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_REGION -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_ACCOUNT_ID -ErrorAction SilentlyContinue;
```

### Step 1.b: Set Environment Variables
Set environmental variables for target environment:
```powershell
$env:AWS_PROFILE="[target_profile]"  # {dev | uat | prod}
$env:ENVIRONMENT="[target_environment]"  # {dev | uat | prod}
$env:AWS_REGION="[target_region]"  # e.g., eu-west-2
```

### Step 1.c: Verify SSO Environment
```powershell
aws sso login --profile [target_profile]
aws sts get-caller-identity
```

### Step 1.d: Set AWS Account ID
```powershell
$env:AWS_ACCOUNT_ID=(aws sts get-caller-identity --query Account --output text)
```

**Outcomes:**
- **Success**: SSO'd environment matches target environment for secret deployment.
- **Failure**: Mismatch in SSO'd environment. Re-SSO into correct environment and reset variables.

---

## Quality Gates

### Pre-Deployment Gates
- [ ] Working directory is `IaC/`
- [ ] SSO session active and authenticated
- [ ] AWS profile matches target environment
- [ ] Environment variables set correctly (`AWS_PROFILE`, `ENVIRONMENT`, `AWS_REGION`, `AWS_ACCOUNT_ID`)
- [ ] Target environment secrets deployment role exists
- [ ] Database connection details available

---

## Step 2: Database Credentials Validation  
**Purpose**: Validate all required database connection details are available.  
**Rationale**: Missing credentials will result in incomplete or invalid secret configuration.

### Step 2.a: Gather Required Credentials
Collect the following database connection details:
- **username**: Database service account username
- **password**: Database service account password  
- **engine**: Database engine type (e.g., mariadb, mysql, postgres)
- **host**: Database endpoint/hostname
- **port**: Database port number
- **dbInstanceIdentifier**: RDS instance identifier

### Step 2.b: Set Credential Environment Variables
Set environment variables with names matching secret field names:
```powershell
$env:username="[database_username]"
$env:password="[database_password]"
$env:engine="[database_engine]"
$env:host="[database_host]"
$env:port="[database_port]"
$env:dbInstanceIdentifier="[rds_instance_identifier]"
```

---

## Quality Gates

### Pre-Secret-Deployment Gates
- [ ] All 6 required credential fields have values set
- [ ] Database endpoint is accessible from target environment
- [ ] Service account credentials are valid
- [ ] RDS instance identifier matches actual RDS instance
- [ ] Database engine type is correct
- [ ] Port number matches database configuration

---

## Step 3: Secret Deployment
**Purpose**: Deploy database credentials to AWS Secrets Manager.  
**Rationale**: Securely store database credentials for DMS and other services to consume.

### Step 3.a: Execute Secret Deployment Script
Run the manage-secrets script:
```powershell
npx ts-node src/utils/manage-secrets.ts
```

### Step 3.b: Validate Deployment Output
Expected successful output:
```
⚠️ Bootstrap credentials not found, will need to provide them manually
✅ Updated secret: [environment]-dms-credentials
✅ Secrets deployment completed
```

**Outcomes:**
- **Success**: Secret created/updated successfully in AWS Secrets Manager.
- **Failure**: Deployment errors. Check IAM permissions, environment variables, and AWS connectivity.

---

## Step 4: Secret Validation
**Purpose**: Verify secret was deployed with correct structure and values.  
**Rationale**: Ensure secret contains all required fields with correct values for consuming services.

### Step 4.a: Retrieve and Verify Secret
Validate secret structure using AWS CLI:
```powershell
aws secretsmanager get-secret-value --secret-id [environment]-dms-credentials --query SecretString --output text
```

### Step 4.b: Validate Secret Structure
Verify secret contains exactly these fields:
```json
{
  "username": "[database_username]",
  "password": "[database_password]",
  "engine": "[database_engine]",
  "host": "[database_host]",
  "port": [database_port_number],
  "dbInstanceIdentifier": "[rds_instance_identifier]"
}
```

**Outcomes:**
- **Success**: Secret contains all required fields with correct values.
- **Failure**: Missing fields or incorrect values. Re-run deployment with corrected environment variables.

---

## Quality Gates

### Post-Deployment Gates
- [ ] Secret exists in AWS Secrets Manager
- [ ] Secret contains all 6 required fields
- [ ] Secret values match provided credentials
- [ ] Secret is accessible by target services (DMS, etc.)
- [ ] No sensitive data exposed in logs or output
- [ ] Secret follows naming convention: `[environment]-dms-credentials`

---

## Final State Validation

### Success Criteria
- All quality gates passed
- Secret successfully deployed to correct environment
- Secret structure matches required format exactly
- Database credentials validated and accessible
- No credential exposure or security issues
- Secret ready for consumption by DMS and other services

```

---

## Related SOPs
- **SOP-C011**: Data Lake Deployment Strategy
- **SOP-C005**: Deployment Debugging
- **SOP-C006**: AI Challenge Protocol 
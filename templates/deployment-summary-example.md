## 📋 **PRODUCTION DEPLOYMENT PLAN**
### Target Stacks: `data-extraction`, `cdc-etl`, `supplementary-etl`

---

## 🎯 **Pre-Deployment Analysis**

### **Current State Assessment**
✅ **Existing Deployment Detected**: All three stacks have existing production directories in `cdktf.out/stacks/`:
- `data-lake-data-extraction-prod/`
- `data-lake-cdc-etl-prod/`  
- `data-lake-supplementary-etl-prod/`

🔄 **Deployment Type**: This is a **redeployment/update** rather than fresh deployment

### **Stack Dependencies** (following SOP-B003 architecture compliance)
```
supplementary-etl (foundation)
    ↓
data-extraction (depends on orchestration table)
    ↓
cdc-etl (depends on data-extraction + supplementary-etl)
```

### **Deployment Order** (following dependencies)
1. **supplementary-etl** (foundational infrastructure)
2. **data-extraction** (data source layer)
3. **cdc-etl** (processing layer)

---

## 🚀 **DEPLOYMENT EXECUTION PLAN**

Following **SOP-C011: Data Lake deployment Strategy**, here's the step-by-step execution:

### **Step 1: Environment Validation**

**Commands to Execute:**
```powershell
# Step 1.a: Clear existing environment variables
Remove-Item Env:AWS_PROFILE -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_ACCOUNT_NUMBER -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_ACCESS_KEY_ID -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_SECRET_ACCESS_KEY -ErrorAction SilentlyContinue;
Remove-Item Env:AWS_SESSION_TOKEN -ErrorAction SilentlyContinue;
Remove-Item Env:TF_VAR_environment -ErrorAction SilentlyContinue;

# Step 1.b: Set production environment variables
$env:AWS_PROFILE="prod"
$env:TF_VAR_environment="prod"

# Step 1.c: Verify SSO environment
aws sso login --profile prod
aws sts get-caller-identity
```

**Expected Output Validation:**
- AWS profile should show production account
- Environment variables should be set to `prod`

### **Step 2: Asset Preparation (Zippy)**

**Commands to Execute:**
```powershell
# Step 2.a: Validate prerequisites
echo "AWS_PROFILE: $env:AWS_PROFILE"
echo "TF_VAR_environment: $env:TF_VAR_environment"

# Step 2.b: Execute Zippy script
python ETL/Modules/Packaged/zippy.py

# Step 2.c: Taint resources (for redeployment)
# Will be executed per stack after synthesis
```

**Expected Changes:**
- ETL ZIP files updated with production configuration
- Account numbers and environment variables embedded in config files

### **Step 3: Stack Synthesis**

**Commands to Execute:**
```powershell
# Synthesize all three stacks
npx ts-node main.ts supplementary-etl data-extraction cdc-etl
```

**Expected Output:**
- Updated Terraform configurations in `cdktf.out/stacks/`
- Production-specific resource naming
- Correct environment tags

### **Step 4: Stack-by-Stack Deployment**

#### **4.1 Supplementary-ETL Stack**
```powershell
cd cdktf.out/stacks/data-lake-supplementary-etl-prod
terraform init
terraform plan
# Review plan output carefully
terraform apply --auto-approve
cd ../../../
```

#### **4.2 Data-Extraction Stack**
```powershell
cd cdktf.out/stacks/data-lake-data-extraction-prod
terraform init
terraform plan
# Review plan output carefully
terraform apply --auto-approve
cd ../../../
```

#### **4.3 CDC-ETL Stack**
```powershell
cd cdktf.out/stacks/data-lake-cdc-etl-prod
terraform init
terraform plan
# Review plan output carefully
terraform apply --auto-approve
cd ../../../
```

---

## 📊 **PLANNED CHANGES ANALYSIS**

### **Supplementary-ETL Stack Changes**
**Infrastructure Components:**
- 🗄️ **DynamoDB Orchestration Table**: `DataLake-ETL-Load-Status`
- 🏗️ **Data Catalog Databases**: 5 medallion architecture databases
- ⚙️ **Glue Foundation Jobs**: 3 orchestration and catalog setup jobs

**Expected Resources:**
- DynamoDB table for ETL coordination
- Glue databases: `datalake-raw`, `datalake-cleaned`, `datalake-datavault`, `datalake-validation`, `datalake-published-datamart`
- Foundation Glue jobs for orchestration seeding

### **Data-Extraction Stack Changes**
**Infrastructure Components:**
- 🔄 **DMS Replication Instance**: Production-class `dms.t3.medium` with 100GB storage
- 🌐 **VPC Infrastructure**: 3 private subnets across AZs with security groups
- 🔐 **IAM Roles**: DMS service roles with least-privilege access
- 📡 **VPC Endpoints**: S3 Gateway, Secrets Manager, and CloudWatch interfaces

**Expected Resources:**
- Multi-AZ DMS replication instance for high availability
- Network infrastructure with proper security groups
- DMS task configured for 30 production tables from `nexodus_std_nexus` schema

### **CDC-ETL Stack Changes**
**Infrastructure Components:**
- 🔧 **Lambda Functions**: CDC orchestrator and handler with 2048MB memory
- 📦 **Lambda Layers**: CDC processing modules and Delta Lake library
- ⏰ **EventBridge Rules**: 1-minute polling schedule for CDC processing
- 🔐 **IAM Policies**: Resource-scoped permissions for S3, DynamoDB, and Lambda

**Expected Resources:**
- Event-driven CDC processing pipeline
- Lambda functions with appropriate memory and timeout settings
- Automated scheduling for continuous change processing

---

## 🛡️ **SECURITY & COMPLIANCE VERIFICATION**

### **Resource Tagging** (SOP-B001 compliance)
All resources will be tagged with:
- `Environment: prod`
- `ManagedBy: cdktf`
- `Service: data-lake`
- `StackType: feature-stack`

### **IAM Security** (SOP-B003 compliance)
- ✅ Resource-scoped permissions (no wildcards)
- ✅ Least-privilege access patterns
- ✅ Cross-stack integration through specific ARNs
- ✅ Environment-specific resource isolation

### **Network Security**
- ✅ Private subnet deployment
- ✅ VPC endpoints for AWS service access
- ✅ Security groups with minimal required access
- ✅ No public internet access requirements

---

## ⚡ **DEPLOYMENT RISKS & MITIGATIONS**

### **Risk Assessment**
| Risk | Impact | Mitigation |
|------|--------|------------|
| **DMS Downtime** | High | Deploy during maintenance window |
| **Lambda Cold Starts** | Medium | Pre-warm functions after deployment |
| **Cross-Stack Dependencies** | High | Deploy in correct order (supplementary → data-extraction → cdc-etl) |
| **Resource Name Conflicts** | Low | Environment-specific naming prevents conflicts |

### **Rollback Strategy**
- Each stack can be rolled back independently
- Terraform state files maintained for point-in-time recovery
- Database schemas preserved during rollback

---

## 📈 **POST-DEPLOYMENT VALIDATION**

### **Success Criteria**
- [ ] All three stacks deploy without errors
- [ ] DMS replication instance shows "available" status
- [ ] Lambda functions pass test invocations
- [ ] EventBridge rules are active and scheduled
- [ ] Orchestration DynamoDB table is accessible
- [ ] All resources have correct production tags

### **Functional Testing**
- [ ] DMS can connect to source database
- [ ] CDC files are being processed automatically
- [ ] Data Vault tables are being updated
- [ ] Orchestration table reflects current load status

---

## 🎯 **ESTIMATED TIMELINE**

| Phase | Duration | Description |
|-------|----------|-------------|
| **Environment Setup** | 5 minutes | SSO login, environment variables |
| **Asset Preparation** | 3 minutes | Zippy script execution |
| **Synthesis** | 2 minutes | Terraform generation |
| **Supplementary-ETL** | 8 minutes | Foundation infrastructure |
| **Data-Extraction** | 15 minutes | DMS and network setup |
| **CDC-ETL** | 10 minutes | Lambda and EventBridge |
| **Validation** | 10 minutes | Post-deployment testing |
| **Total** | **~53 minutes** | Complete deployment |

---

## 📋 **QUALITY GATES CHECKLIST**

### **Pre-Deployment** (SOP-C011 compliance)
- [ ] Working directory is `IaC/`
- [ ] SSO session active for production
- [ ] AWS profile matches production environment
- [ ] Environment variables set correctly (`prod`)
- [ ] Target stacks exist in feature-stacks directory
- [ ] Zippy script executed successfully

### **Post-Deployment** (SOP-C011 compliance)
- [ ] All resources created successfully
- [ ] Resource tags include `Environment: prod`
- [ ] State files updated in respective stack directories
- [ ] Can run synthesis + plan again with no changes
- [ ] Expected resources accessible and functional

---

**Ready to proceed with deployment? The plan above follows all applicable SOPs and ensures a systematic, safe production deployment.** 
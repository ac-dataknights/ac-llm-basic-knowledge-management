# SOP-C008: New Source Table Integration

## Purpose and Scope
Provide operational steps to add a new source table to the ETL infrastructure configuration. 

This SOP covers updating IaC configuration files only. For deployment of these changes, use **SOP-C011: Data Lake Deployment Strategy**.

---

## Step 1: Environment Selection and Prerequisites

**Purpose**: Determine which environments need the new source table.
**Rationale**: Different environments may need different table configurations, and we need to avoid duplicate entries.

### Step 1.a: Environment Selection
Ask user to specify target environments:
- [ ] DEV
- [ ] UAT  
- [ ] PROD

### Step 1.b: Validate Working Directory
Ensure you are in the IaC directory:
```powershell
cd IaC
# Should show: /path/to/data-lake/IaC
```

### Step 1.c: Verify Source Table Name
Confirm the exact source table name (case-sensitive):
```
Source Table: {table_name}
```

**Quality Gates:**
- [ ] Working directory confirmed as IaC
- [ ] Source table name confirmed and documented
- [ ] Target environments selected and documented

---

## Step 2: Update DMS Table Mapping Configuration

**Purpose**: Add the new source table to DMS replication table mapping configuration for specified environments.
**Rationale**: DMS needs to know which tables to replicate from the source database.

### Step 2.a: Check Current DMS Configuration
Verify table is not already included:
```powershell
# Search for table in DMS config
Select-String -Path "IaC/feature-stacks/data-extraction/config/dms-config.ts" -Pattern "{table_name}"
```

### Step 2.b: Update DMS Configuration File
Edit file: `IaC/feature-stacks/data-extraction/config/dms-config.ts`

Add table to each selected environment in the `DATA_EXTRACTION_TABLES` object:

**Example:**
```typescript
{env} : [
  'dealer',
  // ... existing tables
  '{table_name}' // 👈 Add new table here
],
```

### Step 2.c: Validate DMS Configuration Syntax
Test the updated configuration:
```powershell
cd IaC
npx tsc --noEmit feature-stacks/data-extraction/config/dms-config.ts
```

**Quality Gates:**
- [ ] Table not already present in DMS configuration
- [ ] Table added to all specified environments
- [ ] TypeScript compilation succeeds with no errors
- [ ] Syntax validation passes

**Outcomes:**
- **Success**: Table added to DMS configuration with valid syntax
- **Failure**: TypeScript errors or table already exists. Review and fix syntax issues.

---

## Step 3: Update Load Status Seed Script

**Purpose**: Add the new table to the ETL Load Status seed script so it can be processed by full load ETL.
**Rationale**: The ETL orchestration system needs to know this table should be processed for DataVault loading.

### Step 3.a: Check Current Seed Script
Verify table is not already included:
```powershell
Select-String -Path "ETL/Glue/Jobs/DataCatalog_Builder/DataLake-ETL-Load-Status_Seed_Script.py" -Pattern "{table_name}"
```

### Step 3.b: Update Seed Script
Edit file: `ETL/Glue/Jobs/DataCatalog_Builder/DataLake-ETL-Load-Status_Seed_Script.py`

Add new table entry to the `tbl_list` array:
```python
tbl_list = [
    # ... existing entries
    ['{table_name}', True],  # 👈 Add this line (True = modelled in DataVault)
]
```

### Step 3.c: Validate Python Syntax
Test the updated script syntax:
```powershell
python -m py_compile "ETL/Glue/Jobs/DataCatalog_Builder/DataLake-ETL-Load-Status_Seed_Script.py"
```

**Quality Gates:**
- [ ] Table not already present in seed script
- [ ] Table added with `modelled_in_datavault = True`
- [ ] Python syntax validation passes
- [ ] File saved successfully

**Outcomes:**
- **Success**: Table added to seed script with correct parameters
- **Failure**: Python syntax errors or table already exists. Review and fix issues.

---

## Step 4: DataCatalog Table Validation

**Purpose**: Ensure the new table will be properly registered in Glue DataCatalog for Raw and Cleaned databases.
**Rationale**: ETL processes need DataCatalog metadata to process the table through the pipeline.

### Step 4.a: Check Raw DataCatalog Database
Verify if table exists in the Raw database (substitute `{env}` with dev/uat/prod):
```powershell
aws glue get-table --database-name "{env}_datalake-{raw|cleaned}" --name "{table_name}" 2>$null
if ($LASTEXITCODE -eq 0) {
    Write-Host "Table {table_name} found in {env} {raw|cleaned}" database"
} else {
    Write-Host "Table {table_name} NOT found in {env} {raw|cleaned}" database"
}
```


### Step 4.c: Update DataCatalog Population File (If Required)
**If table not found in either database**, the DataCatalog population file in S3 needs updating:

1. **Access the S3 file**: `s3://datalake-etlref-{account}/schema-info/schema_information.csv`
2. **Add table entry** with appropriate schema information
3. **Upload updated file** back to S3
4. **Note**: This file controls which tables the DataCatalog Builder script will process


**Quality Gates:**
- [ ] Checked Raw database for table existence
- [ ] Checked Cleaned database for table existence  
- [ ] If missing, S3 schema file updated with new table
- [ ] Updated schema file uploaded to S3

**Outcomes:**
- **Success**: Table exists in DataCatalog or S3 file updated for future population
- **Failure**: Table missing and S3 file not updated - manual intervention required

---

## Step 5: Final Validation and Summary

**Purpose**: Confirm all configuration changes are complete and syntactically correct.
**Rationale**: Ensure deployment will succeed and provide clear summary of changes made.

### Step 5.a: Validate All File Changes
Run comprehensive syntax validation:
```powershell
# Validate TypeScript
cd IaC
npx tsc --noEmit

# Validate Python files
python -m py_compile "ETL/Glue/Jobs/DataCatalog_Builder/DataLake-ETL-Load-Status_Seed_Script.py"
python -m py_compile "ETL/Glue/Jobs/DataCatalog_Builder/DataCatalog_Builder.py"
```

### Step 5.b: Generate Change Summary
Document what was modified:

**Files Modified:**
- [ ] `IaC/feature-stacks/data-extraction/config/dms-config.ts`
- [ ] `ETL/Glue/Jobs/DataCatalog_Builder/DataLake-ETL-Load-Status_Seed_Script.py`
- [ ] `ETL/Glue/Jobs/DataCatalog_Builder/DataCatalog_Builder.py` (if required)

**Environments Updated:**
- [ ] DEV (if selected)
- [ ] UAT (if selected)  
- [ ] PROD (if selected)

**Table Configuration:**
- **Source Table**: `{table_name}`
- **DMS Replication**: Enabled for selected environments
- **DataVault Processing**: Enabled (`modelled_in_datavault = True`)
- **DataCatalog Registration**: Configured

**Quality Gates:**
- [ ] All syntax validation passes
- [ ] No compilation errors
- [ ] Change summary documented
- [ ] All target environments updated consistently

**Outcomes:**
- **Success**: All configuration files updated successfully with valid syntax
- **Failure**: Syntax errors remain. Review and fix before proceeding.

---

## Next Steps: Deployment

**Configuration changes complete!** 

The new source table `{table_name}` has been configured for ETL processing in the {env}.

## Success Criteria

**Configuration Phase Success:**
- [ ] Source table added to DMS configuration for all target environments
- [ ] Table added to Load Status seed script with correct parameters
- [ ] DataCatalog script updated if required
- [ ] All syntax validation passes
- [ ] No compilation errors
- [ ] Change summary documented

### Ready to Deploy?

**To deploy these configuration changes:**

1. Follow **SOP-C011: Data Lake Deployment Strategy**
2. Deploy to each target environment in order (typically UAT first, then PROD)
3. Required Stacks include supplementary-etl and data-extraction
4. Validate deployment success before proceeding to next environment

---
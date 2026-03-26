# Lab M8.07 - GDPR & EU AI Act Data Protection Preview

**Repository:** https://github.com/cloud-engineering-bootcamp/ce-lab-gdpr-eu-ai-act-preview.git

**Activity Type:** Individual  
**Estimated Time:** 60 minutes

## Overview
This project demonstrates the implementation of GDPR-compliant data protection mechanisms using AWS S3. It covers data residency in the EU, encryption, access control, lifecycle management, and the execution of a real "Right to Erasure" workflow (Article 17).

## Objectives

- Enforce EU data residency (eu-west-1)
- Implement secure data storage with encryption (AES-256)
- Prevent public access to sensitive data
- Apply data classification using object tags
- Configure automated data retention (30 days)
- Execute GDPR Right to Access and Right to Erasure
- Provide compliance evidence for auditing

## Architecture

- AWS S3 for secure object storage
- AWS CLI for configuration and management
- Object Tagging for GDPR classification
- Lifecycle Policies for automated deletion
- Versioning for audit and recovery

## Implementation Steps

1. EU Data Residency
- Created S3 bucket in eu-west-1 (Ireland)
- Verified region to ensure GDPR compliance

2. Security Controls
- Enabled S3 Block Public Access (all 4 settings)
- Enforced server-side encryption (AES-256)
- Applied bucket policy to deny unencrypted uploads

3. Data Upload & Classification
- Uploaded simulated personal data (personal-data.csv)
- Applied GDPR tags:
- DataClassification=PersonalData
- GDPRArticle=4
- LegalBasis=Consent
- RetentionDays=30

4. Lifecycle Management
- Configured lifecycle rule:
  - Delete personal data after 30 days
  - Remove old versions after 7 days

5. Right to Access (Article 15)
- Exported user data (U003 – Clara Rossi)
- Stored in clara-rossi-export.csv

6. Right to Erasure (Article 17)
- Removed user U003 from dataset
- Uploaded cleaned dataset
- Deleted previous S3 object versions
- Verified complete removal

## Task

Create data export and deletion APIs for GDPR compliance, ensure EU data stays in EU regions, and document compliance measures.

## Step-by-Step Instructions

### Step 1: Implement Data Export API

```python
# export-data.py
import boto3
import json

def export_user_data(user_id):
    """GDPR Right to Access (Article 15)"""
    
    # Gather data from all systems
    user_data = {
        'profile': get_user_profile(user_id),
        'orders': get_user_orders(user_id),
        'activity': get_user_activity(user_id)
    }
    
    # Generate JSON export
    export_file = f'user-data-{user_id}.json'
    with open(export_file, 'w') as f:
        json.dump(user_data, f, indent=2)
    
    return export_file
```

### Step 2: Implement Data Deletion API

```python
# delete-data.py
def delete_user_data(user_id):
    """GDPR Right to Erasure (Article 17)"""
    
    # Delete from primary database
    delete_user_from_db(user_id)
    
    # Delete from S3
    s3 = boto3.client('s3')
    s3.delete_objects(
        Bucket='user-data-bucket',
        Delete={'Objects': [{'Key': f'users/{user_id}/'}]}
    )
    
    # Anonymize logs
    anonymize_logs(user_id)
    
    return {'status': 'deleted', 'user_id': user_id}
```

### Step 3: Ensure EU Data Residency

```hcl
# Terraform - EU-only resources
provider "aws" {
  region = "eu-west-1"  # Ireland
}

# Service Control Policy to prevent non-EU resources
resource "aws_organizations_policy" "eu_only" {
  name = "eu-data-residency"
  
  content = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Deny"
      Action = "*"
      Resource = "*"
      Condition = {
        StringNotEquals = {
          "aws:RequestedRegion" = [
            "eu-west-1", "eu-west-2", "eu-west-3",
            "eu-central-1", "eu-north-1"
          ]
        }
      }
    }]
  })
}
```

### Step 4: Document GDPR Compliance

Create `gdpr-compliance-doc.md`:

```markdown
# GDPR Compliance Documentation

## Data Processing Activities
- **Lawful Basis:** Consent and Contract
- **Data Categories:** Name, email, orders, activity logs
- **Retention Period:** 2 years after last activity
- **Data Processors:** AWS (DPA signed)

## Technical Measures
1. **Encryption:** All data encrypted at rest (S3, RDS)
2. **Access Control:** IAM policies, MFA required
3. **Data Residency:** EU customer data stored in eu-west-1
4. **Logging:** CloudTrail enabled for audit trail

## Data Subject Rights Implementation
- **Right to Access:** `/api/gdpr/export` endpoint
- **Right to Erasure:** `/api/gdpr/delete` endpoint
- **Right to Rectification:** User profile update API
- **Right to Portability:** JSON export format

## Breach Notification Process
1. Detection: GuardDuty, Security Hub
2. Notification: Within 72 hours to DPA
3. User notification: If high risk

## Data Protection Officer
- Contact: dpo@example.com
```

## Submission

- `export-data.py` and `delete-data.py` scripts
- Terraform configuration for EU residency
- `gdpr-compliance-doc.md`

## Verification Checklist

- [ ] Data export API implemented
- [ ] Data deletion API implemented
- [ ] EU-only SCP or policy configured
- [ ] GDPR compliance documented

## Project Files
- personal-data.csv → Original dataset
- personal-data-cleaned.csv → Dataset after deletion
- clara-rossi-export.csv → Exported user data
- gdpr-evidence.md → Compliance evidence document
- screenshots/ → Supporting proof

## Key Learnings
- GDPR compliance requires both technical controls and process validation
- Encryption must be enforced, not optional
- Data deletion must include all versions
- Data residency is critical for EU compliance
- Lifecycle policies help enforce retention automatically

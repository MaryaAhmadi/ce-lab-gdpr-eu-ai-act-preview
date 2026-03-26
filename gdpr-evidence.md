# GDPR Compliance Evidence
 
**Date:** 2026-03-26T13:32:22Z
**Engineer:** Sahebejan Ahmadi
**Bucket:** gdpr-personal-data-1774530925
**Region:** eu-west-1
 
---
 
## Technical Controls Implemented
 
| Control | GDPR Basis | Status | Evidence |
|---|---|---|---|
| Data residency: eu-west-1 | Article 46 — Data transfers | ✅ Compliant | Bucket location = eu-west-1 |
| S3 Block Public Access | Article 32 — Security measures | ✅ Enabled | All 4 settings = true |
| Encryption at rest (AES-256) | Article 32 — Encryption | ✅ Enforced | Default encryption + deny policy |
| Encryption in transit (HTTPS) | Article 32 — Encryption | ✅ Enforced | S3 enforces TLS by default |
| Data classification tags | Article 5 — Accountability | ✅ Applied | DataClassification=PersonalData |
| 30-day auto-deletion | Article 5(1)(e) — Storage limitation | ✅ Active | Lifecycle rule: gdpr-30-day-retention |
 
---
 
## Right to Erasure (Article 17)
 
**Data subject:** Clara Rossi (user_id: U003)  
**Request received:** 2026-03-26  
**Completed:** 2026-03-26 (same day — within the 30-day requirement)
 
**Actions taken:**
1. Exported a copy of U003 data before deletion (available on request)
2. Removed U003 row from personal-data.csv
3. Re-uploaded cleaned dataset with encryption
4. Deleted all previous S3 versions containing U003 data
5. Verified U003 is no longer present in active dataset
 
**Verification:** grep "U003" returned no results on current object version.
 
---
 
## Data Processing Activity
 
| Field | Value |
|---|---|
| Data category | Name, email, IP address, country |
| Legal basis | Consent (Article 6(1)(a)) |
| Retention period | 30 days (auto-deleted by lifecycle rule) |
| Storage location | S3 eu-west-1 (Ireland) |
| Processor | AWS (EU DPA signed) |
| Access control | IAM — least privilege |

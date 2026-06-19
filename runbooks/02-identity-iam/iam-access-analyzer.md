# Runbook: Deploy IAM Access Analyzer to Govern Cross-Account Trust

### Operational Overview
As infrastructure configurations expand, manual monitoring cannot reliably catch unapproved trust policy modifications or structural alterations on S3 buckets, IAM roles, and KMS keys. 

Deploying AWS IAM Access Analyzer enables a continuous automated assessment mechanism that flags any system resource allowing connectivity originating from outside our defined corporate zone of trust.

* **Official documentation**:
https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html
https://docs.aws.amazon.com/securityhub/latest/userguide/iam-controls.html

### CIS Benchmark: 
Ensure that IAM Access Analyzer is enabled for all regions to monitor IAM policies and identify resources with unintended external access.

### Risk:
Unintended external access and data exposure, an unauthorized third-party account or public entity exploits overly permissive resource policies to extract corporate data.

* **Without Access Analyzer**: Publicly or cross-account shared resources go unnoticed, creating long-term structural security blind spots across configuration baselines.
* **With Access Analyzer**: Logic-based scanning continuously surfaces unexpected access patterns and alerts administrators dynamically, allowing for misconfiguration closure. Threat is neutralized.

### Enabling IAM Access Analyzer
```
IAM → Access Analyzer → Create analyzer → Choose account type zone of trust → Click Create analyzer
```
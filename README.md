# AWS Infrastructure Compliance & Hardening Repository

This repository tracks the infrastructure compliance discovery, systemic threat validation, and technical remediation of security gaps identified during continuous AWS Prowler audits.

`This is go-to replica of the work I actioned contains generic checks, flow and remediation without revealing the original infratructure information to main confidentiality and align with security policies`

## Compliance Remediation Progress

| Control Identifier | Domain Target | Finding / Technical Gap | Remediation Strategy | Progress Status |
| :--- | :--- | :--- | :--- | :--- |
| **CIS EC2-8** | Compute / IMDS | Enforce IMDSv2 Token Protection | Required session token handshakes |  Complete |
| **CIS 1.2.4** | Identity / IAM | Missing EC2 IAM Instance Profiles | Standardized non-disruptive base role |  Complete |
| **CIS S3-InTransit** | Storage / S3 | Allow Insecure HTTP Configurations | Bucket policies denying non-SSL data | Complete |
| **EBS-Encryption** | Storage / EBS | Unencrypted Block Volumes at Rest | Snapshot migration & default enforcement | In Progress |
| **IAM-Analyzer** | Governance | Enable IAM Access Analyzer | Active external trust validation | In Progress |

---

##  Baseline  Workflow
For every non-compliant benchmark surfaced by Prowler, the engineering pipeline follows this loop:
1. **Threat Profiling:** Analyze business logic, active connections, and resource uptime dependencies.
2. **Control Plane Pre-Check:** Extract local environment metadata arrays to verify if data pipelines are active.
3. **Phased Change Window:** Run low-risk or non-disruptive additions immediately; isolate core state components (e.g., legacy servers, databases) into dedicated windows.
4. **Post-Change Verification:** Execute system token loop validations to guarantee compliance tools return green status flags.
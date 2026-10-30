# AWS Infrastructure Compliance & Hardening

This repository tracks infrastructure compliance discovery, threat validation, and technical remediation of security gaps identified through continuous **Prowler** audits on AWS.

> Note: This is a sanitized replica of real work performed. It contains generic checks, workflows, and remediation guidance without exposing any original infrastructure details, in line with security and confidentiality policies.

---

## Compliance Remediation Progress

| Control Identifier | Domain | Finding / Technical Gap | Remediation Strategy | Status |
|--------------------|--------|--------------------------|----------------------|--------|
| **CIS EC2-8** | Compute / IMDS | Enforce IMDSv2 Token Protection | Require session token handshakes | ✅ Complete |
| **CIS 1.2.4** | Identity / IAM | Missing EC2 IAM Instance Profiles | Standardized non-disruptive base role | ✅ Complete |
| **CIS S3-InTransit** | Storage / S3 | Insecure HTTP configurations allowed | Bucket policies denying non-SSL traffic | ✅ Complete |
| **EBS-Encryption** | Storage / EBS | Unencrypted block volumes at rest | Snapshot migration & default encryption enforcement | 🔄 In Progress |
| **IAM-Analyzer** | Governance | IAM Access Analyzer not enabled | Active external trust validation | 🔄 In Progress |

---

## Baseline Workflow

For every non-compliant finding surfaced by Prowler, the following engineering loop is followed:

1. **Threat Profiling** — Analyze business logic, active connections, and resource uptime dependencies
2. **Control Plane Pre-Check** — Extract environment metadata to verify whether data pipelines are active
3. **Phased Change Window** — Apply low-risk or non-disruptive changes immediately; isolate core components (legacy servers, databases) into dedicated windows
4. **Post-Change Verification** — Validate that compliance tools return green status

---

## Repository Structure

```
aws-prowler-compliance/
└── runbooks/
    ├── 01-compute-imds/
    │   └── IMDSv2-migration.md
    ├── 02-identity-iam/
    │   ├── iam-access-analyzer.md
    │   └── iam-instance-profiles.md
    └── 03-storage-security/
        ├── ebs-volume-encryption.md
        └── s3-enforce-tls.md
```

---

## Key Principles

- Prefer non-disruptive changes where possible
- Always verify post-remediation status with Prowler / AWS Config
- Document every control with clear before/after rationale
- Keep runbooks reusable and environment-agnostic

---

**Author**: Prasanth Narava  
**Focus**: AWS Security Compliance • CIS Benchmarks • Practical Hardening  
**Tooling**: Prowler  
**Last Updated**: October 2026

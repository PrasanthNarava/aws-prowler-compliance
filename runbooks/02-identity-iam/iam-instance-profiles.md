
# Runbook: Eliminate Plaintext Credentials via IAM Instance Profiles (CIS 1.2.4)

### Operational Overview
Lacking native IAM instance profiles forces teams to drop long-lived, static access keys (`AKIA...`) onto server disks or application `.env` files. If the server is compromised, these keys can be exfiltrated and run globally. Attaching an instance profile switches authorization to temporary short-lived tokens via local hypervisor injection.

### 🔍 Investigation Summary & Exceptions
Fleet-wide analysis was conducted to uncover hidden configuration files before blanket deployments.

* **Clean Baseline Fleet:** Direct local shell audits verified zero local `.aws/credentials` footprints and no attached control plane identities. It was approved to deploy a unified, shared infrastructure role to these instances with **$0 cost** and **zero downtime risk**.
* **Critical Exception :** DISCOVERED active, plaintext keys on disk. CloudTrail investigations revealed an automated Let's Encrypt DNS-01 certificate renewal challenge run via a 60-day cron window. Remediation is isolated after formal approval to prevent cutting active main site certificate renewal workflows.

### Remediation Path
1. Create a baseline centralized infrastructure profile named `sirius-ec2-infrastructure-base-role`.
2. Attach core managed management layers:
   * `AmazonSSMManagedInstanceCore` (Console-free secure terminal capability)
   * `CloudWatchAgentServerPolicy` (Unified metrics dispatch)
3. Map to instances via **EC2 Console** ➔ **Security** ➔ **Modify IAM Role**.

### Verification Protocols
Execute locally on targets:
```bash
TOKEN=$(curl -s -X PUT "[http://169.254.169.254/latest/api/token](http://169.254.169.254/latest/api/token)" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600") && \
curl -s -H "X-aws-ec2-metadata-token: $TOKEN" [http://169.254.169.254/latest/meta-data/iam/security-credentials/](http://169.254.169.254/latest/meta-data/iam/security-credentials/)

# Expected Output: sirius-ec2-infrastructure-base-role
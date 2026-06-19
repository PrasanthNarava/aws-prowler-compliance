# Runbook: Enforce IMDSv2 Token Signatures (CIS EC2-8)

### Operational Overview
AWS instances provisioned prior to May 2024 run with Instance Metadata Service Version 1 (IMDSv1) allowed by default. This creates a vulnerability vectors for Server-Side Request Forgery (SSRF), where an adversary exploits a web layer weakness to pull cloud credentials using plain HTTP `GET` loops.

Upgrading to IMDSv2 completely neutralizes this threat by requiring a local state session handshake via a `PUT` transaction and custom headers.

* **Official documentation**: 
https://maturitymodel.security.aws.dev/en/2.-foundational/imdsv2/

https://docs.aws.amazon.com/securityhub/latest/userguide/ec2-controls.html#ec2-8


### Threat Evaluation & Investigation Loop
Before altering the control plane, inspect whether the host is actively executing legacy metadata tracking.

1. Navigate to **CloudWatch** ➔ **All Metrics** ➔ **EC2 Per-Instance Metrics**.
2. Add the metric **`MetadataNoToken`** across the target host/instance.
3. **Evaluation Threshold:**
   * If the sum is exactly `0`, no legacy processes depend on IMDSv1; upgrade immediately.
   * If the sum is `> 0`, evaluate local runtime application engines or legacy AWS CLI tools before forcing token updates.

### Remediation Path

* **Pre-remediation Test (V1 Active):**
```bash
  curl -I [http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/)
  # Returns 200 OK along with plaintext attributes
```

* **AWS Management Console:** Select EC2 Instance ➔ **Actions** ➔ **Security** ➔ **Modify Instance Metadata Options** ➔ Enforce **IMDSv2 Required**.

* **Steps to IMDSv2 upgrade**
**AWS Management Console:** Select EC2 Instance ➔ **Actions** ➔ **Security** ➔ **Modify Instance Metadata Options** ➔ Enforce **IMDSv2 Required**

* **Post-remedation checks**: 
    * IMDSv1 Denial
    ```
    curl -I [http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/)
   # Expected Output: HTTP/1.0 401 Unauthorized
    ```
    * IMDSv2 output 
    ```
    TOKEN=$(curl -s -X PUT "[http://169.254.169.254/latest/api/token](http://169.254.169.254/latest/api/token)" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600") && \
   curl -s -H "X-aws-ec2-metadata-token: $TOKEN" [http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/)
   # Expected Output: Correct listing of metadata attributes
   ```




# Runbook: Enforce Secure Transport for S3 Buckets

### Operational Overview
By default, Amazon S3 endpoints receive API requests over both unencrypted HTTP and encrypted HTTPS channels. Allowing plain HTTP traffic leaves objects susceptible to Man-In-The-Middle (MITM) sniffing or packet injection attacks. Secure systems must feature explicit, global resource restrictions denying any transport payload hitting the service without active SSL/TLS handshakes.

### Scope Analysis of Affected Targets
* **`cf-templates`:** Stagnant asset (No writes since Feb 2020). 
Action: Turn ON Block Public Access (BPA), apply HTTP Deny logic.
* **`aws-config`:** Highly active environment logger. 
Action: Inject HTTP Deny statement block directly into its working bucket policy.
* **`backups`:** 30 TB of critical system recovery image volumes in Glacier Deep Archive. Action: Enforce explicit transport policy immediately.
* **`cloud-old-instances`:** Stagnant 100 GB archive container. 
Action: Apply missing bucket policy structure[cite: 3].

### Remediation Path
Navigate to S3 Permissions console interface and append this evaluation logic block:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EnforceSSLRequestsOnly",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::TARGET-BUCKET-NAME",
        "arn:aws:s3:::TARGET-BUCKET-NAME/*"
      ],
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    }
  ]
}
```

* **Post-Remediation Checks**
```
curl -I [http://TARGET-BUCKET-NAME.s3.amazonaws.com/](http://TARGET-BUCKET-NAME.s3.amazonaws.com/)
# Expected Output: 403 Forbidden (Access Denied)
```
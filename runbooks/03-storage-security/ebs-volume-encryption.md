# Runbook: EBS Volume Encryption Migration & Fleet Reinforcement

### Operational Overview
Unencrypted EBS volumes store state components on bare block drives in plaintext. Compromised console keys or rogue configuration exports can lead to direct drive-snapshot copies being mounted globally on unauthenticated systems without data-at-rest boundary challenges

* **Official documentation**:
https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html
https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-ebs-encryption-by-default.html


### Risk Profile & Uptime Dependencies
Because AWS does not support in-place encryption changes for active assets, execution requires a state swap cycle (Snapshot ➔ Copy with KMS ➔ Re-mount volume). 

The following infrastructure nodes are classified as **Extreme Risk** due to legacy kernel versions and multi-year systemic uptimes:
* `Filestore.xys` (Ubuntu 14.04 — Active uptime up 5+ years)
* `LDAP.xys` & `LDAP1.xys` (Ubuntu 14.04 — Active uptime up 3+ years)
* `WEBSITE.xys` (Ubuntu 16.04 — Active uptime up 3+ years)

### Remediation Step-by-Step Workflow

#### **Step 1: Account-Level Global Enforcement**
Enable the default account-level flag ensuring any future disk provisioned natively configures encryption:
```bash
aws ec2 enable-ebs-encryption-by-default --region your-region
```

#### **Step 2: Existing Disk Migration Blueprint (Console/CLI)
Map and locate target block identities:

```
   aws ec2 describe-volumes --query "Volumes[*].{ID:VolumeId,AZ:AvailabilityZone}" --output table
```
Stop target instance runtime entirely to protect filesystem state integrity before drive detachment.

Execute snapshot creation sequence
```
   aws ec2 create-snapshot --volume-id <volume-id>
```
Perform encrypted transposition clone using our Customer Managed KMS Key[cite: 1]:

```
   aws ec2 copy-snapshot --source-region <region> --source-snapshot-id <snapshot-id> --encrypted --kms-key-id <kms-key-id>
```
Materialize new disk asset within matching Availability Zone target blocks:

```
   aws ec2 create-volume --snapshot-id <encrypted-snapshot-id> --availability-zone <az> --volume-type gp3
``` 
Detach old plaintext drive, associate new encrypted drive mapping matching initial configuration arrays, and restart infrastructure.

Post Checks
```
aws ec2 describe-volumes --query "Volumes[*].{ID:VolumeId,Encrypted:Encrypted}" --output table
# Verify every target volume outputs: True
```
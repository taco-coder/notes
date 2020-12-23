# Storage Gateway
Allows you to store data on the AWS cloud and still be able to use it with your on-premises software apps and IT environment. Downloaded as VM image installed on a host in your datacenter. It supports VMware or Microsoft Hyper-V. Once the VM is installed and configured with AWS account info, you can use the AWS management console to create the gateway option you want.

### 3 types of gateway
- File gateway (NFS & SMB).
    - Stored in S3 buckets.
    - Accessed through Network File System (NFS) mount point.
- Volume gateway (iSCSI).
    - Volumes are stored in EBS snapshots.
    - Stored volumes.
    - Cached volumes.
- Tape Gateway (VTL).



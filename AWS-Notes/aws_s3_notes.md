# S3
S3 is for object files only.

'Buckets', the storage container name in S3, are just folders where you can store files.

Bucket names are universal namespace so they have to be unique.


Objects consist of
- Key: name of object
- Value: the data that makes up the file
- Version ID: version number of that file
- Metadata: data about data in the file


You can enable MFA to delete objects.

`Transfer acceleration` enables fast and easy transfer over the Amazon network to edge locations vs using the regular Internet.



S3 buckets are private by default.


You can have version control enabled and have server side encryption.


You can setup access control using bucket policies and Access Control Lists.


You can have logs of who accesses buckets and makes what changes.

Encryption in transit is acheived by SSL/TLS

### Encryption At Rest (Server Side):
- S3 managed keys - SSE-S3
- AWS Key management service
- Server side encryption with customer provided Keys

---

Client side encryption
: upload encrypted files


Encryption can be found under Properties in the bucket settings.

Once versioning is enabled, it can't be disabled. Only suspended.

Versioning supports MFA to delete objects.

Each version of an uploaded file is private by default. You need to make them public individually.



You can create life cycle rules for each bucket to outline what happens to files after a defined period of time ie transition to different storage tiers if not accessed after X days, delete after Y days, etc.

### S3 Object Lock: Write Once, Read Many (WORM):
- Prevents objects being deleted or modified without required permissions.
- Governance mode: users can alter a file without special permissions.
- Compliance mode: can't be alter by ANY user, even root account. It's locked for the duration of the protection period (defined at creation).
- Legal hold: prevents an object version from being overwritten or deleted. No protection period; can be removed and added by any user who has the s3:PutObjectLegalHold permission.
    
### 3 ways to share S3 bucket access
- Using bucket policies and IAM access (programmatic access only).
- Using Bucket ACLs and IAM access to individual objects (programmatic access only).
- Cross account access through IAM users using Roles (programmatic AND console access).

### Cross Region Replication
1. You need to create a destination bucket before enabling cross region replication.
2. Can be found under `Management` in bucket settings, there is a replication rules tab.
    - Cross Region Replication only starts working with new objects from the point of creation/enablement, it won't replicate past objects in the source bucket.
    - It doesn't replicate source object past versions and doesn't replicate source object viewership permissions ie a public file in source bucket in US will default to private in destination bucket in Brazil.

### S3 Transfer Acceleration
Uses Cloudfront Edge Network to accelerate uploads to S3; faster than regular Internet transfer because it uses AWS backbone network.
    


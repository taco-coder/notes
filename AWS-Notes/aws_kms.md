# Key Management System
Regional secure key management and encryption and decryption. Manages customer master keys (CMKs). Ideal for S3 objects, database passwords and API keys stored in Systems Manager Parameter Store.
You can encrypt and decrypt data up to 4KB in size and it's intergrated with most AWS services. You pay per API call.

3 types of CMKs:
- AWS Managed CMK: Free; used by default if you pick encryption in most AWS services. Only that service can use them directly.
- Customer Managed CMK: Allows key rotation; controlled via key policies and can be enabled/disabled.
- AWS Owned CMK: Used by AWS on a shared basis across many accounts; you typically won't see these.

### Symmetric vs. Asymmetric CMKs
Symmetric:
- Same key used for encryption and decryption
- AES-256
- Never leaves AWS unencrypted
- Must call the KMS APIs to use
- AWS services integrated with KMS use symmetric CMKs
- Encrypt, decrypt, and re-encrypt data
- Generate data keys, data key pairs, and random byte strings
- Import your own key material


Assymetric:
- Mathematically related public/private key pair
- RSA and elliptic-curve cryptography (ECC)
- Private key never leaves AWS unencrypted
- Must call the KMS APIs to use private key
- Download the public key and use outside AWS
- Used outside AWS by users who can't call KMS APIs
- AWS services integrated with KMS do not support asymmetric CMKs
- Sign messages and verify signatures

### Key Policy Examples
Grants AWS account (rootu user) full access to the CMK.
```
{
    "Sid": "Enable IAM User Permissions",
    "Effect": "Allow",
    "Principal": {"AWS": "arn:aws:iam::111122223333:root"}.
    "Action": "kms:*",
    "Resource": "*"
}
```

Grants IAM role access to crypto actions for encrypting and decrypting data.
```
{
    "Sid": "Allow use of the key",
    "Effect": "Allow",
    "Principal": {"AWS": "arn:aws:iam::111122223333:role/EncryptionApp"},
    "Action": [
        "kms:DescribeKey",
        "kms:GenerateDataKey",
        "kms:Encrypt",
        "kms:ReEncrypt*",
        "kms:Decrypt"
    ],
    "Resource": "*"
}
```
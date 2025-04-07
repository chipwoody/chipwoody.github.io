
---

# 📖 AWS CloudFormation Lab Guide (v2)  
### Part 1 & Part 2: CloudFormation Basics and Encryption Enhancements

---

## 📋 Overview

In this phase, you will:

✅ Write your first CloudFormation template (`s3-bucket.yaml`)  
✅ Deploy it using AWS CLI (under your Developer IAM user)  
✅ Enhance it with S3 encryption (first SSE-S3, then SSE-KMS)

All commands are shown for **both Bash** and **PowerShell**.

---

# 🛠️ Part 1: CloudFormation Basics — Create S3 Bucket Template

---

## 1. Create a Working Directory

Create a new local directory for this project.

### 🔵 Bash (Linux/macOS)

```bash
mkdir cloudformation-lab
cd cloudformation-lab
```

### 🟣 PowerShell (Windows)

```powershell
mkdir cloudformation-lab
cd cloudformation-lab
```

---

## 2. Create Your First CloudFormation Template

Create a file named **`s3-bucket.yaml`** inside your working directory.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Basic S3 Bucket

Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-first-cfn-bucket-12345
```

✅ **Important**:  
S3 bucket names must be **globally unique** — append random numbers or your initials if needed.

---

## 3. Deploy the CloudFormation Stack

Using the AWS CLI, deploy the template to AWS.

### 🔵 Bash (Linux/macOS)

```bash
aws cloudformation deploy \
  --template-file s3-bucket.yaml \
  --stack-name my-s3-bucket-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

### 🟣 PowerShell (Windows)

```powershell
aws cloudformation deploy `
  --template-file s3-bucket.yaml `
  --stack-name my-s3-bucket-stack `
  --capabilities CAPABILITY_NAMED_IAM
```

✅ **Expected Result**:
- CloudFormation stack `my-s3-bucket-stack` created.
- S3 bucket visible in AWS Console → S3.

---

# 🧹 Troubleshooting

| Problem | Fix |
|---------|----|
| `REVIEW_IN_PROGRESS` hang | Ensure you passed `--capabilities CAPABILITY_NAMED_IAM` |
| Bucket already exists error | Rename your BucketName to something unique |

---

# ✅ Part 1 Complete

✅ You have now:
- Created a YAML CloudFormation template.
- Deployed your first AWS resource (S3 bucket).

---

# 🛠️ Part 2: Add S3 Bucket Encryption (First with SSE-S3, Then SSE-KMS)

---

## 1. Add Server-Side Encryption (SSE-S3)

Modify your `s3-bucket.yaml` to add default encryption:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: S3 Bucket with SSE-S3 Encryption

Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-first-cfn-bucket-12345
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256
```

✅ **This uses AWS S3-managed keys (AES256).**

---

## 2. Update the CloudFormation Stack

Deploy the updated template.

### 🔵 Bash (Linux/macOS)

```bash
aws cloudformation deploy \
  --template-file s3-bucket.yaml \
  --stack-name my-s3-bucket-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

### 🟣 PowerShell (Windows)

```powershell
aws cloudformation deploy `
  --template-file s3-bucket.yaml `
  --stack-name my-s3-bucket-stack `
  --capabilities CAPABILITY_NAMED_IAM
```

✅ **Expected Result**:
- S3 bucket now defaults to AES256 encryption.
- Visible under S3 Console → Bucket Properties → Default encryption.

---

# 🔥 Bonus — Upgrade to SSE-KMS with Customer-Managed Key

Next, enhance security by encrypting using your own KMS key.

---

## 3. Modify Template to Add KMS Key

Update `s3-bucket.yaml`:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: S3 Bucket with SSE-KMS Encryption

Resources:

  MyKMSKey:
    Type: AWS::KMS::Key
    Properties:
      Description: KMS key for S3 bucket encryption
      EnableKeyRotation: true
      KeyPolicy:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              AWS: !Sub arn:aws:iam::${AWS::AccountId}:root
            Action: "kms:*"
            Resource: "*"

  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-first-cfn-bucket-12345
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: aws:kms
              KMSMasterKeyID: !Ref MyKMSKey
```

✅ **Now your S3 bucket will use a customer-managed KMS key** created by your CloudFormation stack.

---

## 4. Deploy the Updated Stack

### 🔵 Bash (Linux/macOS)

```bash
aws cloudformation deploy \
  --template-file s3-bucket.yaml \
  --stack-name my-s3-bucket-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

### 🟣 PowerShell (Windows)

```powershell
aws cloudformation deploy `
  --template-file s3-bucket.yaml `
  --stack-name my-s3-bucket-stack `
  --capabilities CAPABILITY_NAMED_IAM
```

✅ **Expected Result**:
- S3 bucket encrypts objects using **your KMS key**.
- You can see the KMS key in AWS Console → KMS → Customer Managed Keys.

---

# 📈 Bonus: Add a KMS Alias (optional but professional)

You could also add an alias resource inside your CloudFormation file to name the key nicely — example:

```yaml
MyKMSAlias:
  Type: AWS::KMS::Alias
  Properties:
    AliasName: alias/my-s3-bucket-key
    TargetKeyId: !Ref MyKMSKey
```

---

# 🧹 Troubleshooting

| Problem | Fix |
|---------|----|
| KMS permissions errors | Ensure your Developer IAM policy includes `kms:*` permissions |
| Bucket conflicts | If deployment fails, try deleting the old stack first |

---

# ✅ Part 2 Complete

✅ You now:
- Upgraded bucket security.
- Learned to create and use a KMS key in CloudFormation.

---

# 🚀 Coming Next:  
📦 Part 3: **Setting up CodeStar Connection (GitHub → AWS Integration)**  
📦 Part 4: **Building CodePipeline using CloudFormation**  
📦 Part 5: **Pushing GitHub changes → Auto-Deploy via AWS**

---

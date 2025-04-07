
---

# 📖 AWS CloudFormation Lab Guide (v2)  
### Part 1: CloudFormation Basics Create s3 Buket

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

# 🚀 Coming Next:  
📦 Part 3: **Setting up CodeStar Connection (GitHub → AWS Integration)**  
📦 Part 4: **Building CodePipeline using CloudFormation**  
📦 Part 5: **Pushing GitHub changes → Auto-Deploy via AWS**

---

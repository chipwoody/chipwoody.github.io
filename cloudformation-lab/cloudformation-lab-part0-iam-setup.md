
---

# 📖 AWS CloudFormation Lab Guide (v2)  
### Part 0: IAM Setup for CloudFormation Lab

---

## 📋 Overview

Before beginning the CloudFormation and CodePipeline lab, an **Administrator IAM user** must set up a **Developers IAM Group** and a **Developer IAM User** for controlled CLI-only access.

✅ This ensures students use scoped permissions following AWS security best practices.

---

## 🛠 Prerequisites

- AWS account with **Admin** permissions (root or admin IAM user).
- AWS Console access.
- AWS CLI installed locally.

---

## 1. Create a Custom IAM Policy: `CloudFormationDeploy`

✅ This policy grants the **minimum permissions** needed to complete the lab (CloudFormation, S3, KMS, IAM roles, CodePipeline, CodeStar Connections).

### Steps:

1. Navigate to AWS Console → **IAM** → **Policies** → **Create Policy**.
2. Choose **JSON** tab and paste the following:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudformation:CreateStack",
        "cloudformation:UpdateStack",
        "cloudformation:DeleteStack",
        "cloudformation:DescribeStacks",
        "cloudformation:ListStacks",
        "cloudformation:GetTemplate",
        "cloudformation:CreateChangeSet",
        "cloudformation:ListChangeSets",
        "cloudformation:DescribeChangeSet",
        "cloudformation:ExecuteChangeSet",
        "cloudformation:GetTemplateSummary"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:CreateBucket",
        "s3:PutBucketPolicy",
        "s3:PutBucketVersioning",
        "s3:GetBucketLocation",
        "s3:ListBucket",
        "s3:DeleteBucket",
        "s3:PutEncryptionConfiguration",
        "s3:ListAllMyBuckets"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "kms:CreateKey",
        "kms:PutKeyPolicy",
        "kms:EnableKeyRotation",
        "kms:DescribeKey",
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:ListKeys",
        "kms:ListAliases",
        "kms:CreateAlias"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:DeleteRolePolicy",
        "iam:PutRolePolicy",
        "iam:GetRole",
        "iam:PassRole"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "codepipeline:CreatePipeline",
        "codepipeline:UpdatePipeline",
        "codepipeline:DeletePipeline",
        "codepipeline:GetPipeline"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "codestar-connections:PassConnection"
      ],
      "Resource": "*"
    }
  ]
}
```

3. Name the policy **`CloudFormationDeploy`**.
4. Click **Create Policy**.

---

## 2. Create an IAM Group: `Developers`

1. Navigate to AWS Console → **IAM** → **User Groups** → **Create Group**.
2. Name the group **`Developers`**.
3. Attach the **`CloudFormationDeploy`** policy.
4. Create the group.

✅ The group will now have all permissions needed to complete this lab but nothing more.

---

## 3. Create a Developer IAM User

1. Navigate to AWS Console → **IAM** → **Users** → **Add Users**.
2. User details:
   - Username: **developer**
   - Access type: **Programmatic access only** (✅).
   - No Console access (uncheck AWS Management Console access).

3. Add user to the **`Developers`** group.

4. On the final screen, **download** the user's:
   - **Access Key ID**
   - **Secret Access Key**

✅ These credentials will be used to configure the AWS CLI.

---

## 4. Configure the AWS CLI with Developer User Credentials

After downloading the access keys:

Open your terminal and run:

### 🔵 Bash (Linux/macOS)

```bash
aws configure
# Provide the Access Key ID
# Provide the Secret Access Key
# Default region: us-east-1
# Output format: json
```

### 🟣 PowerShell (Windows)

```powershell
aws configure
# Provide the Access Key ID
# Provide the Secret Access Key
# Default region: us-east-1
# Output format: json
```

✅ After this, **all AWS CLI commands** will run under the limited **Developer** IAM user.

---

## ✅ Expected Result

You now have:
- A Developers group with a customer-managed policy.
- A Developer user configured for CLI-only access.
- AWS CLI authenticated as Developer user.
- No console login permissions (real-world style automation only).

---

## 🧹 Troubleshooting Tips

| Problem | Solution |
|---------|----------|
| Cannot create resources | Ensure `CloudFormationDeploy` policy attached to Developers group |
| CLI errors about permissions | Double-check AWS CLI credentials are from Developer user |
| Wrong region errors | Verify `aws configure` region is `us-east-1` |

---

# ✅ Part 0: IAM Setup - Complete

You are ready to move into:
- Writing and deploying your first CloudFormation template (Part 1).

---

# 📣 Next Up

**👉 Part 1: CloudFormation Basics (S3 Bucket Creation & Deployment)**

- We'll start building your first YAML template
- Deploy it using CLI
- Cover both Bash and PowerShell examples

---

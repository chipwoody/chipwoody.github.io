Awesome — let's move straight into **Part 5**, and we'll keep it clean for your next GitHub Pages file. 🚀

---

# 📖 AWS CloudFormation Lab Guide (v2)  
### Part 5: GitHub Push → Trigger Auto-Deployment

---

## 📋 Overview

In this part, you will:

✅ Push a change to your GitHub repo.  
✅ Watch your CodePipeline automatically trigger.  
✅ See AWS CloudFormation deploy the updated template automatically.

**Congratulations** — this is the point where the full system is working end-to-end!

---

# 🛠️ Part 5: Test the Auto-Deployment Pipeline

---

## 1. Make a Small Update to Your CloudFormation Template

We want a **small but valid** change to trigger a deployment.

Open your `s3-bucket.yaml` file.

✅ Add a harmless comment at the top:

```yaml
# Lab Update: Testing automatic deployment

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

✅ **Why a comment?**
- It modifies the file enough to trigger the GitHub webhook.
- No actual AWS resource change needed (safe test).

---

## 2. Commit and Push Your Changes

### 🔵 Bash (Linux/macOS)

```bash
git add s3-bucket.yaml
git commit -m "Test: Update template to trigger CodePipeline deployment"
git push origin main
```

### 🟣 PowerShell (Windows)

```powershell
git add .\s3-bucket.yaml
git commit -m "Test: Update template to trigger CodePipeline deployment"
git push origin main
```

✅ **Expected Result**:
- GitHub receives the push.
- GitHub automatically triggers CodePipeline through CodeStar Connection webhook.

---

## 3. Watch the Pipeline in AWS Console

Navigate to:
- **AWS Console** → **CodePipeline** → **your pipeline**.

✅ You should see a new execution automatically start:
- **Source** stage → success (pulls latest code).
- **Deploy** stage → success (CloudFormation runs the updated template).

If you click into CloudFormation Console:
- The stack should show **UPDATE_IN_PROGRESS** briefly, then **UPDATE_COMPLETE**.

---

# 🎯 Congratulations!

✅ You've now fully automated:

- GitHub commits → AWS CodePipeline trigger → CloudFormation deploy!

✅ Real DevOps lifecycle completed!

---

# 🧹 Troubleshooting Tips

| Problem | Solution |
|---------|----------|
| No pipeline trigger | Check if the GitHub push reached GitHub webhooks (repo → Settings → Webhooks) |
| Source stage fails | Check GitHub connection status and branch settings |
| Deploy stage fails | Check CloudFormation template syntax (AWS CLI `cfn-lint` can help) |

---

# 🛠️ Bonus Tip (Optional)

Later you can:
- Add **CloudFormation Linting** stage with AWS CodeBuild.
- Add **approval stages** before deploys.
- Deploy **to multiple environments** (Dev, Test, Prod) based on GitHub branches.

---

# ✅ Part 5 Complete

✅ Your infrastructure is now fully **GitOps-enabled**:  
Infrastructure as Code + CI/CD pipelines working together.

---

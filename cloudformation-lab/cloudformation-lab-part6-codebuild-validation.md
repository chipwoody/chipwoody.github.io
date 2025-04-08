
---

# 📖 AWS CloudFormation Lab Guide (v2)  
### Part 6: Add CodeBuild Stage for Template Validation

---

## 📋 Overview

In this part, you will:

✅ Add a **CodeBuild stage** to your CodePipeline.  
✅ Automatically **validate CloudFormation templates** using `cfn-lint` before deploying them.  
✅ Ensure only **valid, tested templates** move into production.

**Real-world best practice**!

---

# 🛠️ Part 6: CloudFormation Validation Using CodeBuild

---

## 1. Create a Buildspec File

AWS CodeBuild uses a **buildspec.yml** file to define build steps.

✅ In your GitHub repo root, create a file:

```plaintext
buildspec-cfn-validate.yml
```

Paste this inside:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      python: 3.x
    commands:
      - pip install cfn-lint
  build:
    commands:
      - echo "Validating CloudFormation template..."
      - cfn-lint s3-bucket.yaml
```

✅ This will:
- Install `cfn-lint`
- Validate your `s3-bucket.yaml` file

---

## 2. Update Your CodePipeline CloudFormation Template

✅ Open your `cfn-codepipeline.yaml` file and update it.

---

### Add New Resources

Add a **CodeBuild Project** and **CodeBuild Role** under your `Resources:` block:

```yaml
CodeBuildRole:
  Type: AWS::IAM::Role
  Properties:
    AssumeRolePolicyDocument:
      Version: "2012-10-17"
      Statement:
        - Effect: Allow
          Principal:
            Service:
              - codebuild.amazonaws.com
          Action:
            - sts:AssumeRole
    Policies:
      - PolicyName: CodeBuildPolicy
        PolicyDocument:
          Version: "2012-10-17"
          Statement:
            - Effect: Allow
              Action:
                - codebuild:*
                - s3:GetObject
                - s3:GetObjectVersion
              Resource: "*"
            - Effect: Allow
              Action:
                - logs:CreateLogGroup
                - logs:CreateLogStream
                - logs:PutLogEvents
              Resource: "*"

CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Name: CloudFormationValidation
    Source:
      Type: CODEPIPELINE
      BuildSpec: buildspec-cfn-validate.yml    # << CORRECT WAY
    Artifacts:
      Type: CODEPIPELINE
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:6.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt CodeBuildRole.Arn
```

✅ **Notice**:
- `BuildSpec: buildspec-cfn-validate.yml` is now inside the **Source** block of the **CodeBuild Project** — this is the correct way.

---

### Update Pipeline Stages

✅ In your `Stages:` section of `MyPipeline`, add the new **Validate stage**:

```yaml
Stages:
  - Name: Source
    Actions:
      - Name: GitHubSource
        ...

  - Name: Validate
    Actions:
      - Name: ValidateTemplate
        ActionTypeId:
          Category: Build
          Owner: AWS
          Provider: CodeBuild
          Version: "1"
        InputArtifacts:
          - Name: SourceOutput
        OutputArtifacts:
          - Name: ValidateOutput
        Configuration:
          ProjectName: !Ref CodeBuildProject
        RunOrder: 1

  - Name: Deploy
    Actions:
      - Name: CFNDeploy
        ...
```

✅ **Key Correction**:
- The Validate stage `Configuration:` should **only reference `ProjectName`** — **no BuildSpec setting here**.

---

## 3. Redeploy Your CodePipeline Stack

Use AWS CLI to deploy the updated CloudFormation template:

### 🔵 Bash (Linux/macOS)

```bash
aws cloudformation deploy \
  --template-file cfn-codepipeline.yaml \
  --stack-name my-codepipeline-stack \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
    GitHubConnectionArn=... \
    GitHubOwner=... \
    GitHubRepo=... \
    GitHubBranch=main
```

### 🟣 PowerShell (Windows)

```powershell
aws cloudformation deploy `
  --template-file cfn-codepipeline.yaml `
  --stack-name my-codepipeline-stack `
  --capabilities CAPABILITY_NAMED_IAM `
  --parameter-overrides `
    GitHubConnectionArn=... `
    GitHubOwner=... `
    GitHubRepo=... `
    GitHubBranch=main
```

---

## 4. Test It

✅ Push a small update to your repo (even just a comment).

✅ Pipeline will now:

- **Source** → GitHub
- **Validate** → CodeBuild runs `cfn-lint`
- **Deploy** → CloudFormation updates

✅ All automatic!

---

# 📣 Common Issues & Fixes

| Problem | Fix |
|---------|----|
| CodeBuild cannot find buildspec | Ensure you set `BuildSpec` in CodeBuild project, not in pipeline |
| Validate stage fails | Check CodeBuild logs for `cfn-lint` output |
| Permissions error | Ensure CodePipelineRole has `codebuild:StartBuild`, `codebuild:BatchGetBuilds` permissions |

---

# ✅ Part 6 Corrected and Complete

✅ You now have a working Validate stage using CodeBuild and `cfn-lint`!  
✅ Pipeline is Source → Validate → Deploy fully automated and tested!

---

Awesome — let’s roll straight into **Part 4**! 🚀  
(Structured perfectly so you can paste this into a new `.md` file for your site.)

---

# 📖 AWS CloudFormation Lab Guide (v2)  
### Part 4: Build CodePipeline via CloudFormation

---

## 📋 Overview

In this part, you will:

✅ Write a CloudFormation template that creates a **CodePipeline**.  
✅ The pipeline will:
- Pull your CloudFormation templates from GitHub (via CodeStar Connection).
- Automatically deploy the template using CloudFormation whenever a change is pushed.

✅ You'll also deploy this new pipeline using the AWS CLI.

---

# 🛠️ Part 4: Creating CodePipeline as Code

---

## 1. Create the Pipeline CloudFormation Template

In your project folder, create a new file named:

```plaintext
cfn-codepipeline.yaml
```

Paste this full YAML content inside:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CodePipeline to auto-deploy CloudFormation template from GitHub

Parameters:
  GitHubConnectionArn:
    Type: String
    Description: ARN of the CodeStar Connection

  GitHubOwner:
    Type: String
    Description: GitHub username or organization

  GitHubRepo:
    Type: String
    Description: GitHub repository name

  GitHubBranch:
    Type: String
    Default: main
    Description: GitHub branch name (default is main)

Resources:

  CodePipelineRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - codepipeline.amazonaws.com
                - cloudformation.amazonaws.com
            Action:
              - sts:AssumeRole
      Policies:
        - PolicyName: CodePipelinePolicy
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - s3:GetObject
                  - s3:GetObjectVersion
                  - s3:PutObject
                Resource: "*"
              - Effect: Allow
                Action:
                  - cloudformation:CreateStack
                  - cloudformation:UpdateStack
                  - cloudformation:DescribeStacks
                  - cloudformation:CreateChangeSet
                  - cloudformation:ExecuteChangeSet
                  - cloudformation:DeleteChangeSet
                  - iam:PassRole
                Resource: "*"
              - Effect: Allow
                Action:
                  - codestar-connections:UseConnection
                  - codestar-connections:GetConnection
                Resource: "*"

  ArtifactStoreBucket:
    Type: AWS::S3::Bucket

  MyPipeline:
    Type: AWS::CodePipeline::Pipeline
    Properties:
      RoleArn: !GetAtt CodePipelineRole.Arn
      ArtifactStore:
        Type: S3
        Location: !Ref ArtifactStoreBucket
      Stages:
        - Name: Source
          Actions:
            - Name: GitHubSource
              ActionTypeId:
                Category: Source
                Owner: AWS
                Provider: CodeStarSourceConnection
                Version: "1"
              Configuration:
                ConnectionArn: !Ref GitHubConnectionArn
                FullRepositoryId: !Sub "${GitHubOwner}/${GitHubRepo}"
                BranchName: !Ref GitHubBranch
                OutputArtifactFormat: CODE_ZIP
              OutputArtifacts:
                - Name: SourceOutput
              RunOrder: 1

        - Name: Deploy
          Actions:
            - Name: CFNDeploy
              ActionTypeId:
                Category: Deploy
                Owner: AWS
                Provider: CloudFormation
                Version: "1"
              InputArtifacts:
                - Name: SourceOutput
              Configuration:
                ActionMode: CREATE_UPDATE
                StackName: my-s3-bucket-stack
                TemplatePath: SourceOutput::s3-bucket.yaml
                Capabilities: CAPABILITY_NAMED_IAM
                RoleArn: !GetAtt CodePipelineRole.Arn
              RunOrder: 1

Outputs:
  PipelineName:
    Description: Name of the created CodePipeline
    Value: !Ref MyPipeline
```

✅ This CloudFormation file will:
- Create an S3 bucket for artifacts.
- Create a full CodePipeline.
- Hook into GitHub via the CodeStar Connection.
- Auto-deploy `s3-bucket.yaml` on pushes.

---

## 2. Deploy the CodePipeline Template

Use the AWS CLI to deploy this stack.

### 🔵 Bash (Linux/macOS)

```bash
aws cloudformation deploy \
  --template-file cfn-codepipeline.yaml \
  --stack-name my-codepipeline-stack \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
    GitHubConnectionArn=arn:aws:codestar-connections:us-east-1:123456789012:connection/abc12345-6789-0abc-def0-123456abcdef \
    GitHubOwner=your-github-username \
    GitHubRepo=your-repo-name \
    GitHubBranch=main
```

### 🟣 PowerShell (Windows)

```powershell
aws cloudformation deploy `
  --template-file cfn-codepipeline.yaml `
  --stack-name my-codepipeline-stack `
  --capabilities CAPABILITY_NAMED_IAM `
  --parameter-overrides `
    GitHubConnectionArn=arn:aws:codestar-connections:us-east-1:123456789012:connection/abc12345-6789-0abc-def0-123456abcdef `
    GitHubOwner=your-github-username `
    GitHubRepo=your-repo-name `
    GitHubBranch=main
```

✅ Replace:
- `your-github-username`
- `your-repo-name`
- Correct `ConnectionArn` copied from Part 3.

---

## 3. Confirm CodePipeline Creation

✅ After successful deployment:
- Go to AWS Console → **CodePipeline**.
- You should see a pipeline named something like `my-pipeline-stack`.
- Source stage should be GitHub.
- Deploy stage should be CloudFormation.

---

# 🧠 Notes

- The pipeline will automatically poll GitHub for webhook events.
- Every time you **git push** to your repo → it will automatically **trigger the pipeline**!

---

# 🧹 Troubleshooting

| Problem | Fix |
|---------|----|
| Role assumption errors | Ensure CodePipelineRole allows both `codepipeline.amazonaws.com` and `cloudformation.amazonaws.com` in `AssumeRolePolicyDocument` |
| Missing Connection | Ensure correct GitHub Connection ARN is passed |
| Pipeline stuck | Manually retry first execution if needed (console button) |

---

# ✅ Part 4 Complete

✅ You now have a **fully automated CodePipeline** driven from GitHub pushes, deploying your CloudFormation templates.

---

# 📣 Up Next:

📦 **Part 5: GitHub Push → Trigger AWS Auto-Deployment**

Where we push a change and watch the magic happen!

---



---

# 📖 AWS CloudFormation Lab Guide (v2)  
### Part 3: Create CodeStar Connection (GitHub → AWS)

---

## 📋 Overview

In this part, you will:

✅ Create a **CodeStar Connection** from GitHub to AWS.  
✅ Authorize AWS to pull from your GitHub repository securely.  
✅ Prepare for CodePipeline integration.

---

# 🛠️ Part 3: Setting Up GitHub Connection with CodeStar

---

## 1. Create a CodeStar Connection to GitHub

1. In AWS Console, search for **Developer Tools → Connections**.
2. Click **Create connection**.
3. Choose:
   - **Provider**: GitHub
   - **Connection name**: `github-connection` (or anything you like)
4. Click **Connect to GitHub**.
5. Approve AWS access to your GitHub account (login if prompted).
6. Complete connection creation.

✅ **Expected Result**:
- You should see your connection with **Status: Available**.

---
  
## 2. Copy the Connection ARN

After creation:

- Copy the **Connection ARN** shown.
  Example:

```
arn:aws:codestar-connections:us-east-1:123456789012:connection/abc12345-6789-0abc-def0-123456abcdef
```

✅ Save it for use in the next step.

---

## 🧠 Why We Use CodeStar Connections

- **Webhooks are auto-managed** (no manual webhook setup needed).
- **Secure OAuth handshake** (no need to store GitHub Personal Access Tokens).
- **AWS-recommended best practice** for GitHub integrations.

---

## 🧹 Troubleshooting

| Problem | Solution |
|---------|----------|
| Can't connect to GitHub | Ensure GitHub account permissions are granted to AWS |
| Connection status is Pending | Edit the connection and reauthorize GitHub |
| Connection missing | Make sure you're in the correct AWS Region (like `us-east-1`) |

---

# ✅ Part 3 Complete

✅ You now have a live GitHub ↔ AWS CodeStar Connection.

✅ Next, you will build your CodePipeline!

---

# 📣 Up Next:

📦 **Part 4: Build CodePipeline via CloudFormation Template**

---

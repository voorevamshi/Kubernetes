# AWS CLI Access Key Setup on Windows

This guide explains how to create **AWS Access Key ID** and **Secret Access Key** from the AWS Console and configure them on a **Windows machine** to connect using the AWS CLI or any Java/Spring Boot application.

---

## Prerequisites

* AWS Account
* IAM access to create users
* Windows OS
* AWS CLI installed

Check AWS CLI installation:

```bash
aws --version
```

---

## Step 1: Login to AWS Console

1. Open: [https://console.aws.amazon.com](https://console.aws.amazon.com)
2. Login using your AWS root account or IAM admin user.

---

## Step 2: Open IAM Service

1. Search for **IAM** in the AWS search bar
2. Open **Identity and Access Management (IAM)**

---

## Step 3: Create an IAM User

1. Go to **Users → Create user**
2. Enter a username (example: `windows-cli-user`)
3. Click **Next**

---

## Step 4: Attach Permissions

Choose one of the following:

### Option 1: Full Access (For Testing Only)

* Attach policy: `AdministratorAccess`

### Option 2: Limited Access (Recommended)

* Example policies:

  * `AmazonS3FullAccess`
  * `AmazonEC2ReadOnlyAccess`

Click **Next → Create User**

---

## Step 5: Create Access Key & Secret Key

1. Open the created user
2. Go to **Security Credentials** tab
3. Click **Create Access Key**
4. Select **Command Line Interface (CLI)**
5. Click **Next → Create Access Key**
6. Download the **.csv file**

You will receive:

```
AWS Access Key ID     : AKIA********XXXX
AWS Secret Access Key: ****************XXXX
```

> ⚠️ **Important:**
>
> * Secret key is shown only once
> * Do not share your keys

---

## Step 6: Configure AWS CLI on Windows

Open **Command Prompt / PowerShell** and run:

```bash
aws configure
```

Enter the values:

```text
AWS Access Key ID     : <your-access-key>
AWS Secret Access Key: <your-secret-key>
Default region name  : ap-south-1
Default output format: json
```

---

## Step 7: Verify Connection

Run the following command:

```bash
aws sts get-caller-identity
```

If successful, your AWS Account ID and User ARN will be displayed.

---

## Security Best Practices

* Never create access keys for the **root user**
* Rotate access keys every **90 days**
* Delete unused access keys
* Grant **least privilege permissions** only
* Store credentials securely

---

## Optional: Configure Using Environment Variables (Windows)

```powershell
setx AWS_ACCESS_KEY_ID "your-access-key"
setx AWS_SECRET_ACCESS_KEY "your-secret-key"
setx AWS_DEFAULT_REGION "ap-south-1"
```

Restart your terminal after setting these.

---

## Useful Commands

```bash
aws s3 ls
aws ec2 describe-instances
aws iam list-users
```

---

## Author

Created for Windows users to securely connect to AWS using AWS CLI and IAM access keys.

---

✅ AWS CLI Setup Complete

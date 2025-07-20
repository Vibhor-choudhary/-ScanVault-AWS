## 🛠️ Step-by-Step Setup: ScanVault Project – AWS-Based Receipt Automation Pipeline

This walkthrough guides you through building a serverless receipt processing system using AWS services, from setup to testing.

---

## 1️⃣ Set Up Amazon S3 Bucket (for receipt uploads)

### ✅ Instructions:

1. Go to the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home) → Click **Create Bucket**  
2. Choose your preferred AWS region (e.g., **ap-south-1**)  
3. Name your bucket (e.g., `scanvault-storage-yourname`)  
4. Click **Create bucket**  
5. Inside the bucket, create a folder named `scanvault-incoming/` for organized uploads

---

## 2️⃣ Configure DynamoDB Table (for storing extracted receipt data)

### ✅ Instructions:

1. Go to the [DynamoDB Console](https://console.aws.amazon.com/dynamodb/home) → Click **Create Table**  
2. Set **Table Name** to `ScanVault-Receipts-Table`  
3. Set **Partition Key**: `receipt_id` (Type: String)  
4. Set **Sort Key**: `date` (Type: String)  
5. Click **Create**

---

## 3️⃣ Set Up Amazon SES (for sending email alerts)

### ✅ Instructions:

1. Open the [Amazon SES Console](https://console.aws.amazon.com/ses/home)  
2. Under **Verified Identities**, verify your sender email address  
3. If your SES account is in sandbox mode, verify recipient email addresses as well  
4. Note the AWS region selected (e.g., **ap-south-1**) for Lambda configuration  

---

## 4️⃣ Create IAM Role for Lambda (manage permissions)

### ✅ Instructions:

1. Open the [IAM Console](https://console.aws.amazon.com/iam/home) → Click **Roles** → **Create Role**  
2. Select **AWS Service** → Choose **Lambda** as use case  
3. Attach these policies:  
   - AmazonS3ReadOnlyAccess  
   - AmazonTextractFullAccess  
   - AmazonDynamoDBFullAccess  
   - AmazonSESFullAccess  
   - AWSLambdaBasicExecutionRole  
4. Name the role: `ScanVault-lambdaRole`  
5. Create the role

---

## 5️⃣ Deploy the Lambda Function (core processing logic)

### ✅ Instructions:

1. Open the [AWS Lambda Console](https://console.aws.amazon.com/lambda/home) → Click **Create Function**  
2. Set function name: `processingLambda`  
3. Choose runtime (e.g., Python 3.9 or Node.js)  
4. Select **Use existing role** → Choose `ScanVault-lambdaRole`  
5. In **Configuration > Environment variables**, add necessary key-values (like S3 bucket name, DynamoDB table, SES sender)  
6. In **Code** section, upload or paste your Lambda function code  
7. Click **Deploy**  
8. Increase timeout to 2 minutes (default is 3 seconds) under **Configuration > General settings**

---

## 6️⃣ Configure S3 Event Trigger for Lambda

### ✅ Instructions:

1. Go to your S3 bucket → **Properties** tab  
2. Scroll to **Event Notifications** → Click **Create event notification**  
3. Set event name, e.g., `ScanVaultTrigger`  
4. Under **Prefix**, enter `scanvault-incoming/`  
5. Select **All object create events**  
6. Set destination as your Lambda function `processingLambda`  
7. Save the notification

---

## 7️⃣ Upload a Sample Receipt to Test the Pipeline

### ✅ Instructions:

1. Navigate to your S3 bucket → `scanvault-incoming/` folder  
2. Click **Upload** and select a receipt image or PDF file  
3. Confirm upload  
4. The Lambda function will automatically process the file, extract data with Textract, save to DynamoDB, and send a notification email via SES

---

## 8️⃣ (Optional) Verify Additional Email Addresses in SES

If your SES is in sandbox mode and you want multiple recipients:

1. Repeat Step 3 for each email to verify additional recipients  
2. Update Lambda function or notification settings to include these emails  

---

## ⌨️ Code Reference

Ensure your Lambda function code handles:

- S3 object creation event parsing  
- Calling Amazon Textract for OCR and data extraction  
- Writing extracted data into DynamoDB  
- Sending notification emails via SES  

(Refer to your repo or provided scripts for full code)

---

## 📨 Expected Result

Once a receipt is uploaded:

- You receive an email summary with key receipt data  
- Extracted data is stored in DynamoDB for easy querying  
- The system runs automatically with no manual intervention needed

---

✍️ **Author**: *Vibhor Choudhary*  
📬 *Connect on [LinkedIn](https://www.linkedin.com/in/vibhor-choudhary/)*  

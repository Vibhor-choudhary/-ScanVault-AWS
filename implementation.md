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

<img width="1447" height="789" alt="Screenshot 2025-07-20 at 8 59 39 PM" src="https://github.com/user-attachments/assets/981fd447-74c7-46f7-b9ab-8e71e7368904" />

<img width="1448" height="787" alt="Screenshot 2025-07-20 at 9 02 07 PM" src="https://github.com/user-attachments/assets/98f2ff28-10f8-4581-9b39-2276c3324c7f" />


---

## 2️⃣ Configure DynamoDB Table (for storing extracted receipt data)

### ✅ Instructions:

1. Go to the [DynamoDB Console](https://console.aws.amazon.com/dynamodb/home) → Click **Create Table**  
2. Set **Table Name** to `ScanVault-Receipts-Table`  
3. Set **Partition Key**: `receipt_id` (Type: String)  
4. Set **Sort Key**: `date` (Type: String)  
5. Click **Create**

<img width="598" height="311" alt="Screenshot 2025-07-20 at 11 11 04 PM" src="https://github.com/user-attachments/assets/9a719e78-5488-43d2-9dd1-15e6a9aacd03" />


<img width="1215" height="744" alt="Screenshot 2025-07-20 at 6 20 28 PM" src="https://github.com/user-attachments/assets/5939f3ed-86b0-470c-b056-8dbd5ca368b8" />

<img width="1432" height="782" alt="Screenshot 2025-07-20 at 9 15 31 PM" src="https://github.com/user-attachments/assets/53a26f4f-2af9-4ca7-962d-d3c81ae10656" />

---

## 3️⃣ Set Up Amazon SES (for sending email alerts)

### ✅ Instructions:

1. Open the [Amazon SES Console](https://console.aws.amazon.com/ses/home)  
2. Under **Verified Identities**, verify your sender email address  
3. If your SES account is in sandbox mode, verify recipient email addresses as well  
4. Note the AWS region selected (e.g., **ap-south-1**) for Lambda configuration  

<img width="1437" height="784" alt="Screenshot 2025-07-20 at 11 13 23 PM" src="https://github.com/user-attachments/assets/61a039ba-8014-48d1-afef-c29107fb8c56" />

![Verified Identities](https://github.com/user-attachments/assets/76ebaf42-a746-4990-90f4-dcc705a7b2c0)


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

<img width="1005" height="517" alt="Screenshot 2025-07-20 at 11 15 40 PM" src="https://github.com/user-attachments/assets/6d7c894c-b550-4e29-a1fe-8a96dcf0dde3" />

<img width="1434" height="780" alt="Screenshot 2025-07-20 at 9 17 45 PM" src="https://github.com/user-attachments/assets/02a3057e-ecc0-4260-a5b1-71d518aef117" />


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

<img width="796" height="426" alt="Screenshot 2025-07-20 at 11 19 00 PM" src="https://github.com/user-attachments/assets/9663eb6a-6179-4a75-b30e-c16a4610b192" />

<img width="1437" height="791" alt="Screenshot 2025-07-20 at 9 17 13 PM" src="https://github.com/user-attachments/assets/3f3d1966-c9ee-4a27-ac65-affa574e8c6d" />

![Lambda Env ](https://github.com/user-attachments/assets/c9a0a02e-5da4-44e8-a004-2b7f761725b2)


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

<img width="1430" height="786" alt="Screenshot 2025-07-20 at 11 21 24 PM" src="https://github.com/user-attachments/assets/64bfc42b-728d-48c4-bc48-db01f5c97e08" />

<img width="1435" height="719" alt="Screenshot 2025-07-20 at 11 22 32 PM" src="https://github.com/user-attachments/assets/02f700f3-4435-41c0-9521-e8403afeb9ce" />

---

## 7️⃣ Upload a Sample Receipt to Test the Pipeline

### ✅ Instructions:

1. Navigate to your S3 bucket → `scanvault-incoming/` folder  
2. Click **Upload** and select a receipt image or PDF file  
3. Confirm upload  
4. The Lambda function will automatically process the file, extract data with Textract, save to DynamoDB, and send a notification email via SES

<img width="1452" height="779" alt="Screenshot 2025-07-20 at 11 23 58 PM" src="https://github.com/user-attachments/assets/90eb57c6-e49a-4c0c-97e1-f48466ac0fcc" />

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

<img width="1437" height="791" alt="Screenshot 2025-07-20 at 9 17 13 PM" src="https://github.com/user-attachments/assets/22fe7734-5aea-4e61-a812-22257f4e386c" />

<img width="1435" height="784" alt="Screenshot 2025-07-20 at 9 14 39 PM" src="https://github.com/user-attachments/assets/782abd77-21cb-46e8-853a-91475b79399d" />

<img width="1450" height="784" alt="Screenshot 2025-07-20 at 9 15 10 PM" src="https://github.com/user-attachments/assets/9f9397d7-127a-45a0-a1b5-7baacd163dbc" />

---

## 📨 Expected Result

Once a receipt is uploaded:

- You receive an email summary with key receipt data  
- Extracted data is stored in DynamoDB for easy querying  
- The system runs automatically with no manual intervention needed

![Reciept mail](https://github.com/user-attachments/assets/fb265d60-59d6-4a11-b60a-ec05f58a4598)

---

✍️ **Author**: *Vibhor Choudhary*  
📬 *Connect on [LinkedIn](https://www.linkedin.com/in/vibhor-choudhary/)*  

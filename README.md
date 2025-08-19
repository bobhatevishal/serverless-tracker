---

# ** How I Build Serverless Expense Tracker**

---

## **Step 1: Set up AWS Account**

1. Sign up or log in to **AWS Management Console**.
2. Ensure your account has **IAM permissions** for:

   * Lambda
   * DynamoDB
   * API Gateway
   * CloudWatch (for logs)
3. Optional: Create a new **IAM user** with programmatic access for deployment.

---

## **Step 2: Create DynamoDB Table**

1. Go to **DynamoDB** → **Create Table**.
2. Table Name: `Expenses`.
3. Primary Key: `expenses_id` (String).
4. Keep default settings:

   * **Billing Mode:** Pay-per-request (on-demand)
   * **Encryption:** AWS managed
5. Click **Create Table**.

---

## **Step 3: Create AWS Lambda Function**

1. Go to **Lambda** → **Create Function**.
2. Select **Author from scratch**.
3. Function Name: `ExpenseTrackerLambda`.
4. Runtime: **Python 3.11** (or latest Python version available).
5. Execution Role: **Create new role with basic Lambda permissions**.
6. After creation, attach **additional IAM policy** for DynamoDB access:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:PutItem",
           "dynamodb:GetItem",
           "dynamodb:UpdateItem",
           "dynamodb:DeleteItem",
           "dynamodb:Scan",
           "dynamodb:Query"
         ],
         "Resource": "arn:aws:dynamodb:<REGION>:<ACCOUNT_ID>:table/Expenses"
       }
     ]
   }
   ```

---

## **Step 4: Add Lambda Code**

1. Open **Lambda function editor**.
2. Paste the **Python code** I shared in the README above. (function_code)
3. Save changes.
4. **Test Lambda manually**:

   * Use a sample event like:

     ```json
     {
       "httpMethod": "POST",
       "body": "{\"amount\": 500, \"description\": \"Lunch\", \"category\": \"Food\"}"
     }
     ```
   * Make sure it creates an expense in DynamoDB.

---

## **Step 5: Create API Gateway REST API**

1. Go to **API Gateway** → **Create API** → **REST API (New API)**.
2. Name: `ExpenseTrackerAPI`.
3. Create **resource**: `/expenses`.
4. Create **methods**:

   * **POST** → Lambda Integration → ExpenseTrackerLambda
   * **GET** → Lambda Integration → ExpenseTrackerLambda
   * **PUT** → Lambda Integration → ExpenseTrackerLambda
   * **DELETE** → Lambda Integration → ExpenseTrackerLambda
5. Enable **CORS** for the resource to allow requests from frontend.
6. Deploy API:

   * Stage name: `Expanse`
   * Note down the **Invoke URL**.

---

## **Step 6: Test API**

1. Use **Postman** or **curl** to test all endpoints:

   * **POST /expenses**

     ```json
     {
       "amount": 100,
       "description": "Groceries",
       "category": "Food"
     }
     ```
   * **GET /expenses?expenses\_id=<id>**
   * **PUT /expenses**

     ```json
     {
       "expenses_id": "<id>",
       "amount": 120,
       "description": "Groceries Updated",
       "category": "Food"
     }
     ```
   * **DELETE /expenses?expenses\_id=<id>**
2. Check **DynamoDB** to confirm the changes.

---

## **Step 7: (Optional) Add Frontend**

1. Create a **simple HTML + JS frontend** to call your API.
2. Form inputs:

   * Amount
   * Description
   * Category
   * Buttons for Create, Update, Delete
3. Use **fetch API** in JS to call your Lambda endpoints via API Gateway.

---

## **Step 8: Add Logging & Monitoring**

1. Go to **CloudWatch** → Log groups → Find your Lambda logs.
2. Monitor:

   * Errors
   * Execution time
   * API requests
3. Add **more detailed logs** in Lambda if needed for debugging:

   ```python
   print("Received event:", event)
   ```<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/fc000123-85f5-41db-87c0-73e14fad0de6" />



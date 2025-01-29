### AWS Lambda Challenge Assignment

**Objective:**
Students will gain hands-on experience creating, configuring, and testing an AWS Lambda function. This challenge will build foundational serverless computing skills essential for cloud-based development.

---

### Walkthrough Phase

**Step 1: Create a Lambda Function**
1. Navigate to the AWS Management Console.
2. Go to the **Lambda** dashboard.
3. Click **Create function**.
4. Select **Author from scratch**.
5. Configure the function:
   - **Function name**: `student-function-<your-username>`
   - **Runtime**: Python 3.9
   - **Role**: Choose "Create a new role with basic Lambda permissions".
6. Click **Create function**.

**Step 2: Upload Code to the Lambda Function**
1. Scroll down to the **Function code** section.
2. Copy and paste the following sample Python code into the inline code editor:

```python
import json

def lambda_handler(event, context):
    """Basic Lambda function that returns a greeting."""
    name = event.get('name', 'World')
    return {
        'statusCode': 200,
        'body': json.dumps(f"Hello, {name}!")
    }
```

3. Click **Deploy**.

**Step 3: Test the Lambda Function**
1. Click the **Test** button.
2. Configure a test event:
   - Event name: `test-event`
   - Template: **Hello World**
   - Modify the JSON to include a `name` key:
     ```json
     {
       "name": "Student"
     }
     ```
3. Click **Save**.
4. Run the test by clicking **Test** again and verify the output:
   - Status code: `200`
   - Body: `"Hello, Student!"`

**Step 4: Add Tags**
1. Go to the **Tags** section.
2. Add the following key-value pair:
   - Key: `Owner`
   - Value: Your username (e.g., `john_doe`).

**Step 5: View Metrics**
1. Navigate to the **Monitor** tab.
2. Explore the metrics such as **Invocations** and **Duration**.

---

### Challenge Phase

### **Objective**
Students will deploy an AWS **Lambda function** that processes a **CSV file** stored in **S3**, converts it into **LDIF format**, and re-uploads it to a specific path in the bucket.


### **Challenge Instructions**
1. **Create an S3 bucket** named `codeyou-student-devops-<username>`.
    - S3 doesn't allow `_` so you may need to change it to `-` instead.
    - Be sure to add a tag of `Owner=<username>`
2. **Write the sample CSV data** below into a file named `users.csv` and upload it to `input/users.csv` in the bucket.
3. **Deploy the Lambda function** provided below.
    - Be sure to add a tag of `Owner=<username>`
4. **Set the required environment variables** by referring to the `os.environ` lines in the code.
5. **Execute the Lambda function** and verify that the processed LDIF file appears in `output/converted_users.ldif`.

> **Note:** Use the IAM role **`DevOpsClassChallengeLambdaFunctionRole`** for your Lambda function permissions.  
> **Note:** Ensure that you are using Python 3.13 runtime

---

### **Sample CSV Data**
Save this content as `users.csv` before uploading to S3:
   ```csv
   uid,cn,sn,mail,uidNumber,gidNumber,homeDirectory,loginShell
   jdoe,John Doe,Doe,jdoe@example.com,1005,1000,/home/jdoe,/bin/bash
   asmith,Alice Smith,Smith,asmith@example.com,1006,1001,/home/asmith,/bin/bash
   ```

---

### **Lambda Function Code**
```python
import boto3
import csv
import os
from io import StringIO

s3_client = boto3.client("s3")

S3_BUCKET = os.environ.get("S3_BUCKET")
CSV_KEY = os.environ.get("CSV_FILE_KEY")
LDIF_KEY = os.environ.get("LDIF_FILE_KEY", "output/converted_users.ldif")

def convert_csv_to_ldif(csv_content):
    ldif_entries = []
    csv_reader = csv.DictReader(StringIO(csv_content))
    for row in csv_reader:
        ldif_entry = f"""dn: uid={row['uid']},ou=Users,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: top
cn: {row['cn']}
sn: {row['sn']}
mail: {row['mail']}
uid: {row['uid']}
uidNumber: {row['uidNumber']}
gidNumber: {row['gidNumber']}
homeDirectory: {row['homeDirectory']}
loginShell: {row['loginShell']}

"""
        ldif_entries.append(ldif_entry)
    return "\n".join(ldif_entries)

def lambda_handler(event, context):
    try:
        response = s3_client.get_object(Bucket=S3_BUCKET, Key=CSV_KEY)
        csv_content = response["Body"].read().decode("utf-8")
        ldif_content = convert_csv_to_ldif(csv_content)
        s3_client.put_object(
            Bucket=S3_BUCKET,
            Key=LDIF_KEY,
            Body=ldif_content.encode("utf-8"),
            ContentType="text/plain"
        )
        return {"statusCode": 200, "body": f"Successfully converted {CSV_KEY} to {LDIF_KEY}."}
    except Exception as e:
        return {"statusCode": 500, "body": f"Error: {str(e)}"}
```

---

### **Expected S3 Paths**
- **Input CSV file:** `s3://codeyou-student-devops-<username>/input/users.csv`
- **Output LDIF file:** `s3://codeyou-student-devops-<username>/output/converted_users.ldif`


Good luck!


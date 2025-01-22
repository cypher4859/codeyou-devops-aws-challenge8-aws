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

**Scenario:**
Build a Lambda function that processes a JSON input payload and performs basic arithmetic operations.

#### Task Instructions:
1. **Create a Lambda Function**
   - Use Python 3.9 as the runtime.
   - Name your function `student-math-<your-username>`.
   - Use the existing role from the walkthrough.

2. **Write the Lambda Code**
   - You can find sample code below
   - The function should:
     - Accept a JSON payload with two numbers (`num1` and `num2`) and an operation (`add`, `subtract`, `multiply`, `divide`).
     - Perform the specified operation and return the result.
     - Handle invalid operations or missing keys gracefully.

   - Example input:
     ```json
     {
       "num1": 10,
       "num2": 5,
       "operation": "add"
     }
     ```
   - Example output:
     ```json
     {
       "result": 15
     }
     ```

3. **Test Your Function**
   - Create multiple test cases with different operations and input values.
   - Verify that the function handles edge cases such as division by zero or invalid operations.

4. **Tag the Function**
   - Add the `Owner` tag with your username.

5. **Monitor Metrics**
   - Explore the metrics after multiple test executions.

**Bonus Challenge:**
- Configure an Amazon API Gateway to trigger your Lambda function.
- Test the Lambda function using an HTTP request.

**Submission:**
- Provide a text file with:
  - The function name and ARN.
  - Screenshots of successful test executions.
  - (Bonus) A screenshot of the API Gateway integration.

---

### Sample Python Code for Challenge

```python
import json

def lambda_handler(event, context):
    """Lambda function to perform basic arithmetic operations."""
    try:
        num1 = event['num1']
        num2 = event['num2']
        operation = event['operation']

        if operation == 'add':
            result = num1 + num2
        elif operation == 'subtract':
            result = num1 - num2
        elif operation == 'multiply':
            result = num1 * num2
        elif operation == 'divide':
            if num2 == 0:
                return {
                    'statusCode': 400,
                    'body': json.dumps('Error: Division by zero is not allowed')
                }
            result = num1 / num2
        else:
            return {
                'statusCode': 400,
                'body': json.dumps(f"Error: Invalid operation '{operation}'")
            }

        return {
            'statusCode': 200,
            'body': json.dumps({'result': result})
        }
    except KeyError as e:
        return {
            'statusCode': 400,
            'body': json.dumps(f"Error: Missing key {str(e)}")
        }
```


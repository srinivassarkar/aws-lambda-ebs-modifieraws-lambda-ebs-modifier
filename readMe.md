# Project 3: AWS Lambda & CloudWatch Integration with EBS Volume Modification

This project demonstrates creating an AWS Lambda function that listens for EBS volume notifications from CloudWatch. Upon receiving a trigger event, it modifies the specified EBS volume from `gp2` to `gp3`. This project covers Lambda creation, CloudWatch rule configuration, and IAM permissions setup.

---

## Overview
- Create a Lambda function
- Set up CloudWatch rule to monitor EBS volume events
- Modify an EBS volume from `gp2` to `gp3` upon trigger
- Watch and debug logs in CloudWatch

---

## 1. Creating the Lambda Function

- **Create Function**:
  - Name: `EBSVolumeModifier`
  - Runtime: Python 3.x
  - Handler: `lambda_function.lambda_handler`

- **Code**:
  - Initial `lambda_function.py` code:
    ```python
    import json

    def lambda_function(event, context):
        print(event)
        return {
            'statusCode': 200,
            'body': json.dumps('Hello from Lambda!')
        }
    ```

- **Deploy the Function**:
  - Deploy the function after creating it.

- **Event Source**:
  - Event Name: `EBS Volume Notification`

---

## 2. Configuring CloudWatch Rule

- **Service Name**: Amazon Elastic Block Store (EBS)
- **Rule Name**: `EBSVolumeChangeRule`
- **Rule Description**: "Trigger on any EBS volume state change and notify Lambda function."

- **Configure EBS Volume Notification**:
  - ARN: Any volume ARN (e.g., `arn:aws:ec2:region:account-id:volume/vol-0123456789abcdef0`)
  - **Add Targets**:
    - Select Lambda function created earlier: `EBSVolumeModifier`

- **Step: Configure Rule Details**:
  - **Description**: "This rule monitors EBS volume state changes and triggers Lambda for modification."

---

## 3. Modify EBS Volume from `gp2` to `gp3`

- **Updated Lambda Code (`lambda_function.py`)**:
  ```python
  import boto3

  def get_volume_id_from_arn(volume_arn):
      # Split the ARN using the colon (':') separator
      arn_parts = volume_arn.split(':')
      volume_id = arn_parts[-1].split('/')[-1]
      return volume_id

  def lambda_function(event, context):
      volume_arn = event["resources"][0]
      volume_id = get_volume_id_from_arn(volume_arn)
      
      ec2_client = boto3.client('ec2')
      
      response = ec2_client.modify_volume(
          VolumeId=volume_id,
          VolumeType='gp3',
      )

---

## 4. Grant IAM Permissions

### Create an IAM Role for Lambda
1. **Attach required policies** or add an **inline policy** to allow the following actions:
   - EC2 permissions to modify EBS volumes (`ModifyVolume`).




## 5. Watch Logs and Validate

1. **Deploy the Lambda Function**:
   - After creating and configuring the Lambda function, deploy it to activate it.

2. **Trigger the CloudWatch Rule**:
   - Create or modify an EBS volume to trigger the CloudWatch rule and invoke the Lambda function.

3. **Watch Logs**:
   - Go to CloudWatch logs to monitor the triggers and Lambda execution.
   - Check if the function is being invoked properly and logs are being generated.

4. **Validate JSON Input and Output**:
   - Review the JSON input and output in the logs to ensure the Lambda function is processing the event correctly and modifying the EBS volume as expected.

---
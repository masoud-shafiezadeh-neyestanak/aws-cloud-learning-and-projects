# IAM Permissions and Security Documentation

## 1. Overview

This document describes the AWS Identity and Access Management (IAM) configuration used in the Serverless Image Processing project.

AWS Lambda uses a dedicated IAM execution role to access Amazon S3 and Amazon CloudWatch Logs.

The permissions are designed according to the Principle of Least Privilege.

## 2. Lambda Execution Role

**Role Name:** `image-resizer-function-role-wyyezp51`

**Role ARN:**

`arn:aws:iam::635520162853:role/service-role/image-resizer-function-role-wyyezp51`

This execution role allows the Lambda function to access the AWS resources required for image processing and logging.

## 3. Attached and Inline Policies

The execution role contains two policies:

| Policy | Type | Purpose |
|---|---|---|
| AWSLambdaBasicExecutionRole | Customer managed | CloudWatch Logs permissions |
| ImageResizerS3Access | Customer inline | S3 object read and write permissions |

## 4. CloudWatch Logs Permissions

**Policy:** `AWSLambdaBasicExecutionRole-5080d4dc-d51e-4488-afb4-77e9898031d4`

This policy grants the Lambda function permission to create log groups, create log streams, and publish log events.

| IAM Action | Purpose |
|---|---|
| `logs:CreateLogGroup` | Creates a CloudWatch log group |
| `logs:CreateLogStream` | Creates a log stream |
| `logs:PutLogEvents` | Writes execution logs |

The resource configuration limits log stream access to the log group associated with `image-resizer-function`.

## 5. Amazon S3 Permissions

**Policy:** `ImageResizerS3Access`

This inline policy defines the Lambda function's permissions for accessing objects in the S3 bucket.

### 5.1 Read Original Images

```json
{
  "Sid": "ReadOriginalImages",
  "Effect": "Allow",
  "Action": ["s3:GetObject"],
  "Resource": "arn:aws:s3:::masoud-serverless-image-project-2026/originals/*"
}
```

This permission allows Lambda to retrieve original images from the `originals/` prefix.

### 5.2 Write Processed Images

```json
{
  "Sid": "WriteThumbnails",
  "Effect": "Allow",
  "Action": ["s3:PutObject"],
  "Resource": "arn:aws:s3:::masoud-serverless-image-project-2026/thumbnails/*"
}
```

This permission allows Lambda to upload processed thumbnails into the `thumbnails/` prefix.

## 6. Principle of Least Privilege

The IAM configuration follows the Principle of Least Privilege by granting only the permissions required by the function.

Key security decisions include:

- Lambda can read objects only from `originals/`.
- Lambda can write objects only to `thumbnails/`.
- Lambda does not have `s3:DeleteObject` permission.
- Lambda does not have unrestricted S3 access.
- CloudWatch logging permissions are scoped to the function's log group for log stream operations.
- No long-term AWS credentials are embedded in the application code.

## 7. Lambda Invocation Permission

In addition to its execution role, the Lambda function has a resource-based policy that allows Amazon S3 to invoke it.

The policy specifies:

- Principal: `s3.amazonaws.com`
- Action: `lambda:InvokeFunction`
- Source account: The project's AWS account
- Source ARN: The designated S3 bucket

This allows the configured S3 bucket to invoke the Lambda function when matching object creation events occur.

## 8. Security Architecture Summary

| Security Component | Implementation |
|---|---|
| Lambda execution identity | Dedicated IAM execution role |
| S3 read access | `s3:GetObject` on `originals/*` |
| S3 write access | `s3:PutObject` on `thumbnails/*` |
| Logging access | CloudWatch Logs permissions |
| Lambda invocation | S3 resource-based permission |
| Public access | S3 Block Public Access enabled |
| Encryption | Default SSE-S3 encryption |

## 9. Conclusion

The project uses IAM execution roles and resource-based permissions to control access between AWS services.

By separating S3 read and write permissions and limiting access to specific object prefixes, the implementation demonstrates practical application of AWS IAM security principles and least-privilege access control.

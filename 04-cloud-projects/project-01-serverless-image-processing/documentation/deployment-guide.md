# Deployment Guide – Serverless Image Processing

## 1. Overview

This document describes the deployment process for the Serverless Image Processing project using Amazon S3, AWS Lambda, IAM, and the Python Pillow library.

The deployment was performed using the AWS Management Console and a local Windows development environment.

## 2. Prerequisites

Before deployment, the following were required:

- An AWS account.
- Access to Amazon S3, AWS Lambda, IAM, and CloudWatch.
- Python 3.12.
- A Windows development environment.
- The Pillow Python library.
- Basic understanding of S3 buckets, Lambda functions, and IAM permissions.

## 3. Create the Amazon S3 Bucket

1. Open the Amazon S3 console.
2. Create a general-purpose bucket.
3. Use the following bucket name:

   `masoud-serverless-image-project-2026`

4. Select the `us-east-1` AWS Region.
5. Keep Block Public Access enabled.
6. Use default server-side encryption (SSE-S3).
7. Create two prefixes inside the bucket:

   - `originals/`
   - `thumbnails/`

The `originals/` prefix stores uploaded images, while `thumbnails/` stores processed images.

## 4. Create the AWS Lambda Function

1. Open the AWS Lambda console.
2. Create a new function from scratch.
3. Configure the function with the following settings:

| Setting | Value |
|---|---|
| Function name | `image-resizer-function` |
| Runtime | Python 3.12 |
| Architecture | x86_64 |
| AWS Region | us-east-1 |

4. Assign a dedicated execution role to the function.

The execution role is responsible for granting the permissions required to access S3 and CloudWatch Logs.

## 5. Prepare the Pillow Deployment Package

The Pillow library is required for image processing.

Because the Lambda function uses a Linux execution environment while the development machine uses Windows, Pillow dependencies must be packaged for the compatible Lambda environment.

### 5.1 Create a Python Virtual Environment

A local project directory was created:

`C:\Users\lenovo\serverless-image-processing`

Python 3.12 was used for the deployment package.

### 5.2 Install Pillow for the Lambda Environment

The following command was used to download compatible Linux dependencies:

```cmd
python -m pip install --platform manylinux2014_x86_64 --target lambda-package --implementation cp --python-version 3.12 --abi cp312 --only-binary=:all: Pillow==12.2.0
```

This installs Pillow and its dependencies into the `lambda-package` directory.

### 5.3 Package the Lambda Function

The deployment package contains:

- `lambda_function.py`
- Pillow library files
- Required Pillow dependencies

A Python ZIP-building script was used to package these files into `function.zip`.

The resulting ZIP package was approximately 8 MB.

## 6. Upload the Deployment Package

1. Open the Lambda function.
2. Navigate to the Code section.
3. Choose the option to upload a `.zip` file.
4. Select `function.zip`.
5. Upload the package.

Configure the Lambda handler:

`lambda_function.lambda_handler`

This identifies the Python file and function that AWS Lambda executes.

## 7. Configure IAM Permissions

The Lambda execution role requires permissions for:

- Reading original images from S3.
- Writing processed thumbnails to S3.
- Creating and publishing CloudWatch Logs.

The S3 permissions are restricted to the following prefixes:

- `originals/*` – `s3:GetObject`
- `thumbnails/*` – `s3:PutObject`

See [IAM Permissions Documentation](iam-permissions.md) for the detailed policy configuration.

## 8. Configure the S3 Event Notification

The S3 bucket was configured to automatically invoke Lambda when an original image is uploaded.

Configuration:

| Setting | Value |
|---|---|
| Event name | `process-original-images` |
| Event type | `s3:ObjectCreated:*` |
| Prefix | `originals/` |
| Suffix | Not configured |
| Destination | `image-resizer-function` |

The Lambda resource-based policy allows the designated S3 bucket to invoke the function.

## 9. Test the Deployment

The deployment was tested by uploading original images to the `originals/` prefix.

The following workflow was verified:

1. Upload an image to S3.
2. S3 generates an object creation event.
3. The event invokes the Lambda function.
4. Lambda retrieves the original image.
5. Pillow resizes and optimizes the image.
6. Lambda uploads the resulting thumbnail to `thumbnails/`.
7. CloudWatch Logs records execution information.

The end-to-end workflow completed successfully.

## 10. Troubleshooting

### Unsupported Image Format: None

**Issue:** The Lambda function initially returned:

`ValueError: Unsupported image format: None`

**Cause:** The image format was being read after applying the EXIF orientation transformation.

**Resolution:** The original image format was captured before calling `ImageOps.exif_transpose()`.

### Lambda Packaging Compatibility

**Issue:** Pillow requires compatible Linux binary dependencies in the Lambda environment.

**Resolution:** Pillow was installed using the `manylinux2014_x86_64` platform option for Python 3.12 and packaged into the deployment ZIP.

## 11. Deployment Considerations

- Keep S3 Block Public Access enabled.
- Use least-privilege IAM permissions.
- Keep original and processed images in separate prefixes.
- Ensure the Lambda architecture and Python runtime match the packaged dependencies.
- Monitor Lambda execution and CloudWatch Logs.
- Review AWS costs and remove unused resources after testing.

## 12. Future Improvements

Potential enhancements include:

- Infrastructure as Code using AWS CloudFormation or Terraform.
- Automated deployment pipelines.
- Amazon DynamoDB integration for image metadata.
- Improved error handling and monitoring.
- Support for additional image formats.

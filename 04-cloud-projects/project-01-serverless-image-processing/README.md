# Project 01 — Serverless Image Processing System

## 1. Project Overview

This project demonstrates an event-driven, serverless image processing workflow built using Amazon Web Services (AWS).

When a user uploads an original image to an Amazon S3 bucket, an S3 event notification automatically invokes an AWS Lambda function. The function uses Python and the Pillow library to resize and optimize the image, then stores the processed image in a separate S3 prefix.

The project demonstrates practical cloud architecture concepts, including serverless computing, event-driven processing, IAM security, automated workflows, and cloud monitoring.

## 2. Business Objective

The objective is to automate image resizing without requiring manual processing or dedicated image-processing servers.

Key objectives:

- Automate image processing after upload.
- Reduce image dimensions and file size.
- Use managed AWS services instead of maintaining servers.
- Apply least-privilege access controls.
- Build a scalable and cost-conscious serverless workflow.

## 3. Architecture

### Workflow

1. A user uploads an image to the `originals/` prefix in Amazon S3.
2. Amazon S3 detects the object creation event.
3. S3 sends an event notification to AWS Lambda.
4. Lambda retrieves the original image from S3.
5. Python and Pillow process and resize the image.
6. Lambda uploads the processed image to the `thumbnails/` prefix.
7. Amazon CloudWatch captures Lambda execution logs.

### Architecture Flow

`User → Amazon S3 → S3 Event Notification → AWS Lambda → Pillow → Amazon S3`

**Architecture documentation:** [View Architecture](architecture/architecture.md)

## 4. AWS Services and Technologies

| Service / Technology | Purpose |
|---|---|
| Amazon S3 | Stores original and processed images |
| AWS Lambda | Executes image processing automatically |
| AWS IAM | Controls Lambda execution permissions |
| Amazon CloudWatch | Provides execution logs and monitoring |
| Python 3.12 | Implements the processing logic |
| Pillow | Resizes and processes images |

## 5. Key Implementation Details

- Lambda runtime: Python 3.12
- Lambda architecture: x86_64
- Maximum thumbnail dimensions: 300 × 300 pixels
- Supported image formats: JPEG and PNG
- Image orientation correction using EXIF metadata
- JPEG output optimization with quality set to 85
- Separate S3 prefixes for original and processed images
- S3 event filtering using the `originals/` prefix
- Image processing performed automatically after upload

The function processes only objects under `originals/`, helping prevent recursive processing of generated thumbnails.

## 6. Security Considerations

The project follows the principle of least privilege.

The Lambda execution role has restricted S3 permissions:

- `s3:GetObject` for objects under `originals/`
- `s3:PutObject` for objects under `thumbnails/`

Additional security configurations:

- S3 Block Public Access enabled.
- Default server-side encryption enabled (SSE-S3).
- Lambda invocation permission restricted to the project S3 bucket and AWS account.
- CloudWatch logging permissions provided through the Lambda execution role.

**Security documentation:** [IAM Permissions](documentation/01-iam-permissions.md)

## 7. Testing and Validation

The implementation was successfully validated through an end-to-end test.

Test results:

- S3 event notification successfully triggered Lambda.
- Lambda retrieved the original image.
- Pillow image processing completed successfully.
- The processed image was stored in the `thumbnails/` prefix.
- CloudWatch logs were used to inspect execution behavior.

### Example Test Result

| Metric | Original | Thumbnail |
|---|---:|---:|
| File name | beach.jpg | beach.jpg |
| File size | 85.8 KB | 14.3 KB |
| Creation time | 13:36:30 | 13:36:34 |

**Result:** Approximately 83.3% reduction in file size.

The thumbnail was generated approximately four seconds after the original image upload.

### Screenshot Evidence

[View S3 Original and Thumbnail Images](screenshots/01-s3-originals-and-thumbnails.png)

Additional testing details and troubleshooting notes are available in:

[Testing and Troubleshooting](documentation/03-testing-and-troubleshooting.md)

## 8. Troubleshooting and Lessons Learned

During initial testing, the Lambda function returned the following error:

`ValueError: Unsupported image format: None`

The issue occurred because the image format was accessed after applying `ImageOps.exif_transpose()`.

The implementation was corrected by retrieving the original image format before applying the EXIF orientation transformation.

This troubleshooting exercise reinforced the importance of:

- Understanding image metadata behavior.
- Validating application dependencies.
- Inspecting AWS Lambda execution logs.
- Testing event-driven workflows end to end.

## 9. Deployment Documentation

The project was deployed using the AWS Management Console, with the Lambda deployment package prepared locally on Windows.

Deployment instructions, configuration details, and required steps are documented here:

[Deployment Guide](documentation/02-deployment-guide.md)

## 10. Project Scope and Future Enhancements

### Current Implementation

- Amazon S3
- AWS Lambda
- AWS IAM
- Amazon CloudWatch
- Python 3.12
- Pillow image processing
- S3 event notifications

### Planned Enhancements

- Store image metadata in Amazon DynamoDB.
- Introduce structured error handling.
- Improve monitoring and logging.
- Explore API Gateway integration.
- Expand automated testing.
- Improve the architecture with additional AWS services where appropriate.

These enhancements are not part of the current implementation.

## 11. Project Structure

```text
project-01-serverless-image-processing/
│
├── README.md
│
├── architecture/
│   └── architecture.md
│
├── lambda/
│   └── lambda_function.py
│
├── documentation/
│   ├── 01-iam-permissions.md
│   ├── 02-deployment-guide.md
│   └── 03-testing-and-troubleshooting.md
│
└── screenshots/
    ├── README.md
    └── 01-s3-originals-and-thumbnails.png
```

## 12. Project Status

**Version:** 1.0

**Status:** Successfully implemented and end-to-end tested.

**Project Type:** Hands-on AWS Serverless Cloud Project

---

*This project is part of an ongoing AWS Cloud Learning and Projects portfolio.*

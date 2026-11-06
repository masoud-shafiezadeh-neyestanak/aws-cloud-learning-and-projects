# Project 01 — Serverless Image Processing System

## 1. Project Overview

This project demonstrates a serverless image processing workflow built on Amazon Web Services (AWS).

Users upload original images to an Amazon S3 bucket. An S3 event notification automatically invokes an AWS Lambda function, which processes the uploaded image using Python and Pillow.

The resized image is then stored in a separate S3 prefix.

The project demonstrates practical cloud computing concepts, event-driven architecture, serverless computing, IAM permissions, and automated file processing.

## 2. Business Objective

The objective is to automate image resizing without requiring users to manually process images or maintain dedicated image-processing servers.

Key objectives:

- Automate image processing after upload.
- Reduce image dimensions and file size.
- Use managed AWS services instead of maintaining servers.
- Apply least-privilege access controls.
- Build a cost-conscious and scalable serverless workflow.

## 3. Architecture

**Workflow:**

1. A user uploads an image to the `originals/` prefix in Amazon S3.
2. Amazon S3 detects the object creation event.
3. S3 sends an event notification to AWS Lambda.
4. Lambda retrieves the original image from S3.
5. Python and Pillow process and resize the image.
6. Lambda uploads the processed image to the `thumbnails/` prefix.

**Architecture flow:**

`User → Amazon S3 → S3 Event Notification → AWS Lambda → Pillow → Amazon S3`

See the `architecture/` directory for architecture documentation.

## 4. AWS Services and Technologies

| Service / Technology | Purpose |
|---|---|
| Amazon S3 | Stores original and processed images |
| AWS Lambda | Executes image processing automatically |
| AWS IAM | Controls Lambda permissions |
| Amazon CloudWatch | Provides execution logs and monitoring |
| Python 3.12 | Implements the processing logic |
| Pillow | Resizes and processes images |

## 5. Key Implementation Details

- Runtime: Python 3.12
- Architecture: x86_64
- Maximum thumbnail dimensions: 300 × 300 pixels
- Supported image formats: JPEG and PNG
- Image orientation correction using EXIF metadata
- JPEG output optimization with quality set to 85
- Separate S3 prefixes for original and processed images
- S3 event filtering using the `originals/` prefix

## 6. Security Considerations

The project follows the principle of least privilege.

The Lambda execution role has restricted S3 permissions:

- `s3:GetObject` for objects under `originals/`
- `s3:PutObject` for objects under `thumbnails/`

Additional security configurations:

- S3 Block Public Access enabled
- S3 default server-side encryption enabled (SSE-S3)
- Lambda invocation permission restricted to the project S3 bucket and AWS account

## 7. Testing and Validation

The implementation was validated through an end-to-end test.

Test results:

- Lambda function executed successfully.
- S3 event notification successfully triggered Lambda.
- Pillow image processing completed successfully.
- Processed images were successfully stored in the `thumbnails/` prefix.

**Example result:**

| Metric | Original | Thumbnail |
|---|---:|---:|
| File size | ~80 KB | ~14 KB |

The example demonstrated approximately 82.5% file-size reduction.

## 8. Troubleshooting and Lessons Learned

During initial testing, the Lambda function returned:

`ValueError: Unsupported image format: None`

The issue occurred because the image format was accessed after applying `ImageOps.exif_transpose()`.

The implementation was corrected by retrieving the original image format before applying the EXIF orientation transformation.

This troubleshooting exercise reinforced the importance of validating image metadata and inspecting CloudWatch execution logs.

## 9. Project Scope and Future Enhancements

**Current implementation:**
- Amazon S3
- AWS Lambda
- IAM
- Pillow-based image processing

**Planned enhancements:**
- Store image metadata in Amazon DynamoDB.
- Add structured error handling.
- Improve monitoring and logging.
- Explore API Gateway integration.
- Add architecture diagrams and deployment documentation.

## 10. Project Status

**Version:** 1.0

**Status:** Initial end-to-end implementation successfully tested.

**Project Type:** Hands-on AWS Serverless Cloud Project

---

*This project is part of an ongoing AWS Cloud Learning and Projects portfolio.*

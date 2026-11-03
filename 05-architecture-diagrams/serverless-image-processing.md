# Serverless Image Processing Architecture

## Architecture Overview

This diagram represents a serverless image processing workflow using AWS services.

## Workflow

1. User uploads an image.
2. Amazon S3 stores the original image.
3. S3 Event Notification triggers AWS Lambda.
4. Lambda processes and resizes the image.
5. The processed image is stored in Amazon S3.
6. Image metadata is stored in Amazon DynamoDB.

## AWS Services

- Amazon S3
- AWS Lambda
- Amazon DynamoDB

## Architecture Flow

```text
User
  |
  v
Amazon S3 (Original Image)
  |
  v
S3 Event Notification
  |
  v
AWS Lambda (Image Processing)
  |
  +------> Amazon S3 (Resized Image)
  |
  +------> Amazon DynamoDB (Metadata)
```

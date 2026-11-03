# Serverless Architecture

## Overview

Serverless is a cloud computing model in which the cloud provider manages the underlying server infrastructure, allowing developers to focus on building and running applications without managing servers directly.

## Key Concepts

- Serverless is an architectural and operational model.
- Applications can use multiple managed cloud services.
- Services can scale automatically based on demand.
- Many serverless services follow a pay-per-use pricing model.
- Event-driven architecture is commonly used in serverless applications.

## Key AWS Services

| Service | Purpose |
|---|---|
| AWS Lambda | Executes code without managing servers |
| Amazon S3 | Stores files and objects |
| Amazon DynamoDB | Stores NoSQL data |
| Amazon API Gateway | Manages API requests |

## Example Use Case

An image processing application can use serverless architecture:

1. A user uploads an image to Amazon S3.
2. An S3 event triggers AWS Lambda.
3. Lambda reads and resizes the image.
4. The resized image is stored in S3.
5. Image metadata and user information are saved in DynamoDB.

## Key Takeaway

Serverless does not mean there are no servers. It means the cloud provider manages the underlying infrastructure.

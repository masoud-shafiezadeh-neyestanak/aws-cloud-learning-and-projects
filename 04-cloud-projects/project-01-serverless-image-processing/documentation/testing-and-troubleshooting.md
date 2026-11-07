# Testing and Troubleshooting – Serverless Image Processing

## 1. Overview

This document describes the testing process, observed results, and troubleshooting activities performed during the implementation of the Serverless Image Processing project.

The objective was to verify that Amazon S3, AWS Lambda, and the Pillow library work together correctly to process uploaded images.

## 2. Testing Environment

| Component | Configuration |
|---|---|
| AWS Region | us-east-1 |
| S3 Bucket | masoud-serverless-image-project-2026 |
| Lambda Function | image-resizer-function |
| Runtime | Python 3.12 |
| Architecture | x86_64 |
| Image Processing Library | Pillow 12.2.0 |
| Input Prefix | originals/ |
| Output Prefix | thumbnails/ |

## 3. Test Cases

### Test 1: Lambda Handler Validation

**Objective:** Verify that the Lambda function can be invoked successfully.

**Test Method:**

A basic test event containing an empty Records array was submitted:

```json
{
  "Records": []
}
```

**Expected Result:**

The Lambda function should execute without errors.

**Observed Result:**

The test completed successfully.

**Status:** Passed

Note: This test validated basic handler execution, not the complete image-processing workflow.

### Test 2: S3 Event Notification

**Objective:** Verify that uploading an image triggers the Lambda function.

**Test Method:**

An image was uploaded to the `originals/` prefix of the S3 bucket.

**Expected Result:**

Amazon S3 should generate an ObjectCreated event and invoke the configured Lambda function.

**Observed Result:**

The Lambda function was successfully invoked following image uploads.

**Status:** Passed after troubleshooting

### Test 3: Image Processing

**Objective:** Verify that Lambda can retrieve, resize, and optimize an uploaded image.

**Test Method:**

Original images were uploaded to S3 and processed using Pillow.

**Expected Result:**

The function should generate resized images and upload them to the `thumbnails/` prefix.

**Observed Result:**

The first processing attempt encountered an image format detection error. After correcting the code, image processing completed successfully.

**Status:** Passed after troubleshooting

### Test 4: Thumbnail Storage

**Objective:** Verify that processed images are stored in the correct S3 prefix.

**Expected Result:**

Generated thumbnails should appear under `thumbnails/`.

**Observed Result:**

The processed thumbnail was successfully created and stored in the expected location.

**Status:** Passed

### Test 5: Event Loop Prevention

**Objective:** Ensure that processing an output image does not trigger another Lambda execution.

**Implementation:**

- S3 Event Notification filters uploads using the `originals/` prefix.
- Lambda also checks the object key and skips objects outside `originals/`.
- Processed images are saved under `thumbnails/`.

**Expected Result:**

Thumbnail uploads should not initiate another image-processing cycle.

**Status:** Implemented and verified through the prefix configuration and function logic.

## 4. Troubleshooting

### Issue 1: Pillow Deployment Package Compatibility

**Problem:**

The Lambda function runs in a Linux environment, while the development machine uses Windows.

Installing Pillow directly for Windows would not provide the Linux-compatible binary dependencies required by Lambda.

**Cause:**

The deployment package needs dependencies compatible with the Lambda runtime and architecture.

**Resolution:**

Pillow was installed using the following command:

```cmd
python -m pip install --platform manylinux2014_x86_64 --target lambda-package --implementation cp --python-version 3.12 --abi cp312 --only-binary=:all: Pillow==12.2.0
```

The Lambda function and Pillow dependencies were then packaged into a ZIP file.

### Issue 2: Unsupported Image Format: None

**Problem:**

CloudWatch Logs reported:

```text
ValueError: Unsupported image format: None
```

**Cause:**

The image format was being accessed after applying `ImageOps.exif_transpose()`.

The transformation could return an image object without retaining its original format attribute.

**Resolution:**

The original image format was captured before applying the EXIF transformation.

The corrected implementation stores the format in a separate variable:

```python
image_format = image.format
image = ImageOps.exif_transpose(image)
```

The function was then repackaged and uploaded to Lambda.

**Result:**

Image processing completed successfully.

### Issue 3: Verifying the Updated Lambda Deployment

**Problem:**

After updating the Lambda deployment package, the updated function needed to be tested again.

**Resolution:**

A new object was copied into the `originals/` prefix under a different name to generate another S3 ObjectCreated event.

**Result:**

Lambda processed the new object and successfully generated a thumbnail.

## 5. Test Results

The end-to-end workflow was successfully tested.

An example image produced the following result:

| Measurement | Approximate Size |
|---|---:|
| Original image | 80 KB |
| Processed thumbnail | 14 KB |
| Approximate size reduction | 82.5% |

The result demonstrates that the image-processing workflow can reduce file size while generating a smaller image.

Actual results depend on the original image dimensions, format, and content.

## 6. Final Verification

The following capabilities were verified during project testing:

- Lambda handler execution.
- S3 event-driven Lambda invocation.
- Retrieval of original images from S3.
- Image resizing using Pillow.
- Storage of processed images in S3.
- CloudWatch logging.
- Separation of original and processed image prefixes.

## 7. Limitations and Future Testing

The current project is a functional learning implementation. Additional testing would be valuable before production use.

Potential improvements include:

- Testing larger image files.
- Testing unsupported image formats.
- Testing corrupted image files.
- Adding structured exception handling.
- Testing duplicate uploads and filename collisions.
- Configuring CloudWatch alarms.
- Evaluating Lambda timeout and memory settings.
- Testing retry behavior and duplicate event delivery.

## 8. Conclusion

The project successfully demonstrates an event-driven serverless image-processing workflow using Amazon S3, AWS Lambda, IAM, CloudWatch Logs, and Pillow.

The troubleshooting process also provided practical experience with Python dependency packaging, Lambda deployment compatibility, CloudWatch log analysis, and debugging serverless applications.

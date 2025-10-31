# Cloud Sync Backend: Technical Overview

## Purpose
The Cloud Sync backend is a Python FastAPI service that manages Shimmer sensor data uploads, decoding, calibration, and metadata storage. It integrates with AWS S3 for file storage and DynamoDB for metadata and summary metrics.

## Architecture
- **main.py**: FastAPI app, REST endpoints for file upload, download, metadata, and device-patient mapping.
- **shimmerCalibrate.py**: Decodes and calibrates Shimmer binary data, ported from MATLAB, with robust handling for sensor channels and time alignment.
- **AWS S3**: Stores raw and decoded sensor files.
- **AWS DynamoDB**: Stores file metadata, summary metrics, and device-patient mappings.

## Key Technical Details

### main.py
- Defines REST endpoints for:
  - File upload/download (with S3 integration)
  - Grouping files by day, device, or patient
  - Generating presigned URLs for secure file access
  - Device-patient mapping CRUD operations
- Uses boto3 for AWS S3 and DynamoDB access
- Handles ZIP creation for batch downloads
- Uses Pydantic models for request/response validation
- Loads environment variables for configuration

### shimmerCalibrate.py
- Provides exact MATLAB port for decoding Shimmer3 binary files
- Handles:
  - 24-bit signed integer parsing (custom functions)
  - Inertial sensor calibration (offset, gain, alignment matrix)
  - Time calibration and rollover correction
  - Output as JSON or MATLAB file
- All array math is implemented in pure Python

### Example Workflow
1. User uploads a Shimmer .txt file via REST API
2. File is stored in S3
3. Decoding endpoint parses and calibrates data, stores full JSON in S3 and summary in DynamoDB
4. Metadata endpoints allow searching, grouping, and downloading files

### Notable Endpoints
- `/upload/` (POST): Upload sensor file
- `/files/` (GET): List all files
- `/files/metadata/` (GET): Grouped metadata
- `/download/{filename}` (GET): Download file
- `/file/decode/` (GET): Decode file
- `/ddb/device-patient-map` (GET/PUT/DELETE): Device-patient mapping

---
For more, see the code in `Cloud Sync/main.py` and `Cloud Sync/shimmerCalibrate.py`.

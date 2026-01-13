# Getting Started

This guide provides setup instructions for the three main components of the COSDA Project: the Android docking app, the cloud API backend, and the web interface dashboard.

## Android Docking App

The Android app automates docking, data transfer, and cloud sync for Shimmer sensors.

**Repository:** [cosda-umass/shimmer-docking-android](https://github.com/cosda-umass/shimmer-docking-android)

**What it is:** A Java-based Android application that handles Bluetooth communication with Shimmer devices, transfers sensor data, and syncs to cloud storage.

**How to run:**
1. Clone the repository: `git clone https://github.com/cosda-umass/shimmer-docking-android`
2. Open the project in Android Studio
3. Build the `app` module
4. Ensure runtime permissions are granted (Bluetooth, location, notifications for Android 13+)
5. Run on an Android device (minimum SDK 31)

See the repository README for detailed protocol documentation and UI guides.

## Cloud API Backend

The backend provides serverless data processing and storage APIs.

**Repository:** [cosda-umass/shimmer-cloud-api](https://github.com/cosda-umass/shimmer-cloud-api)

**What it is:** A Python FastAPI application deployed on AWS Lambda that decodes Shimmer sensor data, applies calibration, and manages storage in S3 and DynamoDB.

**How to run locally:**
1. Install dependencies: `pip install fastapi uvicorn boto3 python-dotenv mangum pydantic`
2. Configure environment variables in `.env`:
   ```
   S3_BUCKET=your-bucket-name
   DDB_TABLE=your-device-patient-db
   DDB_FILE_TABLE=your-file-db
   AWS_REGION=your-region
   ```
3. Run: `uvicorn main:app --reload`

**For AWS deployment:** Package dependencies and deploy to Lambda with API Gateway.

See the repository README for API endpoints and data processing details.

## Web Interface Dashboard

The web app for data visualization and management.

**Repository:** [cosda-umass/shimmer-web-ui](https://github.com/cosda-umass/shimmer-web-ui)

**What it is:** An Angular-based web application with AG Grid for data tables, Chart.js for visualizations, and AWS Amplify for authentication and deployment.

**How to run:**
1. Clone the repository: `git clone https://github.com/cosda-umass/shimmer-web-ui`
2. Install dependencies: `npm install`
3. Start development server: `ng serve`
4. Open http://localhost:4200 in your browser

**Prerequisites:** Node.js v18+, npm v9+, Angular CLI.

See the repository README for component details and deployment instructions.



## Shimmer Sensor and Firmware
<!-- TODO: Zhaolong -->
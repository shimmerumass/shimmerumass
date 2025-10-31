# Shimmer_Server_UI (Web Dashboard): Technical Overview

## Purpose
The Shimmer_Server_UI is an Angular-based web dashboard for managing, visualizing, and downloading Shimmer sensor data. It provides researchers and administrators with real-time access to device, patient, and file metadata, as well as interactive data visualization tools.

## Architecture
- **Angular 20+**: Modern SPA framework
- **AG Grid**: For tabular display of device, patient, and file metadata
- **Chart.js**: For time-series visualization of sensor data
- **AWS Amplify**: For authentication, deployment, and S3 integration
- **API Service**: Handles all backend communication

## Key Technical Details

### api.service.ts
- Central service for all HTTP requests to the backend (Cloud Sync API)
- Methods for:
  - Listing files and metadata
  - Downloading files (direct and batch)
  - Device-patient mapping CRUD
  - Fetching decoded sensor data fields for visualization
- Uses Angular's HttpClient and RxJS Observables

### data-grid.ts
- Implements AG Grid for displaying metadata
- Defines column groups for Shimmer 1 and Shimmer 2 (recorded time, day, accel var, MAC, etc.)
- Integrates with API service to fetch and display data
- Provides buttons for graph visualization (opens Chart.js modal)
- Handles downsampling for large datasets

### Component Structure
- **comp/**: Reusable UI components (data grid, header, auth, file viewer, etc.)
- **pages/**: Main dashboard, data ops, user ops
- **services/**: API integration
- **assets/**: Static images and resources
- **environments/**: Angular environment configs

### Example Workflow
1. User logs in via AWS Amplify Auth
2. Dashboard loads metadata from backend via API service
3. User can view, filter, and sort files/devices/patients
4. Clicking graph buttons fetches decoded data and opens interactive charts
5. Files can be downloaded individually or in batch (ZIP)

---
For more, see the code in `Shimmer_Server_UI/src/app/services/api.service.ts` and `Shimmer_Server_UI/src/app/comp/data-grid/data-grid.ts`.

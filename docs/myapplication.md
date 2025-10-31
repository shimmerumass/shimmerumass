# Mobile App: Technical Overview

## Purpose
MyApplication is an Android app that automates the nightly docking, data transfer, and cloud sync for Shimmer sensor devices. It is designed for robust, unattended operation in research and deployment settings, ensuring reliable data collection and upload to the cloud.

## Architecture
- **DockingManager.java**: Orchestrates the docking protocol, scanning, monitoring, and transitions between states. Handles device discovery, retry logic, and session management.
- **ShimmerFileTransferClient.java**: Manages Bluetooth RFCOMM connections and the custom binary file transfer protocol. Handles chunked transfer, ACK/NACK, and file header stamping with device/system timestamps.
- **SyncService.java**: Android Service that manages background file synchronization with the cloud (S3). Handles notifications, progress updates, and error handling.
- **Other Services/Receivers**: Includes ScanningService, TransferService, DockingService, and broadcast receivers for alarms and system events.
- **Local Storage**: Uses SQLite database and app-private directories to track and cache files before upload.

## Key Technical Details

### DockingManager.java
- Manages the full docking protocol:
  - Scans for up to two paired Shimmer devices
  - Monitors docking state and triggers file transfer
  - Handles retry logic, silent backoff, and session state
  - Maintains per-device timestamps and session logs
- Uses Android Bluetooth APIs and background threads

### ShimmerFileTransferClient.java
- Handles all Bluetooth RFCOMM communication
- Implements chunked file transfer with ACK/NACK and error recovery
- Stamps file headers with both Shimmer RTC64 and Android RTC32 timestamps
- Integrates with Firebase Analytics and Crashlytics for monitoring
- Provides methods for listing files, transferring, and marking as synced

### SyncService.java
- Runs as a foreground service with notification
- Finds unsynced files, checks against S3/cloud, and uploads missing files
- Updates notifications with progress and completion status
- Uses ShimmerFileTransferClient for file operations

### Example Workflow
1. App scans for paired Shimmer devices and monitors docking state
2. When docked, initiates file transfer using chunked protocol
3. Files are cached locally and tracked in SQLite
4. When network is available, SyncService uploads files to S3 and marks them as synced
5. All operations are logged and errors reported via Crashlytics

---
For more, see the code in `MyApplication/app/src/main/java/com/example/myapplication/`.

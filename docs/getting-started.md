# Getting Started

## Hardware Components Overview

The platform is designed to work with any data-collecting sensor device.  
In our deployment, we use **Shimmer sensors** as an example, but the system can be adapted to other wearable or IoT sensors that collect physiological or environmental data.

---

## Software Components Overview

Our platform consists of several core software components, each with specific roles and technical responsibilities.

---

### Cloud Backend
- Provides a central API for uploading, downloading, and managing sensor data and metadata.  
- Receives files from the mobile app, processes and calibrates the data, and stores both raw and processed results.  
- Manages secure access to file storage and metadata for other components.  
- Groups and organizes files by device, user, and time for easy retrieval and analysis.  
- Serves as the main integration point for the web dashboard and mobile app.

---

### Mobile App
- Collects data from wearable sensors and manages local storage of files.  
- Detects when sensors are ready to transfer data and initiates file transfer.  
- Ensures reliable transfer of files to the backend, with error handling and retries.  
- Tracks which files have been uploaded and manages uploads when a network is available.  
- Tags each file with device and time information for traceability.

---

### Web Dashboard
- Provides a user interface for browsing, searching, and visualizing sensor data.  
- Allows users to download files, manage devices, and map devices to users or patients.  
- Supports secure login and access control for different user roles.  
- Enables bulk operations and administrative management of the system.

---

### Database
- Stores metadata about files, devices, users, and summary statistics for fast lookup and queries.  
- Supports grouping, searching, and validation of data sync status.  
- Keeps only summary and reference data, with large files stored separately.

---

### File Storage
- Stores all raw and processed sensor data files.  
- Supports scalable upload and download of large datasets.  
- Provides secure, time-limited access to files for authorized users and components.  
- References to files are stored in the database for quick lookup.

---

## Workflow Summary

1. The **mobile app** collects data from Shimmer sensors, transfers files, and uploads them to the **cloud backend**.  
2. The **backend** decodes, calibrates, and stores data in **Amazon S3** and **DynamoDB**.  
3. The **web dashboard** provides researchers with tools to browse, visualize, and download data, as well as manage devices and users.  
4. **DynamoDB** and **S3** work together to provide fast metadata queries and scalable file storage.

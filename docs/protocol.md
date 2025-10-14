# Communication Protocol

This document describes the communication protocols used by the UMass Shimmer sensor platform for device control, data transmission, and system integration.

## Protocol Overview

The Shimmer platform implements a layered protocol stack supporting multiple communication methods:

- **Bluetooth Low Energy (BLE)** for wireless sensor communication
- **USB/Serial** for high-speed docking station interface
- **HTTP/WebSocket** for web-based applications
- **TCP/UDP** for network-based integrations

## Bluetooth Protocol

### Connection Establishment

#### Device Discovery
```
SCAN_REQUEST → SCAN_RESPONSE
├─ Device Name: "Shimmer3-XXXX"  
├─ MAC Address: 00:06:66:XX:XX:XX
├─ Signal Strength: RSSI value
└─ Available Services: UUID list
```

#### Pairing Process
```
1. CONNECT_REQUEST(mac_address)
2. AUTHENTICATION_CHALLENGE
3. AUTHENTICATION_RESPONSE(pin/key)
4. CONNECTION_ESTABLISHED
5. SERVICE_DISCOVERY
```

### Data Transmission Protocol

#### Command Structure
All commands follow this format:
```
[START][LENGTH][COMMAND][PAYLOAD][CHECKSUM][END]
```

**Field Descriptions:**
- `START`: 0x24 ($ symbol)
- `LENGTH`: Payload length (1 byte)
- `COMMAND`: Command identifier (1 byte)  
- `PAYLOAD`: Variable length data
- `CHECKSUM`: XOR checksum (1 byte)
- `END`: 0x0A (newline)

#### Core Commands

**Device Information**
```
GET_DEVICE_INFO    = 0x01
├─ Response: Device ID, firmware version, capabilities
GET_SAMPLING_RATE  = 0x02  
├─ Response: Current sampling rate in Hz
SET_SAMPLING_RATE  = 0x03
├─ Payload: New sampling rate (2 bytes)
```

**Sensor Configuration**
```
GET_SENSORS        = 0x04
├─ Response: Bitmask of enabled sensors
SET_SENSORS        = 0x05
├─ Payload: Sensor enable bitmask (2 bytes)
GET_CONFIG         = 0x06
├─ Response: Complete configuration dump
SET_CONFIG         = 0x07
├─ Payload: Configuration parameters
```

**Data Streaming**
```
START_STREAMING    = 0x08
├─ Begins real-time data transmission
STOP_STREAMING     = 0x09
├─ Ends data transmission
GET_DATA_PACKET    = 0x0A
├─ Response: Single data packet
```

### Data Packet Format

#### Real-time Streaming
```
Packet Structure:
[TIMESTAMP][SENSOR_DATA][BATTERY][STATUS]

TIMESTAMP: 4 bytes (milliseconds since start)
SENSOR_DATA: Variable length based on enabled sensors
BATTERY: 1 byte (percentage)
STATUS: 1 byte (device status flags)
```

#### Sensor Data Layout
```
Accelerometer: 6 bytes (X, Y, Z as signed 16-bit)
Gyroscope:     6 bytes (X, Y, Z as signed 16-bit)  
Magnetometer:  6 bytes (X, Y, Z as signed 16-bit)
Temperature:   2 bytes (signed 16-bit, 0.1°C resolution)
Pressure:      3 bytes (unsigned 24-bit, Pa)
```

## USB/Serial Protocol

### Docking Station Interface

#### High-Speed Data Transfer
```
BULK_DOWNLOAD      = 0x10
├─ Initiates bulk data download from sensor memory
├─ Response: Data stream with progress indicators

DATA_STREAM Format:
[BLOCK_ID][BLOCK_SIZE][DATA][CRC32]
```

#### Firmware Updates
```
ENTER_BOOTLOADER   = 0x20
├─ Switches device to firmware update mode
WRITE_FLASH        = 0x21  
├─ Payload: Flash address + data block
VERIFY_FLASH       = 0x22
├─ Payload: Address range for verification
EXIT_BOOTLOADER    = 0x23
├─ Returns to normal operation mode
```

## HTTP/WebSocket API

### RESTful API Endpoints

#### Device Management
```http
GET    /api/devices              # List all devices
GET    /api/devices/{id}         # Get device details  
POST   /api/devices/{id}/connect # Connect to device
DELETE /api/devices/{id}/connect # Disconnect device
```

#### Configuration
```http
GET    /api/devices/{id}/config      # Get configuration
PUT    /api/devices/{id}/config      # Update configuration
POST   /api/devices/{id}/calibrate   # Start calibration
GET    /api/devices/{id}/status      # Get device status
```

#### Data Access
```http
GET    /api/devices/{id}/data/live   # Real-time data stream
GET    /api/devices/{id}/data/batch  # Historical data
POST   /api/devices/{id}/data/export # Export data formats
```

### WebSocket Protocol

#### Real-time Data Streaming
```javascript
// Connection establishment
ws = new WebSocket('ws://host/api/stream/{device_id}')

// Message format
{
  "type": "data",
  "timestamp": 1635789123456,
  "device_id": "shimmer_001", 
  "sensors": {
    "accelerometer": {"x": 0.234, "y": -0.567, "z": 9.801},
    "gyroscope": {"x": 0.012, "y": -0.034, "z": 0.056},
    "temperature": 23.5
  },
  "battery": 85,
  "quality": "good"
}
```

#### Command Interface
```javascript
// Send command
ws.send(JSON.stringify({
  "command": "set_sampling_rate",
  "parameters": {"rate": 100}
}))

// Response format
{
  "status": "success",
  "command": "set_sampling_rate", 
  "result": {"previous_rate": 50, "new_rate": 100}
}
```

## Error Handling

### Error Codes
```
0x00  SUCCESS           - Operation completed successfully
0x01  INVALID_COMMAND   - Unknown or malformed command
0x02  INVALID_PARAMETER - Parameter out of range
0x03  DEVICE_BUSY       - Device currently unavailable  
0x04  MEMORY_ERROR      - Insufficient memory
0x05  COMMUNICATION_ERROR - Transmission failure
0x06  SENSOR_ERROR      - Hardware sensor malfunction
0x07  BATTERY_LOW       - Insufficient battery level
0x08  TIMEOUT           - Operation timed out
```

### Retry Logic
```
1. Exponential backoff: 100ms, 200ms, 400ms, 800ms
2. Maximum retry attempts: 3
3. Connection recovery: Auto-reconnect on disconnect
4. Data integrity: CRC validation and retransmission
```

## Security Protocols

### Authentication
```
1. Device pairing with PIN/passkey
2. AES-128 encryption for sensitive data
3. Session tokens for API access
4. Certificate-based validation for firmware updates
```

### Data Protection
```
- TLS 1.3 for HTTPS/WSS connections
- Data anonymization options  
- Configurable data retention policies
- Audit logging for access tracking
```

## Integration Examples

### Python SDK Usage
```python
from shimmer_api import ShimmerDevice

# Connect and configure
device = ShimmerDevice("00:06:66:XX:XX:XX")
device.connect()
device.set_sampling_rate(100)  # 100 Hz
device.enable_sensors(['accel', 'gyro'])

# Stream data
device.start_streaming()
for packet in device.stream():
    print(f"Accel: {packet.accelerometer}")
    print(f"Gyro: {packet.gyroscope}")
    
device.stop_streaming()
```



## Performance Specifications

### Throughput
- **Bluetooth**: Up to 1 Mbps effective data rate
- **USB**: Up to 12 Mbps for bulk transfers
- **Network**: Limited by connection bandwidth

### Latency  
- **Bluetooth streaming**: <50ms typical
- **USB commands**: <10ms response time
- **Network API**: <100ms over LAN

### Reliability
- **Packet loss**: <0.1% under normal conditions
- **Error detection**: CRC32 for all data transfers  
- **Recovery time**: <500ms for connection restoration
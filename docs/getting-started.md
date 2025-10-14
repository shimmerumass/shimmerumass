# Getting Started

This guide will help you set up and start using the UMass Shimmer sensor platform.

## Prerequisites

Before you begin, ensure you have:

- A Shimmer3 sensor device
- USB cable for initial setup
- Computer with Bluetooth capability
- Compatible operating system (Windows, macOS, or Linux)

## Installation

### 1. Hardware Setup

1. **Unpack your Shimmer sensor**
   - Handle the device carefully
   - Ensure the battery is charged or connect to power

2. **Connect via USB**
   - Use the provided USB cable
   - Connect the sensor to your computer
   - The device should be recognized automatically

### 2. Software Installation

#### Option A: Using Python (Recommended)

```bash
# Install required packages
pip install shimmer-python-api
pip install bluetooth-utils
```

#### Option B: Using the Shimmer Application

1. Download the official Shimmer application from the [Shimmer website](https://www.shimmersensing.com)
2. Follow the installation wizard
3. Launch the application and follow setup prompts

### 3. First Connection

1. **Enable Bluetooth** on your computer
2. **Power on** your Shimmer sensor
3. **Pair the device** through your system's Bluetooth settings
4. **Test the connection** using the Shimmer application or API

## Basic Usage

### Connecting to a Sensor

```python
from shimmer_api import ShimmerDevice

# Create connection
sensor = ShimmerDevice()
sensor.connect("00:06:66:XX:XX:XX")  # Replace with your device MAC

# Start streaming
sensor.start_streaming()

# Read data
data = sensor.read_data()
print(data)

# Stop streaming
sensor.stop_streaming()
sensor.disconnect()
```

### Configuration

Basic sensor configuration options:

- **Sampling Rate**: Set data collection frequency
- **Sensor Selection**: Choose which sensors to activate
- **Data Format**: Configure output data structure
- **Power Management**: Optimize battery usage

## Next Steps

- Review the [Architecture](architecture.md) to understand system components
- Learn about [Protocol](protocol.md) specifications for advanced integration
- Explore example projects and use cases

## Troubleshooting

### Common Issues

**Bluetooth Connection Problems**
```
- Ensure Bluetooth is enabled
- Check device pairing status  
- Verify correct MAC address
- Try restarting the Bluetooth service
```

**Data Not Streaming**
```
- Confirm sensor is powered on
- Check connection status
- Verify sensor configuration
- Review firewall/security settings
```

**Battery Issues**
```
- Charge device for at least 2 hours
- Check charging cable connection
- Monitor battery indicator
- Consider battery replacement if old
```

For additional support, check our [GitHub Issues](https://github.com/shimmerumass/shimmerumass-webpage/issues) or contact the development team.
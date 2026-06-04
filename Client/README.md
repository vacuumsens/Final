# Senseeker Client Module

## Overview

The Client module is the edge device component of the Senseeker system. It runs on a Raspberry Pi and handles data collection from sensors, manages the heater module through PWM control, and communicates with the Server for data transmission and configuration synchronization.

## Hardware Requirements

- **Raspberry Pi** (with i2c capability)
- **Battery system** (4 leads to battery balancer)
- **Heater module** (PWM-controlled)
- **Sensors** (i2c connected)
- **Power management**: VSYS for system voltage and battery power

## Setup Instructions

Just install custom imagae vsens-client.img.xz using standard Raspi Imager found online.  For more information if you want to modify files and scripts, continue below. 

### 1. System Preparation

Ensure your Raspberry Pi has i2c enabled:

```bash
sudo raspi-config
```

Navigate to **Interface Options** → **I2C** and enable it.

### 2. Installation

Extract the `Codebase.zip` file:

```bash
unzip Client/Codebase.zip
cd client-directory
```

### 3. Python Environment Setup

Create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
```

Install required dependencies:

```bash
pip install -r requirements.txt
```

### 4. Automatic Startup Configuration

The Client module is configured for automatic startup. To enable it:

```bash
# Make the startup script executable
chmod +x startup.sh

# Add to crontab for automatic startup
crontab -e
```

Add the following line (adjust path as needed):

```bash
@reboot /path/to/startup.sh
```

**Note**: If you need to run the application manually, you may need to kill old processes first:

```bash
pkill -f "Post-sender.py"
```

## Running the Application

### Main Application File

The primary entry point is `Post-sender.py`:

```bash
python Post-sender.py
```

### Manual Execution

To run the client manually within the Python environment:

```bash
source venv/bin/activate
python Post-sender.py
```

## Functionalities

### Core Features

- **Sensor Data Collection**: Reads data from i2c-connected sensors
- **Heater Control**: Manages PWM-based heater module for temperature regulation
  - PWM voltage is flexible (strict voltage not required)
  - For higher power requirements, consider adding a step-up voltage regulator
- **Server Communication**: Transmits collected data to the Server module
- **Configuration Synchronization**: Receives updates from the Server
- **Autonomous Operation**: Runs automatically on system startup

### Configuration

The Client can be configured through the Server's web interface. Settings include:

- Sensor sampling intervals
- Heater operation parameters
- Server communication frequency
- Data collection preferences

## Troubleshooting

### i2c Connection Issues

**Problem**: Sensors not detected on i2c bus

**Solution**:
1. Verify i2c is enabled via `raspi-config`
2. Check sensor connections
3. Test i2c communication:
   ```bash
   i2cdetect -y 1
   ```

### Process Management

**Problem**: Application fails to start or old processes interfere

**Solution**:
```bash
# Kill existing processes
pkill -f "Post-sender.py"

# Start fresh
python Post-sender.py
```

### Power/Heater Issues

**Problem**: Heater underpowered or not responding

**Solution**:
- Check battery connections (4 leads to balancer)
- Verify VSYS voltage is adequate
- Consider adding a step-up voltage regulator if higher voltage is needed
- Ensure PWM pin configuration is correct

### Port Connection Issues

**Problem**: Unable to connect to Server

**Solution**:
1. Ensure Server is running
2. Verify network connectivity
3. Check firewall/port settings
4. Review application logs for connection details

## File Structure

```
Client/
├── Codebase.zip          # Main source code
├── vsens-client.img.xz   # Pre-configured system image (optional)
└── README.md             # This file
```

## Deployment Options

### Option 1: Fresh Installation
Extract the `Codebase.zip`, set up the Python environment, and run `Post-sender.py`.

### Option 2: Pre-configured Image
Use the provided `vsens-client.img.xz` image for a quick system setup:

```bash
# Extract the image
xz -d vsens-client.img.xz

# Flash to SD card (replace sdX with your SD card device)
sudo dd if=vsens-client.img of=/dev/sdX bs=4M conv=fsync
```

## Performance Considerations

- **Sensor Reading**: i2c communication is handled efficiently
- **Data Transmission**: Scheduled transmission to minimize bandwidth usage
- **Power Management**: Optimized for continuous operation on battery power
- **Heater Control**: PWM allows flexible power management

## Support & Documentation

For additional information:
- Check `PossibleChangesNotes.txt` for known issues and recommendations
- Review the Server module's README for integration details
- Consult hardware component datasheets for specific configurations

## License

Part of the Senseeker Deliverables project.

---

**Last Updated**: June 2026

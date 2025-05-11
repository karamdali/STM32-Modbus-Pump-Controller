# STM32 Modbus RTU Water Pump Controller

A Modbus RTU slave device using STM32F103 (Blue Pill) to control a 12V water pump and collect sensor data (current, flow rate, vibration). Designed to connect to an IoT gateway (ESP32) for MQTT communication with an online server.

## Features

- **Pump Control**: ON/OFF control via Modbus commands
- **Sensor Monitoring**:
  - Current consumption measurement (ACS712)
  - Water flow measurement (YF-S401 flow meter)
  - Vibration monitoring (MPU6050 accelerometer)
- **Modbus RTU Interface**:
  - Coil for pump control
  - Input registers for sensor data
- **RS485 Communication**: Using MAX485 module
- **Debug Mode**: Optional serial debugging output

## Hardware Requirements

- STM32F103C8T6 (Blue Pill) board
- MAX485 RS485 transceiver module
- MPU6050 accelerometer
- YF-S401 water flow sensor
- ACS712 current sensor (30A version)
- 12V water pump
- Appropriate power supplies and protection circuits

## Pin Configuration

| Function            | STM32 Pin |
|---------------------|-----------|
| RS485 Control       | PA3       |
| Pump Control        | PB3       |
| Current Sensor      | PB1       |
| Flow Meter          | PA4       |
| MPU6050 Interrupt   | PA0       |
| I2C SDA            | PB7       |
| I2C SCL            | PB6       |
| Debug Serial TX     | PB10      |
| Debug Serial RX     | PB11      |

## Modbus Register Map

### Coils (Read/Write)

| Address | Description         | Values      |
|---------|---------------------|-------------|
| 0       | Pump Control        | 0 = OFF, 1 = ON |

### Input Registers (Read Only)

| Addresses | Description       | Data Type | Units |
|-----------|-------------------|-----------|-------|
| 0-1       | X-axis skewness   | float     | -     |
| 2-3       | Y-axis skewness   | float     | -     |
| 4-5       | Z-axis skewness   | float     | -     |
| 6-7       | Current           | float     | Amps  |
| 8-9       | Flow Rate         | float     | mL/s  |
| 10-11     | Total Flow        | float     | mL    |

## Software Implementation

### Key Functions

1. **Modbus RTU Communication**:
   - Uses `ModbusRTUSlave` library
   - RS485 control via PA3 pin
   - Device address: 1
   - Baud rate: 9600

2. **Current Measurement**:
   - ACS712 sensor with voltage divider
   - 10-sample averaging for stability

3. **Flow Measurement**:
   - YF-S401 flow meter with interrupt-based pulse counting
   - Calibration factor: 550 pulses per liter
   - Calculates both instantaneous flow rate and total volume

4. **Vibration Monitoring**:
   - MPU6050 accelerometer with interrupt-driven data collection
   - 128-sample window for skewness calculation
   - Normalized output values

### Configuration

1. **MPU6050 Setup**:
   - Accelerometer range: ±2g
   - Digital Low Pass Filter: 184Hz cutoff
   - Sample rate: 1kHz
   - Data-ready interrupt enabled

2. **Debug Mode**:
   - Enabled by `#define DEBUG 1`
   - Uses HardwareSerial on PB10/PB11
   - Outputs sensor data and status information

## Usage
   - Connect all hardware components as per pin configuration
   - Power the system (3.3V for STM32, 12V for pump)
   - Connect RS485 interface to Modbus master (e.g., ESP32 gateway)
   - Send Modbus commands to control pump and read sensor data

## License
This code is provided AS IS without warranty. Feel free to use and modify it for any purpose.

## Author
Karam Dali
Damascus - October 2024

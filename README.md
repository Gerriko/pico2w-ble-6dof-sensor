# Pico W 6DOF BLE Sensor

This project implements a 6 Degrees of Freedom (6DOF) sensor application on a Raspberry Pi Pico W, exposing sensor data (accelerometer, magnetometer, orientation, and on-board temperature) via Bluetooth Low Energy (BLE).

The primary sensor used is the LSM303DLM, which provides 3-axis acceleration and 3-axis magnetic field data. This data is processed to calculate pitch, roll, and heading (both raw and tilt-compensated).

## Features

*   Reads 3-axis accelerometer and 3-axis magnetometer data from an LSM303DLM sensor.
*   Calculates orientation:
    *   Pitch
    *   Roll
    *   Heading (uncompensated/raw)
    *   Heading (tilt-compensated)
*   Applies a moving average filter to sensor readings for smoother output.
*   Reads the Pico W's on-board temperature sensor.
*   Exposes sensor data via BLE GATT services:
    *   Device Name
    *   Temperature (Environmental Sensing Service)
    *   Custom service for Orientation data (Pitch, Roll, Headings) and Sample Rate.
*   Allows configuration of the sensor data sample rate via BLE.
*   Provides notifications for sensor data updates.
*   Toggles the on-board LED as a heartbeat indicator.
*   Uses I2C for communication with the LSM303DLM sensor.

## Hardware Requirements

*   Raspberry Pi Pico W
*   LSM303DLM sensor module (or compatible)
*   Connecting wires

## Software & Libraries

*   Pico SDK
*   BTstack library for Bluetooth Low Energy functionality
*   CYW43 wireless driver

## BLE GATT Profile

The BLE services and characteristics are defined in `6dof_sensor.gatt`. Key characteristics include:

*   **GAP Service:**
    *   Device Name: `picow_sense` (Read)
*   **Environmental Sensing Service (`ORG_BLUETOOTH_SERVICE_ENVIRONMENTAL_SENSING`):**
    *   Temperature (`ORG_BLUETOOTH_CHARACTERISTIC_TEMPERATURE`): Read, Notify, Indicate, Dynamic
*   **Custom Orientation Service (UUID: `19B10010-E8F2-537E-4F6C-D104768A1214`):**
    *   Sample Rate (UUID: `19B10012-...`): Read, Write Without Response, Dynamic (uint16_t, Hz)
    *   Orientation Pitch (UUID: `19B10013-...`): Read, Notify, Dynamic (float32, degrees)
    *   Orientation Roll (UUID: `19B10014-...`): Read, Notify, Dynamic (float32, degrees)
    *   Heading Raw (UUID: `19B10015-...`): Read, Notify, Dynamic (float32, degrees)
    *   Heading Compensated (UUID: `19B10016-...`): Read, Notify, Dynamic (float32, degrees)

User descriptions and format descriptors are provided for clarity.

## Setup & Building

1.  **Clone the repository (or set up your project).**
2.  **Ensure Pico SDK and BTstack are correctly set up** in your development environment.
3.  **Connect the LSM303DLM sensor:**
    *   SDA to Pico GPIO16 (I2C0 SDA)
    *   SCL to Pico GPIO17 (I2C0 SCL)
    *   VCC to 3.3V
    *   GND to GND
4.  **Build the project:**
    The `6dof_sensor.gatt` file is automatically converted to `6dof_sensor.h` during the compilation process (typically found in `build/generated/...`).
    Follow the standard Pico SDK build procedure (e.g., using `cmake` and `make`).
    ```bash
    mkdir build
    cd build
    cmake ..
    make
    ```
5.  **Flash the `.uf2` file** to your Pico W.

## Usage

1.  Power on the Pico W. The on-board LED should start toggling.
2.  Use a BLE scanning application (e.g., nRF Connect, LightBlue) on your phone or computer.
3.  Scan for BLE devices and connect to "picow_sense".
4.  Once connected, you can:
    *   Read the current temperature, pitch, roll, and heading values.
    *   Subscribe to notifications for these characteristics to receive updates automatically.
    *   Read the current sample rate.
    *   Write a new sample rate (as a uint16_t value representing Hz) to the Sample Rate characteristic.

## Pinout

*   **I2C0:**
    *   SDA: GPIO16
    *   SCL: GPIO17
*   **LED:**
    *   On-board LED (controlled via CYW43)

## Code Structure

*   `main.c`: Main application logic, sensor reading, BLE handling, and calculations.
*   `6dof_sensor.gatt`: Defines the BLE GATT profile.
*   `lsm303_sensor/`: Contains the driver for the LSM303DLM sensor.
*   `server_common.c`/`server_common.h`: Common BLE server helper functions (assumed).

## Constants and Configuration

Key parameters are defined at the top of `main.c`:
*   `I2C_PORT`, `I2C_SDA`, `I2C_SCL`: I2C configuration.
*   `HEARTBEAT_PERIOD_MS`: Interval for BLE updates and LED toggle checks.
*   `SENSOR_READ_FREQUENCY_HZ`: Target frequency for reading the LSM303 sensor.
*   `MOVING_AVG_SIZE`: Size of the window for the moving average filter.
*   `TEMP_UPDATE_INTERVAL_MS`: How often the on-board temperature is read and potentially notified.
*   `LED_TOGGLE_INTERVAL_MS`: How often the LED state is toggled.

## License

This project is based on the Raspberry Pi (Trading) Ltd. `temp_sensor` example code and includes modifications.
SPDX-License-Identifier: BSD-3-Clause applies.

Further modifications/tweaks by C Gerrish (Gerrikoio) (c) May 2025.

## Acknowledgements

*   Raspberry Pi (Trading) Ltd. for the original `temp_sensor` example.
*   Google Code Assist for assistance in generating parts of the LSM303 sensor integration.

---

# STM32 Environmental Alarm System with BME280 & UART

An embedded firmware project that implements a real-time environmental monitoring and hazard mitigation system. Built on an **STM32F401** microcontroller, it interfaces with a **BME280** sensor to read atmospheric data and triggers a visual alert and serial data broadcast when specific safety thresholds are breached.

## 🏗️ System Architecture & Logic

The system continuously samples local environmental conditions at a frequency of **0.5 Hz (every 2 seconds)** using a state machine implemented via standard peripheral initialization and main looping:

1. **Sensor Interfacing**: Communicates with the BME280 sensor via **I2C1** (100 kHz Standard Mode), custom-wrapping the hardware abstraction layer (HAL) memory functions into the driver interface.
2. **Data Logging**: Formats and streams telemetry strings (Temperature, Humidity, Pressure) through the **USART2** hardware peripheral to a host PC using an external USB-to-UART TTL bridge at **115200 baud**.
3. **Threshold-Driven Alarm Logic**:
   - **Normal State**: If the ambient temperature stays $\le 28.0^\circ\text{C}$, the system logs regular telemetry and turns OFF the onboard LED (Active-Low configuration).
   - **Alarm State**: If the temperature surpasses $28.0^\circ\text{C}$, the firmware instantly pulls down the **GPIOC Pin 13** to turn ON the warning LED and overrides the serial output with an **`ALARM! HIGH TEMP`** broadcast.

## 📊 Technical Specifications & Registers

- **Microcontroller**: STM32F401 (ARM Cortex-M4 @ 84MHz)
- **Sensor**: Bosch BME280 (I2C Address: `0x76` shifted left to `0xEC` for HAL 8-bit addressing)
- **Sensor Configurations**:
  - `0xF2` (ctrl_hum): Humidity oversampling $\times 1$
  - `0xF4` (ctrl_meas): Temperature oversampling $\times 1$, Pressure oversampling $\times 1$, Normal Mode
  - `0xF5` (config): Standby time 0.5ms, Filter off

## 🔌 Hardware Wiring Diagram

| STM32F401 Board Pin | BME280 Sensor Pin | USB-to-UART TTL Pin | Description |
|---------------------|-------------------|---------------------|-------------|
| **3.3V**            | VCC               | -                   | Power Supply |
| **GND**             | GND               | GND                 | Common Ground Reference |
| **PA2 (TX)**        | -                 | RX                  | Serial Telemetry Output |
| **PA3 (RX)**        | -                 | TX                  | Serial Command Input (Optional) |
| **PB8 (I2C1_SCL)**  | SCL               | -                   | I2C Clock Line |
| **PB9 (I2C1_SDA)**  | SDA               | -                   | I2C Data Line |
| **PC13**            | Onboard LED       | -                   | Status Indicator (Active-Low) |

## 🚀 How to Run the Project

1. Clone this repository to your local workspace.
2. Import the project into **STM32CubeIDE**.
3. Connect your STM32F401 board and your USB-to-UART module to the PC.
4. Open your favorite Serial Terminal (e.g., Putty, TeraTerm, or Minicom) and set the port to **115200 Baud, 8N1**.
5. Build and Flash the project onto the target microcontroller.

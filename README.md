# EnergyLink
### Wireless Power Transfer System

## Overview

EnergyLink is a prototype designed to demonstrate wireless power transmission between two nearby coils. It transfers electrical energy from a powered transmitter to a receiver without requiring a direct wired connection between them.

The receiver module converts the transferred energy into a regulated 5V DC output, which is used to power an Arduino Nano.

The first version of EnergyLink uses a Qi wireless charging transmitter and its compatible receiver module. This implementation demonstrates short-range inductive wireless power transfer rather than energy harvesting from ordinary ambient radio waves.

## Requirements

| Component | Specifications |
|---|---|
| Arduino Nano | Arduino Nano V3.0, ATmega328P, 5V |
| Qi Transmitter | Wireless charging transmitter pad with transmitter coil |
| Qi Receiver | Matching receiver module with regulated 5V DC output |
| USB Power Supply | 5V USB adapter compatible with the transmitter |
| USB Cable | USB-A-to-MINI-B cable for programming a classic Arduino Nano |
| Multimeter | For measuring voltage and current |
| Jumper Wires | For electrical connections |
| Breadboard | Optional, for prototyping |

### Hardware Specifications

- The transmitter and receiver must be compatible with each other.
- The receiver must provide a regulated 5V DC output.
- The receiver should support an output current of at least 500 mA.
- Both transmitter and receiver coils should be included in the kit.
- The USB power supply must meet the transmitter's input voltage and current requirements.

## How It Works

EnergyLink operates using the principle of electromagnetic induction.

The system consists of two primary components:

### 1. Transmitter

The Qi transmitter receives electrical power from a USB power supply. It uses an internal coil to generate a changing magnetic field.

### 2. Receiver

When the receiver coil is positioned close to the transmitter coil, the changing magnetic field induces an electrical voltage in the receiver.

The receiver module processes the received energy and converts it into a regulated 5V DC output.

This output can then power the Arduino Nano.

### Power Transfer Process

```text
USB Power Supply
       |
       v
Qi Transmitter
       |
       v
Changing Magnetic Field
       |
       v
Qi Receiver
       |
       v
Regulated 5V DC Output
       |
       v
Arduino Nano
```

The transmitter and receiver must remain sufficiently close and properly aligned to maintain effective power transfer.

EnergyLink does not generate electricity. All transferred energy originates from the USB power supply connected to the transmitter.

## Connections

Follow these steps to assemble the system:

**1. Power the Transmitter**

Connect the Qi transmitter pad to a compatible 5V USB power adapter according to the manufacturer's specifications.

**2. Connect the Receiver to the Arduino**

Connect the receiver module's output pins to the Arduino Nano.

| Qi Receiver | Arduino Nano |
|---|---|
| 5V / VOUT | 5V |
| GND | GND |

**3. Align the Coils**

Position the receiver coil directly above the transmitter coil. Adjust their alignment to establish a stable wireless power connection.

**4. Verify the Output**

Use a multimeter to verify that the receiver provides a regulated 5V DC output before connecting it to the Arduino.

**5. Power the Arduino**

Once the output voltage has been verified, connect the receiver's 5V and GND outputs to the corresponding Arduino Nano pins.

## Safety and Technical Considerations

- Verify the receiver's output voltage before connecting it to the Arduino.
- Never connect an unregulated receiver output directly to the Nano's 5V pin.
- Ensure that the receiver can supply sufficient current for the Arduino and its connected peripherals.
- Avoid short-circuiting the receiver's output terminals.
- Follow the manufacturer's voltage, current, and coil alignment specifications.
- Avoid simultaneously powering the Arduino through its 5V pin and another 5V source unless the power paths are properly isolated.

## Project Objectives

EnergyLink aims to demonstrate the fundamentals of wireless power transmission through a practical hardware implementation.

The project explores several electronics concepts:

- Electromagnetic induction.
- Wireless power transmission.
- Qi wireless charging technology.
- Voltage regulation and power conversion.
- Arduino power management.
- Contactless energy transfer.

## Author

**Deadcode**

## License

This project is licensed under the MIT License. You are free to use, modify, distribute, and build upon this project under the terms of the license.

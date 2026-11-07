# EnergyLink — Component Register

**Project:** EnergyLink  
**Document:** Component Register  
**Version:** 0.1 (Initial Research)  
**Design Target:** 5W Single-Device Wireless Charger  
**Technology:** Qi-Compatible Inductive Wireless Power Transfer  
**PCB Software:** KiCad 9.x  
**Project Status:** Theoretical / Research and Design

---

# 1. Project Specifications

| Specification | Target |
|---|---|
| Charging technology | Inductive wireless power transfer |
| Compatibility | Qi-compatible receiver |
| Target output power | 5W |
| Nominal receiver output | 5V DC |
| Maximum nominal output current | 1A |
| Transmitter input | 5V DC (preliminary) |
| Charging method | Resonant inductive coupling |
| Main controller | To be finalized |
| Monitoring controller | ESP32 |
| Temperature monitoring | NTC thermistor |
| Current monitoring | Dedicated current-monitoring IC |
| PCB design | Custom PCB |

**Important:** These are initial design targets, not verified operating specifications.

---

# 2. Main Transmitter PCB Components

The transmitter PCB is the charging base. It generates the magnetic field used to transfer energy to a compatible receiver.

## 2.1 Wireless Power Controller

| Reference | Component | Manufacturer | Exact Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| U1 | Wireless Power Transmitter Controller | Texas Instruments | BQ500212A | VQFN-48, 7x7mm | Custom footprint required / verify against RGZ package drawing |

**Purpose:**

- Controls wireless power transmission.
- Detects compatible receivers.
- Manages power transfer.
- Supports foreign object detection.
- Provides charging status and fault indications.

**Datasheet:** BQ500212A, Revision D.

**Important:** This is a legacy reference-design candidate. TI marks it as not recommended for new designs. We must investigate a currently recommended transmitter controller before locking the final design.

---

## 2.2 Power Stage

These components are taken from TI's historical BQ500212A reference design.

| Reference | Component | Manufacturer | Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| U2 | NexFET Power Stage | Texas Instruments | CSD97376Q4M | VSON-CLIP-8, 3.5x4.5mm | Custom footprint / verify land pattern |
| U3 | NexFET Power Stage | Texas Instruments | CSD97376Q4M | VSON-CLIP-8, 3.5x4.5mm | Custom footprint / verify land pattern |

**Purpose:**

These components implement the power switching stage in the historical reference design.

They must not be connected based solely on this component list. Their exact circuit connections and suitability must be verified against the controller reference schematic.

---

## 2.3 Power Regulation

| Reference | Component | Manufacturer | Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| U4 | 3.3V LDO Regulator | Texas Instruments | TLV70033DDC | SOT-23-5 | Package_TO_SOT_SMD:SOT-23-5 |

**Purpose:**

Provides a regulated 3.3V supply for the transmitter controller's supporting circuitry.

---

# 3. Monitoring and Intelligence Components

These components are proposed additions to EnergyLink. They are not part of the original transmitter reference design.

## 3.1 Main Microcontroller

| Reference | Component | Manufacturer | Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| U5 | ESP32-C3-MINI-1 | Espressif | ESP32-C3-MINI-1-N4 | RF Module | RF_Module:ESP32-C3-MINI-1 |

**Purpose:**

- Collects sensor measurements.
- Monitors operating conditions.
- Calculates power consumption.
- Communicates with a monitoring interface.
- Provides data for the EnergyLink dashboard.

The ESP32 will be a monitoring controller, not a replacement for the Qi transmitter controller.

## 3.2 Current and Voltage Monitoring

| Reference | Component | Manufacturer | Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| U6 | Current/Power Monitor | Texas Instruments | INA219AIDCNR | SOT-23-8 | Package_TO_SOT_SMD:SOT-23-8 |

**Purpose:**

- Measures bus voltage.
- Measures current through an external shunt.
- Calculates electrical power.
- Communicates measurements through I2C.

We will initially use this to monitor the DC input supply.

A separate measurement point will be required for receiver output measurements.

## 3.3 Temperature Monitoring

| Reference | Component | Manufacturer | Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| RT1 | NTC Thermistor, 10kΩ | TDK | NTCG163JF103F | 0603 | Resistor_SMD:R_0603_1608Metric |
| R1 | Pull-up Resistor, 10kΩ, 1% | Yageo | RC0603FR-0710KL | 0603 | Resistor_SMD:R_0603_1608Metric |

**Purpose:**

- Measures temperature near the transmitting coil.
- Provides temperature information to the ESP32 ADC.
- Allows the firmware to detect abnormal temperature conditions.

The thermistor must be positioned near the area we actually want to monitor.

---

# 4. Wireless Power Transfer Components

## 4.1 Transmitter Coil

| Reference | Component | Manufacturer | Part Number | Specification |
|---|---|---|---|---|
| L1 | Wireless Power Transmitter Coil | Würth Elektronik | 760308111 | 6.3µH, 55x55mm |

**Purpose:**

Generates the alternating magnetic field used for wireless power transfer.

This coil is documented in TI's historical BQ500212A reference design.

**Important:** Coil compatibility must be verified against the final transmitter controller, operating frequency, resonant network, and Qi coil requirements.

## 4.2 Resonant Capacitors

The transmitter coil works with a resonant capacitor network.

The following are examples from TI's historical reference design:

| Reference | Value | Manufacturer | Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| C27 | 100nF | TDK | C3225C0G1H104K250AA | 1210 | Capacitor_SMD:C_1210_3225Metric |
| C28 | 100nF | TDK | C3225C0G1H104K250AA | 1210 | Capacitor_SMD:C_1210_3225Metric |
| C29 | 100nF | TDK | C3225C0G1H104K250AA | 1210 | Capacitor_SMD:C_1210_3225Metric |
| C30 | 100nF | TDK | C3225C0G1H104K250AA | 1210 | Capacitor_SMD:C_1210_3225Metric |

**WARNING:** These capacitor values must not be copied directly into a new resonant circuit without checking the original topology, coil inductance, frequency, voltage rating, and effective capacitance.

---

# 5. Receiver Components

The receiver is the second half of the wireless power system.

For the first design, the receiver is treated as a separate circuit or test board. It is not placed on the charging-base PCB.

## 5.1 Wireless Power Receiver Controller

| Reference | Component | Manufacturer | Part Number | Package | KiCad Footprint |
|---|---|---|---|---|---|
| U1 | Wireless Power Receiver | Texas Instruments | BQ51013CRHLR | VQFN-20, 4.5x3.5mm | Custom footprint based on TI RHL0020A land pattern |

**Datasheet:** BQ51013C, Revision A.

**Purpose:**

- Receives wireless power from the transmitting coil.
- Rectifies the received AC voltage.
- Regulates the output voltage.
- Implements Qi receiver communication.
- Supports foreign object detection communication.
- Provides thermal shutdown.

**Specifications:**

| Parameter | Value |
|---|---|
| Wireless power standard | Qi / WPC 1.3 |
| Maximum receiver power | 5W |
| Nominal regulated output | 5V |
| Maximum input voltage | 20V absolute maximum |
| Package | VQFN-20 |

The receiver coil and its matching network must be selected using the manufacturer's reference design.

---

# 6. Passive Components and Footprint Standards

The following KiCad footprint conventions will be used throughout the project.

| Component | Package | KiCad Footprint |
|---|---|---|
| Small resistors | 0603 | Resistor_SMD:R_0603_1608Metric |
| Small capacitors | 0603 | Capacitor_SMD:C_0603_1608Metric |
| Medium capacitors | 0805 | Capacitor_SMD:C_0805_2012Metric |
| Large capacitors | 1206 | Capacitor_SMD:C_1206_3216Metric |
| Power capacitors | 1210 | Capacitor_SMD:C_1210_3225Metric |
| Small LEDs | 0805 | LED_SMD:LED_0805_2012Metric |
| SOT-23 components | SOT-23 | Package_TO_SOT_SMD:SOT-23 |
| SOT-23-5 ICs | SOT-23-5 | Package_TO_SOT_SMD:SOT-23-5 |
| SOT-23-8 ICs | SOT-23-8 | Package_TO_SOT_SMD:SOT-23-8 |
| QFN components | According to datasheet | Package_DFN_QFN |

**Important:** A matching package name does not guarantee a matching footprint. Every IC footprint must be checked against its manufacturer's mechanical drawing and recommended PCB land pattern.

---

# 7. Power Input and Connections

| Reference | Component | Specification | Status |
|---|---|---|---|
| J1 | DC Input Connector | 5V DC input | Not selected |
| J2 | Transmitter Coil Connector | Two-terminal coil connection | Not selected |
| J3 | Programming Connector | ESP32 programming interface | Not selected |
| J4 | OLED Connector | I2C interface | Not selected |
| TP1 | Input Voltage Test Point | VIN | Required |
| TP2 | Ground Test Point | GND | Required |
| TP3 | Current Measurement Test Point | Current monitoring | Required |
| TP4 | Temperature Test Point | Temperature signal | Required |

Connector selection depends on current requirements, mechanical design, and PCB layout.

---

# 8. Design Rules

The following rules apply to all EnergyLink components.

1. Every IC must have an official datasheet.
2. Every component must have an exact manufacturer part number.
3. Every footprint must match the manufacturer's package drawing.
4. Power components must be selected according to their voltage and current ratings.
5. Capacitors must be selected according to voltage rating, tolerance, dielectric, and effective capacitance.
6. Resistors must have appropriate power ratings and tolerances.
7. Thermal pads must be connected according to the datasheet recommendations.
8. Test points must be included for important power and measurement signals.
9. The PCB must be checked for thermal and electrical clearance.
10. No component should be considered finalized until its electrical role has been verified against the schematic.

---

# 9. Component Status

| Component | Status |
|---|---|
| BQ500212A | Legacy reference candidate; replace before final design |
| CSD97376Q4M | Historical reference-design component |
| TLV70033DDC | Historical reference-design component |
| ESP32-C3-MINI-1-N4 | Proposed monitoring controller |
| INA219AIDCNR | Proposed monitoring IC |
| NTCG163JF103F | Proposed temperature sensor |
| BQ51013CRHLR | Receiver candidate |
| 760308111 | Historical transmitter coil reference |
| Resonant capacitors | Values require circuit verification |
| Input connector | Not selected |
| Receiver coil | Not selected |
| PCB dimensions | Not determined |

---

# 10. Official Technical References

The following documents must be consulted during circuit development.

| Document | Reference |
|---|---|
| BQ500212A Datasheet | TI SLUSBD6D, Revision D |
| BQ500212A Reference Design | TI SLUUAP8B, Revision B |
| BQ51013C Datasheet | TI SLUSFU9, Revision A |
| CSD97376Q4M Datasheet | TI SLPS422, Revision B |
| INA219 Datasheet | TI SBOS448 |
| TLV700 Datasheet | TI SBVS072 |

---



**Document Status:** Initial Research — Not Approved for Manufacturing.

**Engineering Principle:** No component is considered final simply because it appears in this document. All selections must be verified against the final electrical design.

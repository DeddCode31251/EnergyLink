# EnergyLink — Schematic Design Specification

**Document:** `schematic.md`  
**Project:** EnergyLink  
**Version:** 0.2  
**Design target:** 5W Qi-compatible wireless charging transmitter  
**EDA:** KiCad 9.x  
**Architecture:** Single-coil, Qi Basic Power Profile (BPP), A11 transmitter  
**Status:** Design-development specification — not approved for manufacturing.

---

# 1. Project Overview

EnergyLink is a 5W wireless charging transmitter based on magnetic induction.

The transmitter creates an alternating magnetic field using a resonant circuit and a transmitter coil. A compatible Qi receiver captures that energy and converts it into regulated DC power.

## Target specifications

| Parameter | Target |
|---|---|
| Wireless standard | Qi BPP |
| Reference topology | A11 single-coil transmitter |
| Maximum receiver-side power | 5W |
| Input voltage | 5V DC |
| Input connector | USB-C |
| Input supply capability | At least 5V / 2A |
| Transmitter coil | 6.3µH reference coil |
| Operating frequency | 110–205kHz reference range |
| Main controller | PIC16F18326 |
| Power stage | Full bridge |
| Monitoring | Optional external monitoring interface |

**Important:** The 5W rating refers to power delivered on the receiver side, not the USB input power.

# 2. Reference Design

The electrical architecture is based on:

**Microchip AN4880 — Microchip 5W Qi Wireless Transmitter**

This reference design provides:

- Qi communication and state-machine firmware.
- Receiver detection.
- ASK communication decoding.
- Full-bridge MOSFET control.
- Resonant tank voltage measurement.
- Resonant tank current measurement.
- Foreign Object Detection (FOD).
- Overvoltage and overcurrent protection.

Do not replace individual components with superficially similar parts without checking their electrical characteristics.

# 3. Schematic Sheet Structure

Create the following schematic hierarchy in KiCad:

```text
EnergyLink/
│
├── EnergyLink.kicad_pro
├── EnergyLink.kicad_sch
│
└── schematic/
    ├── 01_power_input.kicad_sch
    ├── 02_main_controller.kicad_sch
    ├── 03_gate_drivers.kicad_sch
    ├── 04_power_stage.kicad_sch
    ├── 05_analog_measurement.kicad_sch
    ├── 06_indicators.kicad_sch
    └── 07_monitoring_interface.kicad_sch
```

The main schematic should contain the hierarchical sheets above.

The monitoring interface is optional and must not interfere with the Qi controller.

# 4. Sheet 01 — Power Input

## Purpose

Provide a protected 5V input to the transmitter.

## Components

| Reference | Component | Part number / value | Notes |
|---|---|---|---|
| J1 | USB-C receptacle | USB-C female connector | 5V input |
| F1 | Fuse | 2A reference fuse | Input protection |
| FB1 | Ferrite bead | Reference-design component | EMI filtering |
| C1–C4 | Capacitors | 100nF, 25V | Input decoupling |
| R38–R39 | Resistors | 5.1kΩ reference values | USB-C CC pull-downs |
| TP8 | Test point | Input 5V | Measurement |
| TP9 | Test point | Ground | Measurement |

Use the exact USB-C connector and fuse specifications from the reference schematic before assigning final footprints.

## Connections

```text
USB-C VBUS
    |
    +---- F1 ---- +5V
                    |
                    +---- C1 ---- GND
                    |
                    +---- C2 ---- GND
                    |
                    +---- C3 ---- GND
                    |
                    +---- C4 ---- GND
                    |
                    +---- Power Stage
                    |
                    +---- Controller Supply
                    |
                    +---- Analog Circuits
```

USB-C connections:

- VBUS: input power.
- GND: circuit ground.
- CC1: 5.1kΩ pull-down to GND.
- CC2: 5.1kΩ pull-down to GND.
- Shield: follow the reference design's shield connection.
- Unused USB data pins: follow the connector/reference-design requirements.

**Do not connect the USB-C input directly to the transmitter coil.**

# 5. Sheet 02 — Main Controller

## U5 — PIC16F18326

**Manufacturer:** Microchip  
**Part:** PIC16F18326  
**Function:** Main Qi transmitter controller.

The PIC is responsible for the actual wireless charging protocol.

It handles:

- Qi communication.
- Receiver detection.
- ASK demodulation.
- PWM generation.
- Current measurement.
- Voltage measurement.
- Protection decisions.
- Charging state management.

The ESP32 must not replace this controller.

## Main controller signals

Use the PIC16F18326 symbol and its official datasheet pinout.

The following signal names are taken from the reference schematic:

| Signal | Function |
|---|---|
| MCLR | Controller reset/programming |
| ICSPDAT | Programming data |
| ICSPCLK | Programming clock |
| DEMOD_OUT | Analog demodulation input |
| DEMOD_DATA | Decoded communication signal |
| DEMOD_REF | Demodulation reference |
| LED1 | Indicator output |
| LED2 | Indicator output |
| PWM1 | First bridge control signal |
| PWM2 | Second bridge control signal |
| I_SENSE | Resonant current measurement |
| Q_MEAS | Resonant voltage measurement |

## Controller connections

Connect:

- VDD to +5V.
- VSS to GND.
- PWM1 to the first gate-driver control input.
- PWM2 to the second gate-driver control input.
- I_SENSE to the current-measurement output.
- Q_MEAS to the voltage-measurement output.
- DEMOD_OUT to the analog demodulation circuit.
- DEMOD_DATA to the appropriate controller input.
- DEMOD_REF to the analog reference circuit.

Add the controller's local bypass capacitors as shown in the Microchip reference schematic.

## Programming connector

Add:

| Reference | Component | Purpose |
|---|---|---|
| J2 | 4-pin programming header | PIC programming |

Signals:

```text
J2.1 -> MCLR
J2.2 -> ICSPDAT
J2.3 -> ICSPCLK
J2.4 -> GND
```

Verify the exact connector pin numbering and programming voltage requirements against the PIC datasheet before connecting a programmer.

# 6. Sheet 03 — Gate Drivers

The transmitter uses a full-bridge power stage.

A full bridge requires two MOSFET-driver ICs.

## U2 — MCP14A0155

## U4 — MCP14A0155

**Manufacturer:** Microchip  
**Function:** MOSFET gate driving.

Use two instances of the MCP14A0155.

Each driver handles one side of the full bridge.

## Connections

### Driver U2

```text
PWM1 -> Driver input A
PWM2 -> Driver input B

Driver output A -> AHO
Driver output B -> ALO
```

### Driver U4

```text
PWM2 -> Driver input A
PWM1 -> Driver input B

Driver output A -> BHO
Driver output B -> BLO
```

The exact input/output pin mapping must follow the MCP14A0155 datasheet and the reference schematic.

### Driver power

Connect:

- VDD to +5V.
- GND to GND.
- Enable pins according to the reference design.
- Local bypass capacitors close to each IC.

Use the reference schematic's capacitor values and connections.

# 7. Sheet 04 — Resonant Power Stage

This is the most important power section of EnergyLink.

It produces the alternating electrical signal that drives the transmitter coil.

## MOSFETs

The reference design uses four DMN3025LSD-13 MOSFETs.

| Reference | Component | Function |
|---|---|---|
| Q1A | DMN3025LSD-13 | Bridge MOSFET |
| Q1B | DMN3025LSD-13 | Bridge MOSFET |
| Q2A | DMN3025LSD-13 | Bridge MOSFET |
| Q2B | DMN3025LSD-13 | Bridge MOSFET |

Use the correct dual-MOSFET symbol arrangement and verify the physical package and pinout from the manufacturer's datasheet.

## Gate signals

| Signal | Connection |
|---|---|
| AHO | First high-side gate |
| ALO | First low-side gate |
| BHO | Second high-side gate |
| BLO | Second low-side gate |

Do not swap these signals.

## Transmitter coil

| Reference | Component | Value |
|---|---|---|
| L1 | Wireless power transmitter coil | 6.3µH |

The coil is part of the resonant tank.

## Resonant capacitors

| Reference | Component | Purpose |
|---|---|---|
| C23 | Resonant capacitor | Tank capacitance |
| C24 | Resonant capacitor | Tank capacitance |
| C25 | Resonant capacitor | Tank capacitance |
| C26 | Resonant capacitor | Tank capacitance |

The reference design uses four parallel capacitors.

Their combined capacitance is specified in the reference schematic.

**Do not substitute arbitrary capacitor values.**

The resonant network must be designed for the intended coil, frequency range, voltage, and current.

Use capacitors with suitable:

- Voltage rating.
- AC current rating.
- ESR.
- Temperature characteristics.
- Dielectric characteristics.

Use the reference schematic and the WPC A11 design recommendations to establish the final values.

## Test points

| Reference | Net |
|---|---|
| TP1 | Tank measurement |
| TP2 | Bridge/tank measurement |

These points are for engineering measurements. They are not ordinary low-voltage GPIO test points.

# 8. Sheet 05 — Analog Measurement

The transmitter must measure the electrical behavior of the resonant tank.

These measurements are required for receiver detection, Qi communication, and protection.

## U3 — MCP6061

**Function:** Resonant tank current measurement.

The reference design uses:

- R17 as the current-sense resistor.
- U3 as the current-sense amplifier.
- Additional resistors and capacitors for filtering.

### Current measurement path

```text
Power Stage
    |
    +---- R17 ---- Current Sense Circuit
                         |
                         +---- U3
                                |
                                +---- I_SENSE
                                         |
                                         +---- PIC ADC
```

R17 is a precision, low-resistance shunt resistor.

Use the exact reference value, package, and power rating.

Do not select a replacement based only on resistance.

## U1 — MCP6021

**Function:** Analog communication processing.

The circuit processes the demodulated signal received from the resonant tank.

Its output is connected to the controller's communication input.

## U6 — MCP6021

**Function:** Resonant tank voltage measurement.

The circuit processes the detected tank voltage and generates the Q_MEAS signal.

## Demodulation circuit

The reference circuit uses:

- R31.
- D3.
- C9.
- R32.
- R30.
- R33.
- U6.
- Associated filtering components.

Its purpose is to extract the amplitude information from the resonant tank signal.

The resulting signals are:

```text
DET_OUT
    |
    +---- DEMODULATION AMPLIFIER
    |          |
    |          +---- DEMOD_OUT
    |
    +---- VOLTAGE MEASUREMENT
               |
               +---- Q_MEAS
```

Reproduce the analog circuitry using the exact connections and values from Figure 2 of AN4880.

Do not simplify this section into a direct wire between the coil and the PIC.

# 9. Sheet 06 — Indicators

The reference design includes two LEDs.

| Reference | Component | Function |
|---|---|---|
| D4 | Red LED | Status indication |
| D5 | Green LED | Status indication |
| R27 | Resistor | D4 current limiting |
| R28 | Resistor | D5 current limiting |

Connect the LEDs to the controller's LED output signals using the reference circuit.

The firmware should determine the meaning of each LED state.

Suggested user-facing states:

- Power connected.
- Searching for receiver.
- Receiver detected.
- Charging.
- Charging completed.
- Fault detected.

The final LED behavior depends on the firmware implementation.

# 10. Sheet 07 — Monitoring Interface (Optional)

This is where EnergyLink can eventually gain additional functionality.

The original Qi controller must continue handling all safety-critical charging operations.

An external monitoring controller can collect information without interfering with the Qi state machine.

## Proposed monitoring controller

| Reference | Component | Part |
|---|---|---|
| U7 | Microcontroller | ESP32-C3-MINI-1 |

This is a proposed extension, not part of the original Microchip reference design.

## Proposed monitoring signals

| Signal | Description |
|---|---|
| VIN_MON | Input voltage |
| IIN_MON | Input current |
| TEMP_MON | Board temperature |
| STATUS | Charging state |
| FAULT | Fault notification |
| UART_TX | Serial output |
| UART_RX | Serial input |

## Temperature sensor

Use a 10kΩ NTC thermistor as a proposed temperature-monitoring component.

Place the sensor near the power MOSFETs or another relevant thermal hotspot.

The monitoring circuit must not interfere with the PIC's own protection mechanisms.

## Important

Do not connect the ESP32 directly to the MOSFET gate signals.

Do not allow the ESP32 to control the bridge independently.

Do not let monitoring firmware override the Qi controller's safety decisions.

# 11. Power Nets

Use consistent net names throughout KiCad.

| Net | Description |
|---|---|
| +5V | Main input supply |
| GND | Circuit ground |
| PWM1 | First PWM signal |
| PWM2 | Second PWM signal |
| AHO | First high-side gate |
| ALO | First low-side gate |
| BHO | Second high-side gate |
| BLO | Second low-side gate |
| I_SENSE | Resonant current measurement |
| Q_MEAS | Resonant voltage measurement |
| DEMOD_OUT | Analog demodulation output |
| DEMOD_DATA | Communication data |
| DEMOD_REF | Demodulation reference |
| DET_OUT | Detected tank signal |
| VIN_MON | Optional input voltage measurement |
| TEMP_MON | Optional temperature measurement |

# 12. Component Footprint Rules

Do not assign footprints based only on the component's name.

Every footprint must be checked against the manufacturer's package drawing.

The following components require special attention:

| Component | Footprint requirement |
|---|---|
| PIC16F18326 | Exact package variant from datasheet |
| MCP14A0155 | Exact package variant from datasheet |
| MCP6021 | Exact package variant from datasheet |
| MCP6061 | Exact package variant from datasheet |
| DMN3025LSD-13 | Exact package and pinout |
| USB-C connector | Exact manufacturer model |
| Coil | Mechanical footprint or custom outline |
| R17 | Package and power rating appropriate to shunt |
| C23–C26 | Package suitable for resonant current |

If a component does not have an appropriate KiCad footprint, create a custom footprint using the manufacturer's land-pattern drawing.

# 13. KiCad Electrical Rules

Before considering the schematic complete:

1. Assign a unique reference designator to every component.
2. Verify every IC power pin.
3. Verify every IC ground pin.
4. Check all MOSFET pin assignments.
5. Check all gate-driver connections.
6. Verify the resonant capacitor connections.
7. Verify the coil connections.
8. Check all bypass capacitors.
9. Add appropriate power flags.
10. Add test points.
11. Run Electrical Rules Checker (ERC).
12. Resolve all unintended ERC errors.
13. Annotate the schematic.
14. Save a PDF copy of the schematic.

Do not hide unused pins without checking their required electrical state.

# 14. Important Safety and Validation Requirements

EnergyLink is a power-electronics project.

A schematic that passes ERC is not necessarily electrically safe.

Before powering a physical board, the design must be reviewed for:

- MOSFET voltage and current ratings.
- Gate-driver timing and switching behavior.
- Dead-time and shoot-through prevention.
- Resonant capacitor voltage and current ratings.
- Coil current and thermal limits.
- Input fuse selection.
- Short-circuit protection.
- Overvoltage protection.
- Overcurrent protection.
- Foreign Object Detection.
- Thermal protection.
- PCB copper width and current capacity.
- EMI behavior.
- Qi interoperability.

Do not connect a phone or other valuable device to an unvalidated prototype.

# 15. Reference Documents

Use these documents as the engineering source of truth:

1. Microchip AN4880 — Microchip 5W Qi Wireless Transmitter.
2. PIC16F18326 official datasheet.
3. MCP14A0155 official datasheet.
4. MCP6021 official datasheet.
5. MCP6061 official datasheet.
6. DMN3025LSD-13 official datasheet.
7. Wireless Power Consortium Qi specifications.

The AN4880 schematic is the primary circuit reference for this version of EnergyLink.

# 16. Current Design Status

| Section | Status |
|---|---|
| 5V input architecture | Defined |
| USB-C input | Defined |
| Main controller | Selected as reference |
| Gate-driver architecture | Defined |
| Full-bridge architecture | Defined |
| Transmitter coil | Reference value identified |
| Resonant network | Reference topology identified; values require verification |
| Analog measurement | Architecture defined |
| Demodulation | Architecture defined |
| Indicators | Defined |
| ESP32 monitoring | Optional extension |
| Receiver circuit | Separate design |
| PCB layout | Not started |
| Firmware | Not integrated |
| Manufacturing validation | Not completed |

---

**Final design principle:** EnergyLink should first reproduce a working, documented 5W Qi transmitter architecture. Once that is understood and validated, add original monitoring and optimization features without compromising the Qi protocol or protection system.

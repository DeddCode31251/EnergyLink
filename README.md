# EnergyLink
Project EnergyLink is a prototype that sends power wirelessly from a powered transmitter to a nearby receiver. The receiver converts that energy into 5V to run an Arduino Nano.
The first version uses a Qi transmitter and matching receiver, so it demonstrates short-range wireless power transfer not energy collected from ordinary ambient radio waves. 

## Requirements
- Arduino Nano (Arduino Nano V3.0, ATmega328p, 5V) 
- Qi transmitter pad and matching receiver module with regulated 5V output (Choose a matched kit whose receiver explicitly provides regulated 5V DC and can supply at least 500 mA. The transmitter and receiver coils should be included)
- USB power supply for the transmitter, matching the pad's requirements (5V USB power adapter rated for the transmitter kit)
- USB cable or breakout to connect receiver to the Nano's USB input (USB-A-to-MINI-B cable for uploading code to a classic Nano)
- Multimeter for checking voltage and current
- Jumper wires and, optionally, a breadboard

## How it actually works
Project EnergyLink transfers power wirelessly between two nearby coils. The transmitter is powered by a USB supply and creates a changing magnetic field. A coil in the receiver picks up
energy from that field, and the receiver module converts it into regulated 5V power for the Arduino Nano.

The transmitter and receiver need to be close together and properly demonstrates short-range wireless power transfer; it does not generate energy or collect enough ordinary ambient radio waves to run the Arduino Nano.

## Connections
1- Connect the Qi transmitter to its recommended 5V USB Adapter.
2- Connect the receiver's 5V/ VOUT pin to the Nano's 5V pin.
3- Connect the receiver's GND pin to the Nano's GND pin.
4- Align the receiver coil over the transmitter coil.

## Author 
Deadcode

## License 
MIT

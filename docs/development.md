# Development

The DTU Float Forward ECU was developed through two main hardware iterations.

The goal of the process was to explore and test the embedded architecture using easily accessible development hardware before integrating the complete system into a custom PCB.

## Revision 1 — Functional Prototype

The first implementation used an **ESP32-S3 development board** as the central controller.

This approach reduced hardware development time during the early stages of the project and allowed firmware and peripheral integration to be developed before committing to a fully integrated PCB.

The prototype architecture was used to develop and exercise the main ECU functions listed below. This repository does not include test records or measured results for these functions:

* analog sensor acquisition
* CAN communication
* PWM-based actuator control
* auxiliary digital I/O
* battery monitoring
* temperature and humidity monitoring
* Wi-Fi connectivity
* MQTT telemetry

The external high-resolution analog acquisition architecture based on the AD7190 was also developed around this prototype.

This first iteration provided the functional basis for the integrated ECU.

## Lessons from Revision 1

Testing and integration of the first version also identified several areas that could be improved in a second revision.

One important issue was the CAN physical layer. The transceiver originally used in the system produced a logic level that was not directly compatible with the ESP32-S3 input voltage requirements.

Revision 2 therefore moved to a CAN transceiver with native 3.3 V logic compatibility.

GPIO allocation was also revisited to improve routing, peripheral allocation and future expandability.

More generally, the development-board approach was useful during prototyping but introduced additional connectors, wiring and hardware that were unnecessary in the final application.

These observations motivated the transition to a more integrated design.

## Revision 2 — Integrated ECU

Revision 2 replaces the ESP32-S3 development board with an **ESP32-S3-WROOM module mounted directly on the PCB**.

While retaining the previously developed system architecture, the new PCB integrates and refines the main hardware subsystems:

* ESP32-S3 processing and control
* native USB programming and debugging
* onboard power regulation and USB power path
* CAN physical layer
* AD7190 high-resolution analog acquisition
* four configurable PWM output stages
* four configurable auxiliary GPIO interfaces
* low-voltage battery monitoring
* assembly-time configuration through solder bridges

The connector architecture was also reorganised so that power, CAN, analog acquisition, PWM outputs, GPIO interfaces and programming connections use separate connectors.

This makes integration, debugging and modification easier than the single-connector approach used during the initial development.

## Current Status

The schematic and PCB layout of Revision 2 are complete.

The design represents the transition from a development-board-based functional prototype to a dedicated embedded control unit intended specifically for the DTU Float Forward boat.

**Revision 2 has not yet been manufactured or hardware-validated.**

For this reason, the functionality demonstrated using the development-board prototype should not be interpreted as hardware validation of the integrated Revision 2 PCB.

## Evidence and Next Milestone

The images in this repository are design exports, not photographs of an assembled V2 board. The documentation records design intent and prototype experience; it does not establish V2 performance, reliability or readiness for operation on the boat.

The next milestone is a pre-manufacture design review followed by assembly and measured bring-up. See [Validation Status and Plan](validation.md). Record future results against the tested hardware revision and firmware version, with test conditions and pass/fail criteria.

# Design Decisions

This document describes some of the main engineering decisions behind the second revision of the DTU Float Forward Embedded Control Unit.

The objective of the redesign was not only to replace the development board used during prototyping, but to integrate the required sensing, communication and auxiliary-control interfaces into a compact and configurable PCB suitable for installation in the boat.

## Integrated ESP32-S3

The first ECU prototype was built around an ESP32-S3 development board, selected for its integrated Wi-Fi connectivity and straightforward MQTT support. This provided a convenient platform for firmware development and allowed the main system architecture to be validated before committing to a fully integrated PCB.

In Revision 2, the development board was replaced by an **ESP32-S3-WROOM module directly integrated onto the PCB**.

This reduced external wiring and allowed the supporting circuitry, interfaces and GPIO allocation to be designed specifically around the requirements of the ECU.

Native USB support was also an important advantage of the ESP32-S3, as it allows firmware programming and debugging without an additional USB-to-UART converter.

## High-Resolution Analog Acquisition

Low-level analog measurements are acquired using an external **AD7190 24-bit sigma-delta ADC**.

The AD7190 was selected after considering several high-resolution ADC alternatives. It provided a good compromise between:

* low-noise performance
* configurable differential inputs
* sampling bandwidth
* digital filtering flexibility
* integration complexity

Using an external ADC also provides significantly better low-level measurement capability than relying exclusively on the ESP32-S3 internal ADC.

The final analog subsystem provides four external inputs.

Each input includes an RC filter to attenuate high-frequency noise arriving through the external wiring. The analog supply is additionally filtered from the main 5 V rail, with local decoupling around the ADC.

Analog and digital ground domains are connected through a defined link, providing a common reference while allowing the analog return path to be controlled.

## Configurable PWM Output Stages

The ESP32-S3 PWM pins are not exposed directly to external devices.

Instead, each of the four PWM channels drives an N-channel MOSFET configured as an open-drain-style output stage.

This provides two main advantages:

1. the external PWM voltage does not have to be limited to the 3.3 V logic level of the ESP32-S3;
2. the same PCB can interface with devices requiring different PWM voltage levels.

A three-pad solder bridge on each channel selects the pull-up voltage used by the output stage.

Because the MOSFET stage is inverting, the corresponding inversion is handled in firmware.

This configuration was chosen to provide a simple hardware interface while keeping the output stage adaptable to different external actuators.

## Configurable GPIO Interfaces

The four auxiliary GPIO channels were designed as configurable interfaces rather than fixed digital inputs or outputs.

Each channel includes assembly-time configuration through solder bridges.

The interface supports:

* selectable pull-up bias
* selectable pull-down bias
* series resistance for basic protection
* optional bypass of the series resistor

This allows the same physical GPIO interface to be adapted to external sensors, pilot buttons or other digital devices without redesigning the PCB.

The solder bridges are intended as assembly-time configuration options rather than switches that are changed during normal operation.

## CAN Interface

CAN is used as one of the main communication interfaces between embedded nodes in the boat.

The ECU integrates the CAN physical layer directly onto the ECU using a **3.3 V CAN transceiver**, avoiding the need for an external transceiver module.

The external CAN interface includes ESD protection and configurable split termination.

The termination can be enabled using solder bridges when the ECU is installed at one physical end of the CAN bus. When operating as an intermediate node, the termination can be left disconnected.

This makes the same ECU compatible with different physical locations within the CAN network.

## Native USB Programming Interface

Removing the ESP32-S3 development board meant that programming and debugging functionality had to be integrated directly into the PCB.

The ESP32-S3 native USB interface is therefore routed to USB-C and external programming connections.

This provides direct firmware flashing and debugging while avoiding an additional USB-to-UART interface.

Protection is included on the external USB connection to improve robustness during development and connection to external equipment.

## Dual Power Path

The ECU can be powered either from its normal external supply or through USB during development.

The main input is converted to the local 5 V rail, while USB VBUS provides an alternative source during programming and bench work.

A power multiplexer manages these sources before the local 5 V rail is converted to 3.3 V for the ESP32-S3 and low-voltage digital electronics.

This makes it possible to program and work with the ECU without powering the complete electrical system of the boat.

## Battery Monitoring

The low-voltage auxiliary battery is monitored independently through the ECU.

A resistive divider scales the battery voltage to a range compatible with an analog-capable ESP32-S3 input.

The firmware can then estimate the battery voltage and include it in the onboard monitoring and telemetry system.

## PCB Layout

The PCB layout was organised around functional separation.

Particular attention was given to the noise-sensitive analog acquisition section, keeping the ADC and analog front-end close to the corresponding external connections and separating them from faster-switching digital circuitry.

The layout also prioritises accessibility to:

* external connectors
* configuration solder bridges
* supply test points
* mounting holes

Overall, the layout reflects the main objective of Revision 2: integrating the previously validated functionality into a compact PCB while retaining enough hardware configurability for system integration and future development.

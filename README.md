# DTU Float Forward — Embedded Control Unit

Custom ECU hardware designed for the **DTU Float Forward electric racing boat**, combining sensing, communication and auxiliary control around an **ESP32-S3-WROOM module**.

> **Revision 2 status: schematic and PCB layout complete; not yet manufactured or hardware-validated.** The images are CAD renders and design exports. Earlier prototype work does not establish validation of the integrated V2 PCB.

![Revision 2 CAD render of the ECU PCB, top view](assets/pcb_render_top.png)

## Project Context and Contribution

The ECU was developed as part of **DTU Float Forward**, DTU's electric racing boat project for the Monaco Energy Boat Challenge.

I was responsible for the ECU hardware design, from system architecture and component selection to schematic design and PCB layout.

## At a Glance

| Area | Design scope |
| --- | --- |
| Processing | ESP32-S3-WROOM module with native USB programming/debugging |
| Acquisition | AD7190 24-bit sigma-delta ADC with four external analog input lines |
| Communication | CAN physical layer and Wi-Fi/MQTT telemetry |
| Auxiliary interfaces | Four configurable PWM signal outputs and four configurable GPIO interfaces |
| Power and monitoring | External/USB power paths, local 5 V and 3.3 V rails, auxiliary battery monitoring |
| Firmware | ESP-IDF with modular drivers and application functionality; not included here |

## System Architecture

![ECU architecture](assets/ecu_architecture.svg)

The **ESP32-S3** is the central controller. An external **AD7190** handles analog acquisition over SPI, while CAN connects the ECU to the boat network. Configurable GPIO interfaces support external sensors and pilot-interface signals, and PWM stages provide control signals for auxiliary actuators such as cooling devices.

Wi-Fi provides the connection for MQTT telemetry. The sensors, actuators and MQTT broker shown in the diagram are external to the PCB.

## Hardware Design Highlights

### High-Resolution Analog Acquisition

![Analog front-end](assets/schematic_blocks/analog_frontend.png)

The external **AD7190** was selected for low-level sensor acquisition. The front-end includes RC input filtering, a filtered analog supply, local decoupling and a defined connection between analog and digital ground domains.

The ADC supports two differential or four pseudo-differential channels through a multiplexer. Its nominal 24-bit resolution describes the converter, rather than the achieved accuracy of the complete acquisition chain.

Reference: [Analog Devices AD7190 documentation](https://www.analog.com/en/products/ad7190.html).

### Configurable PWM Output Stages

![PWM output stages](assets/schematic_blocks/pwm_output_stages.png)

Each of the four channels uses an N-channel MOSFET as an open-drain-style signal output. A three-pad solder bridge selects the pull-up voltage during assembly, allowing adaptation to different actuator control inputs.

The stages invert the logic, which firmware must account for. They provide control signals rather than direct motor or pump power switching.

### Configurable GPIO Interfaces

![GPIO interface](assets/schematic_blocks/gpio_interface.png)

Four auxiliary GPIO channels support external sensors, buttons and digital interfaces. Solder bridges provide assembly-time selection of pull-up or pull-down bias and an optional bypass of the series resistor.

The series resistor limits current; external signal voltages must remain compatible with the ESP32-S3 pins.

### PCB Layout

![PCB layout export showing front and back copper routing](assets/pcb_layout_top.png)

The layout is organised by functional area, with attention to the analog acquisition section, connector accessibility, configuration points and the module antenna region. The export shows routing on both copper layers.

## Supporting Interfaces

### CAN

A 3.3 V CAN transceiver integrates the physical layer on the board. The interface includes ESD protection and split termination selectable through solder bridges, allowing configuration for an intermediate node or a node at a physical bus end.

### USB and Power

The USB-C connection uses the ESP32-S3 native USB interface for programming and debugging, avoiding an external USB-to-UART converter.

The power architecture provides external-supply and USB power paths for onboard operation and bench development, with local 5 V and 3.3 V rails.

### Battery Monitoring

A dedicated resistive divider scales the auxiliary battery voltage for an analog-capable ESP32-S3 input, providing a separate measurement for monitoring and telemetry.

## Development

**Revision 1 — Functional prototype.** An ESP32-S3 development board provided the platform for firmware development and functional prototyping of acquisition, CAN, auxiliary control and telemetry.

**Revision 2 — Integrated ECU.** The custom PCB replaces the development board with a directly mounted ESP32-S3-WROOM module and integrates the supporting interfaces, power circuitry and assembly-time configuration options. Separate connectors organise the external interfaces for system integration.

The next milestone is pre-manufacture review, followed by assembly and measured board bring-up. The [validation plan](docs/validation.md) describes the remaining checks and evidence to collect.

## Firmware

The firmware uses **ESP-IDF** with a modular architecture separating hardware drivers from application functionality. Its scope includes AD7190 acquisition, cooling-system control, battery and environmental sensing, CAN, network time synchronisation and MQTT telemetry.

The complete boat firmware is not included. Reusable hardware drivers may be published separately as standalone projects.

## Repository Scope and Documentation

This repository is a **technical portfolio and design case study** containing an architecture overview, selected schematic excerpts and PCB images. Complete schematics, KiCad/PCB sources, manufacturing files and the complete BOM are excluded.

- [Design Decisions](docs/design_decisions.md)
- [Development History](docs/development.md)
- [Validation Status and Plan](docs/validation.md)

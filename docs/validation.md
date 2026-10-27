# Validation Status and Plan

**Revision 2: schematic and PCB layout complete; not yet manufactured or hardware-validated.**

This is a proposed review and test plan, not a record of completed tests. Exact limits and pass criteria must be established from the full design, component specifications and boat interface requirements before testing.

## Evidence Available

| Item | What this repository establishes |
| --- | --- |
| Architecture diagram | Intended functional partition and signal paths |
| Selected schematic excerpts | ADC filtering, configurable PWM signal stages and GPIO configuration |
| PCB CAD render and layout export | Visual documentation of the V2 design |
| Development history | Narrative of the earlier development-board prototype; no test logs included |
| V2 bench or integration results | None included; hardware validation is pending |

## Before Manufacture

- Review complete schematics and PCB sources; resolve and record ERC/DRC findings and any justified exceptions.
- Check footprints, pin mappings, component ratings, connector orientation, assembly options and manufacturing outputs.
- Verify analog input modes, allowable input/common-mode ranges, reference arrangement and ADC configuration for the actual sensors.
- Review external and USB power paths, source isolation, input range, startup and thermal budget.
- Check PWM pull-up selections and intended loads; verify external GPIO signal levels and startup states.
- Verify CAN logic compatibility, termination options, bus protection and intended cable topology.
- Check antenna clearance across all layers and the intended enclosure against the manufacturer's guidance.

## After Assembly

| Area | Planned verification | Evidence to retain |
| --- | --- | --- |
| Initial power-up | Inspection, resistance checks, current-limited startup, rail voltages and current consumption | Assembly configuration, supply conditions and measured values |
| External/USB power | Each source independently and source transitions; unintended backfeed and thermal behavior | Voltage/current traces and temperatures at defined loads |
| Boot and USB | Boot/reset behavior, flashing and debug access | Firmware version, procedure and result |
| Analog acquisition | Known inputs, channel sequence, calibration, noise and settling at selected gain/filter settings | Raw data, reference instrument, settings and error/noise analysis |
| PWM interfaces | Polarity, voltage levels, rise/fall time, duty cycle and reset behavior under representative loads | Oscilloscope captures, pull-up selection and load conditions |
| GPIO and battery sensing | Selected bias/bypass settings, valid logic levels and battery-divider calibration | Configuration, input sweep and measured error |
| CAN | Communication with known nodes, bitrate, termination and error/recovery behavior | Bus setup, traffic captures and error counters |
| Wi-Fi/MQTT | Connectivity, reconnection and behavior in the intended enclosure | Network conditions, logs and communication range/throughput observations |
| Integration | Sensor/control operation, loss-of-communication behavior and representative electrical noise/load conditions | Defined test scenarios, observed results and unresolved issues |

Keep prototype results and integrated V2 results separate. Do not describe the board as validated, deployment-ready or proven in competition until the relevant evidence exists.

## Manufacturer References

- [Analog Devices AD7190 documentation](https://www.analog.com/en/products/ad7190.html)
- [Espressif ESP32-S3 hardware design guidelines](https://www.espressif.com/sites/default/files/documentation/esp32-s3_hardware_design_guidelines_en.pdf)

# Hardware Guide

This document describes the physical and electrical structure of the Solar Tracker project.

## Main hardware blocks

The system is divided into six hardware blocks: Arduino controller, light sensing block, irradiance measurement input, servo motor actuation, I2C orientation/time modules, and acoustic buzzer alerts.

## Recommended components

| Component | Recommended type | Function |
|---|---|---|
| Microcontroller | Arduino UNO, NANO, MEGA, or compatible | Runs the firmware |
| Servo motor | SG90 for small prototypes; stronger servo for larger panels | Adjusts panel angle |
| LDR sensors | 2 light dependent resistors with voltage dividers | Detect east/west light imbalance |
| Irradiance input | Analog sensor or calibrated light sensor module | Provides approximate W/m² value |
| Compass | HMC5883L module | Measures magnetic heading |
| RTC | PCF8563 module | Keeps time for sunrise/sunset schedule |
| Buzzer | Passive or active buzzer | Audible state/error feedback |
| Power supply | External regulated servo supply | Prevents Arduino reset during servo movement |

## Pin mapping

| Arduino pin | Signal | Description |
|---|---|---|
| A0 | West LDR | Analog sensor reading from west side |
| A1 | East LDR | Analog sensor reading from east side |
| A2 | Irradiance sensor | Analog input mapped to 0–1000 W/m² |
| D3 | Servo PWM | Servo signal line |
| D13 | Buzzer | Tone output |
| SDA/SCL | I2C bus | RTC and compass communication |

## LDR sensor arrangement

The two LDRs should be installed with a small divider or shade wall between them. The goal is to create a measurable difference when the panel is not perpendicular to the dominant light direction. One sensor should represent the east side, and the other should represent the west side.

The firmware calculates:

```text
error = westLdrRead - eastLdrRead
```

If the error exceeds the configured tolerance, the servo angle is corrected.

## Irradiance input

The firmware reads the irradiance input from `A2` and maps it as follows:

```cpp
int a2Value = analogRead(A2);
float a2Voltage = (a2Value / 1023.0) * 5.0;
float irradiance = (a2Voltage / 5.0) * 1000.0;
```

This means that, by default, 0 V corresponds to 0 W/m² and 5 V corresponds to 1000 W/m². If your sensor has another scale, adjust the formula in the firmware.

## Servo motor wiring

Use an external servo supply when the system moves a real panel or a structure with meaningful mechanical load. The Arduino 5 V pin is not recommended for heavy servo current.

| Servo wire | Connect to |
|---|---|
| Signal | Arduino D3 |
| VCC | External 5 V supply or appropriate servo supply |
| GND | External supply GND and Arduino GND |

## I2C wiring

The RTC and compass share the I2C bus.

| Board | SDA | SCL |
|---|---|---|
| Arduino UNO/NANO | A4 | A5 |
| Arduino MEGA | 20 | 21 |

## Compass installation

The compass should be mounted away from high-current wires, motors, steel screws, magnets, and other magnetic interference sources. Keep the sensor orientation fixed relative to the tracker frame.

## Mechanical files

The `hardware/3d-models/` directory contains STL files for prototyping. Before printing, review scale, orientation, servo compatibility, infill, material behavior outdoors, panel weight, and wind load.

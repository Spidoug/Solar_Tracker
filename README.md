# Solar Tracker

![Solar tracker](https://github.com/user-attachments/assets/14e7e882-e4fa-4f13-8470-cbe85fea057b)

**Solar Tracker** is an Arduino-based photovoltaic panel positioning system. It uses two LDR sensors, an irradiance input, a servo motor, a compass module, an RTC module, EEPROM persistence, and a browser-based Web Serial control interface to keep a solar panel aligned with the most favorable light direction during the configured operating window.

The project combines embedded control and a lightweight web dashboard. The Arduino firmware performs the real-time tracking logic, while the HTML interface allows the operator to connect through USB serial, set angles, sync the RTC, configure sunrise and sunset times, adjust magnetic declination, toggle operation modes, record calibration data, and export CSV files.

![Solar Tracker App](https://github.com/user-attachments/assets/cdbe6c58-a20d-438f-949f-b8ca73dcf23d)

## Project objective

The objective of this project is to maximize solar energy capture by orienting a photovoltaic panel toward the best available solar incidence angle throughout the day. The system is designed for small photovoltaic installations, educational experiments, solar-energy research, and prototypes that need low-cost automatic solar tracking.

The tracker uses LDR readings to compare light intensity on the east and west sides of the panel, an analog irradiance input to estimate solar incidence, an HMC5883L compass module to support orientation checks, and an RTC module to keep operation limited to a configured sunrise-to-sunset period. Configuration values are stored in EEPROM, allowing the system to keep important parameters after power cycling.

## Main features

- Arduino firmware for UNO, NANO, MEGA, and compatible boards.
- Servo-based panel angle control from **30° to 150°**.
- Active solar tracking based on LDR difference and PID control.
- Passive solar tracking based on time progression between sunrise and sunset.
- Manual positioning mode through the serial menu or web interface.
- Auto-calibration routine for finding the angle with maximum irradiance.
- RTC-based operating schedule.
- HMC5883L compass orientation check with magnetic declination compensation.
- EEPROM persistence for operating mode, schedule, magnetic declination, and filter state.
- Sudden-variation filter for reducing unstable movement caused by temporary shadows or fast irradiance disturbances.
- Buzzer alerts for startup, movement, confirmation, orientation errors, RTC errors, compass errors, and invalid input.
- Browser-based control panel using the Web Serial API.
- Real-time chart for irradiance versus panel angle using Chart.js.
- CSV export for calibration or experiment data.
- Included STL files for 3D-printed mechanical parts.

## Repository structure

```text
Solar-Tracker/
├── README.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
├── NOTICE.md
├── SECURITY.md
├── docs/
│   ├── ARDUINO_FIRMWARE.md
│   ├── HARDWARE_GUIDE.md
│   ├── OPERATING_GUIDE.md
│   ├── SERIAL_PROTOCOL.md
│   ├── WEB_APP.md
│   ├── TROUBLESHOOTING.md
│   └── ORIGINAL_README.md
├── hardware/
│   └── 3d-models/
│       ├── FanStBase.stl
│       ├── FanStGrille.stl
│       ├── FanStVert.stl
│       ├── Laser_Cradle_9mm_SG90.stl
│       ├── Servo_Box_3.stl
│       ├── arduino_uno_bottom.stl
│       └── arduino_uno_top.stl
└── src/
    ├── arduino/
    │   └── Solar_Tracker/
    │       └── Solar_Tracker.ino
    └── web/
        ├── index.html
        └── Solar Tracker.html
```

## System overview

The firmware runs in a loop and reads the sensors once per second. During the configured operating interval, it chooses the appropriate behavior based on the current mode:

In **active mode**, the tracker compares the west and east LDR readings. The difference between these readings becomes the tracking error. The firmware applies a PID-style correction and moves the servo when the error exceeds the configured tolerance.

In **passive mode**, the tracker does not depend primarily on the LDR difference. Instead, it maps the current time between sunrise and sunset to the servo angle range. At sunrise, the servo approaches the minimum angle. At sunset, it approaches the maximum angle.

In **manual mode**, the operator defines the desired angle. The tracker keeps the servo at that manually configured angle until automatic behavior is restored.

Outside the configured operating time, the firmware returns the servo to a neutral position and detaches the servo to reduce unnecessary power consumption.

## Hardware components

The project is designed around commonly available Arduino modules:

| Component | Purpose |
|---|---|
| Arduino UNO/NANO/MEGA or compatible board | Main controller |
| Servo motor, such as SG90 or compatible | Moves the solar panel structure |
| 2 × LDR sensors | Detect light imbalance between east and west sides |
| Irradiance sensor or analog light sensor | Measures solar irradiance through analog input A2 |
| HMC5883L compass module | Provides magnetic orientation data |
| PCF8563 RTC module | Keeps date and time for operating schedule |
| Buzzer | Provides acoustic alerts |
| 3D-printed parts | Mechanical support, servo mount, Arduino case, and structural parts |
| External servo power supply | Recommended for stable motion under load |

## Default pin mapping

| Arduino pin | Connected device | Notes |
|---|---|---|
| A0 | West LDR | Analog input |
| A1 | East LDR | Analog input |
| A2 | Irradiance sensor | Mapped from 0–5 V to 0–1000 W/m² in firmware |
| D3 | Servo signal | PWM-capable output |
| D13 | Buzzer | Tone alerts |
| SDA/SCL | RTC + compass | I2C bus |

For Arduino UNO and NANO, the I2C pins are normally A4/SDA and A5/SCL. For Arduino MEGA, they are normally pins 20/SDA and 21/SCL. Always connect all grounds together, especially when using an external servo power supply.

## Arduino dependencies

Install these libraries in the Arduino IDE before compiling:

- `RTClib`
- `Servo`
- `Adafruit HMC5883 Unified`
- `Adafruit Unified Sensor`, required by the HMC5883 library
- `Wire`, included with the Arduino core
- `EEPROM`, included with the Arduino core

## Getting started

1. Clone or download this repository.
2. Open `src/arduino/Solar_Tracker/Solar_Tracker.ino` in the Arduino IDE.
3. Install the required libraries from the Arduino Library Manager.
4. Select the correct board and serial port.
5. Upload the firmware to the Arduino.
6. Assemble the LDR sensors, irradiance input, compass, RTC, servo motor, and buzzer according to the pin mapping.
7. Open `src/web/index.html` in a browser that supports the Web Serial API.
8. Connect to the Arduino using the **Connect to Arduino** button.
9. Configure time, sunrise/sunset, magnetic declination, and operation mode.
10. Test the servo movement without a heavy panel before installing the final mechanical load.

## Web control interface

The web app is a single HTML file that communicates with the Arduino through USB serial. It provides modules for serial connection, manual angle setting, automatic mode restoration, RTC synchronization, schedule configuration, magnetic declination, active/passive mode switching, sudden-variation filter control, data recording, CSV export, compass display, and an irradiance-versus-angle chart.

The interface sends the same menu commands expected by the Arduino serial menu. For example, the manual angle command sends option `7`, followed by the desired angle.

## Serial menu commands

The firmware exposes a simple text-based serial menu at **9600 baud**:

| Option | Action | Expected follow-up input |
|---|---|---|
| `1` | Adjust system date and time | `YYYY MM DD HH MM SS` |
| `2` | Adjust sunrise time | `HH MM` |
| `3` | Adjust sunset time | `HH MM` |
| `4` | Toggle sudden variation filter | None |
| `5` | Adjust magnetic declination | Decimal value in degrees |
| `6` | Toggle active/passive operation mode | None |
| `7` | Enter manual positioning mode | Angle from 30 to 150; invalid value returns to automatic mode |

The web app uses these commands internally, so both the browser interface and the Arduino Serial Monitor can control the same firmware.

## Operation modes

### Active mode

Active mode uses the two LDR sensors as the main feedback source. The panel moves when the west/east light difference exceeds the tolerance threshold. A PID-style correction is applied using the configured constants in the firmware:

```cpp
float Kp = 0.5, Ki = 0.1, Kd = 0.05;
```

### Passive mode

Passive mode uses the configured sunrise and sunset times to calculate a time ratio. The firmware maps the current time to the servo range from 30° to 150°. This mode is useful when LDR readings are noisy, the sky is partially cloudy, or the mechanical structure requires predictable movement.

### Manual mode

Manual mode allows the operator to set a fixed angle. This is useful for testing, calibration, inspection, or experiments that require a known panel position.

### Auto-calibration

When active operation is enabled, the firmware can scan the servo range, measure irradiance at each angle, and store the best angle found during that scan. This routine helps identify the angle with the highest measured solar incidence at calibration time.

## Data recording and CSV export

The web interface can record values received from the Arduino and export them as a semicolon-separated CSV file named `Solar_Tracker.csv`. The exported columns are:

```text
Date/Time;Angle (°);Irradiance (W/m²);Compass (°)
```

This is useful for testing panel performance, validating the calibration routine, and comparing irradiance values at different angles.

## 3D models

The repository includes STL files for mechanical prototyping:

| File | Description |
|---|---|
| `FanStBase.stl` | Base structure |
| `FanStGrille.stl` | Grille/support element |
| `FanStVert.stl` | Vertical support structure |
| `Laser_Cradle_9mm_SG90.stl` | SG90 servo cradle / small actuator support |
| `Servo_Box_3.stl` | Servo box |
| `arduino_uno_bottom.stl` | Bottom half of Arduino UNO enclosure |
| `arduino_uno_top.stl` | Top half of Arduino UNO enclosure |

Review part dimensions, orientation, infill, and material selection according to the final panel weight and operating environment.

## Recommended workflow

Start with the electronics on a bench before installing the complete mechanical system. Confirm that the RTC is recognized, the compass is working, LDR values change correctly, the irradiance input is within the expected 0–5 V range, and the servo can move across the allowed range without obstruction. After the bench test, assemble the mechanical structure, set the local magnetic declination, align the panel axis, and perform calibration under real sunlight.

## Safety notes

Do not power the servo motor directly from the Arduino 5 V pin when moving a real panel or a heavy mechanism. Use an external regulated supply suitable for the servo current and connect the grounds together. Avoid mechanical end-stop collisions by testing the 30° and 150° limits before attaching the final load.

## Potential expansions

- Add meteorological sensors to account for cloudy or unstable conditions.
- Replace the servo with a stepper motor and lead screw for larger photovoltaic panels.
- Add current, voltage, and power monitoring for real energy-yield evaluation.
- Add SD-card logging for offline data collection.
- Add Wi-Fi, Ethernet, MQTT, or Modbus communication for remote monitoring.
- Add a more complete calibration routine for compass hard-iron and soft-iron correction.
- Add enclosure protection for outdoor operation.

## Documentation

More detailed documentation is available in the `docs/` folder:

- [`docs/HARDWARE_GUIDE.md`](docs/HARDWARE_GUIDE.md)
- [`docs/ARDUINO_FIRMWARE.md`](docs/ARDUINO_FIRMWARE.md)
- [`docs/WEB_APP.md`](docs/WEB_APP.md)
- [`docs/SERIAL_PROTOCOL.md`](docs/SERIAL_PROTOCOL.md)
- [`docs/OPERATING_GUIDE.md`](docs/OPERATING_GUIDE.md)
- [`docs/TROUBLESHOOTING.md`](docs/TROUBLESHOOTING.md)

## License

This repository is provided with an **All Rights Reserved** license placeholder. Replace `LICENSE` with the license selected by the project owner before public distribution if open-source use, modification, or redistribution is intended.

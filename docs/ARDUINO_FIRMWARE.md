# Arduino Firmware Documentation

Firmware location:

```text
src/arduino/Solar_Tracker/Solar_Tracker.ino
```

The folder name matches the sketch name so it can be opened directly in the Arduino IDE.

## Purpose

The firmware controls the solar tracker by reading light sensors, calculating the best panel position, moving a servo motor, checking compass orientation, managing an RTC schedule, and storing configuration values in EEPROM.

## Libraries

The sketch includes:

```cpp
#include <Wire.h>
#include "RTClib.h"
#include <Servo.h>
#include <Adafruit_HMC5883_U.h>
#include <EEPROM.h>
#include <math.h>
```

Required Arduino libraries: `RTClib`, `Servo`, `Adafruit HMC5883 Unified`, `Adafruit Unified Sensor`, `Wire`, and `EEPROM`.

## Important constants

```cpp
const int westLdr = A0;
const int eastLdr = A1;
const int servoPin = 3;
const int buzzerPin = 13;
const int minLimit = 30, maxLimit = 150;
int servoAngle = 90;
```

The default servo range is limited to 30° through 150° to reduce the risk of mechanical overload or end-stop collision.

## PID-style active tracking

Active mode uses the LDR difference as the control error:

```cpp
int error = westLdrRead - eastLdrRead;
float adjustment = Kp * error + Ki * errorSum + Kd * derivative;
servoAngle = servoAngle + adjustment;
```

Default values:

```cpp
float Kp = 0.5, Ki = 0.1, Kd = 0.05;
```

The integral sum is constrained to avoid excessive accumulated correction.

## Moving average

The firmware applies a moving average to the two LDR inputs using five samples. This helps reduce noise before calculating the tracking error.

## Sudden variation filter

The sudden-variation filter changes the tolerance used to decide whether the servo should move. A higher tolerance makes the tracker less sensitive to sudden or temporary light changes.

## Passive tracking

In passive mode, the firmware calculates how far the current time is between sunrise and sunset, then maps that progress to the servo angle range.

## Manual mode

Manual mode is entered through menu option `7`. If the received angle is between 30° and 150°, the firmware writes that angle to the servo and keeps the tracker in manual mode. If an invalid value is entered, the firmware exits manual behavior and returns to automatic operation.

## EEPROM persistence

The firmware uses a signature byte to determine whether EEPROM has already been initialized:

```cpp
#define EEPROM_SIGNATURE 0xAB
```

Stored values include the filter state, sunrise time, sunset time, operation mode, and magnetic declination.

## Compass heading

The firmware reads the HMC5883L compass module, compensates the heading with magnetic declination and a mechanical offset, and normalizes the result to 0°–360°.

## Auto-calibration

The auto-calibration routine scans the full servo range from 30° to 150°. For each angle, it reads the irradiance input and tracks the angle with the highest measured value. After the scan, the servo moves to the best angle found.

## Firmware review note

When preparing this project for hardware testing, review the initialization order of the RTC and compass modules in `setup()`. The current sketch starts compass calibration before the compass initialization block appears later in `setup()`. If the compass does not respond correctly, move the compass initialization step before the calibration routine.

## Suggested firmware improvements

Add structured serial messages, a menu option for manually starting auto-calibration, safer servo ramping, EEPROM versioning, configurable PID constants, and validation for schedule values.

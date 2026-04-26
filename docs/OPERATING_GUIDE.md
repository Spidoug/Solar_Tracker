# Operating Guide

This guide explains how to operate the Solar Tracker after the hardware has been assembled and the firmware has been uploaded.

## 1. Bench test first

Before mounting a photovoltaic panel, test the system on a bench. Confirm the servo moves safely between 30° and 150°, the LDR values change when shaded, the irradiance input changes with light level, the RTC is detected, the compass is detected, and the buzzer alerts are audible.

## 2. Upload the firmware

Open this file in the Arduino IDE:

```text
src/arduino/Solar_Tracker/Solar_Tracker.ino
```

Install the required libraries, select the correct board and port, then upload.

## 3. Open the web interface

Open:

```text
src/web/index.html
```

Click **Connect to Arduino** and select the correct serial port.

## 4. Synchronize date and time

Use **Sync Time and Date** to set the RTC using the computer time. Passive mode and operating-hour checks depend on RTC values.

## 5. Configure sunrise and sunset

Set the desired operating interval. The default values stored by the firmware are 06:00 and 18:00. Outside this interval, the firmware returns the servo to a neutral angle and detaches it.

## 6. Configure magnetic declination

Set the magnetic declination for the installation location. This helps the compass orientation check produce a more accurate heading.

## 7. Choose operation mode

Use passive mode for predictable time-based movement, active mode for LDR-based tracking, and manual mode for inspection or fixed-angle testing.

## 8. Use the sudden variation filter

Enable the sudden variation filter when shadows, reflections, or clouds cause unstable LDR readings.

## 9. Record experiment data

Use **Start Recording** before a calibration scan or test. The interface stores parsed angle, irradiance, and compass data. Use **Stop Recording and Save CSV** to export the dataset.

## 10. Field installation checklist

Check mechanical freedom, servo limits, external power stability, common ground, RTC time, symmetric LDR installation, compass interference, magnetic declination, cable strain relief, and outdoor protection.

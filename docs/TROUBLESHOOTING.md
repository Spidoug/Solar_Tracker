# Troubleshooting

## Arduino does not appear in the web app

Confirm the USB cable supports data, the Arduino driver is installed, the Arduino Serial Monitor is closed, the browser supports Web Serial, and the USB cable is reconnected if needed.

## Web Serial connection opens but no data appears

Confirm both firmware and web app use 9600 baud. Press reset on the Arduino after connecting and check whether another program is using the serial port.

## Servo jitters or resets the Arduino

Use an external power supply for the servo, connect grounds together, add decoupling capacitance if needed, and check for mechanical friction or overload.

## Servo moves in the wrong direction

Swap the physical east/west LDR positions or invert the control logic in firmware. Confirm A0 is connected to the west LDR and A1 to the east LDR.

## Tracker keeps moving too often

Enable the sudden variation filter, increase tolerance in firmware, improve the LDR divider, or add stronger averaging/filtering.

## RTC not found

Check SDA/SCL wiring, module power, ground, and I2C pull-ups.

## Compass not found or heading is unstable

Check SDA/SCL wiring, keep the compass away from motors and magnets, review compass initialization order in `setup()`, and recalibrate by rotating the sensor.

## Irradiance data is incorrect

Confirm the sensor output is within 0–5 V and adjust the conversion formula if the sensor scale is not 0–1000 W/m².

## CSV file is empty

Start recording before running the test and confirm the web app is receiving serial lines containing angle, irradiance, and orientation labels.

## 3D parts do not fit

Verify printer scale, units, servo dimensions, and print tolerances. Reinforce parts if the panel or mount is heavier than the prototype design.

# E.L.M.S Emergency Locator Response System

## Project Brief
This project is an interactive emergency signaling device utilizing the M5 Stack and an IMU sensor. When an extreme tilt or impact is detected, the device activates an immediate alert mode. If the emergency mode persists beyond 10 seconds without user intervention, the device automatically transitions into an SOS signaling state, emitting the universally recognized Morse code signal (three quick flashes, three long flashes, three quick flashes) in bright green at maximum brightness. This function serves as a visual distress beacon, providing assistance in emergency or dangerous situations.

## Project Outcome
[Prototype Video]https://youtube.com/shorts/HaKyc20MGzE

## Flow State and Sketches
![Flowstate.png]https://github.com/Jerrycai4321/E.L.M.S/blob/main/Flowstate.png?raw=true
## material used

* Protopie
* ESP-32 M5 Stack
* IMU sensors
* LED Stripes

##Firmware
The IMU unit continually measures acceleration values. When significant Y-axis acceleration (beyond ±3.0 units) is detected, emergency mode is activated:
```
imu_val = imu.get_accelerometer()
imu_x, imu_y, imu_z = imu_val[0], imu_val[1], imu_val[2]

if abs(imu_y) > 3.0:
    emergency_mode = True
 
```

In emergency mode, for the first 10 seconds, the LED flashes red to indicate immediate alert status:
```
if (current_time - last_toggle_time) >= 0.5:
    blink_state = not blink_state
    last_toggle_time = current_time
if blink_state:
    set_leds(255, 0, 0)  # Red
else:
    set_leds(0, 0, 0)    # Off
 
```

After 10 seconds without a reset, the device shifts to an SOS mode, displaying Morse code "SOS" with bright green LEDs:
```
# Morse code SOS (Three quick flashes - Three long flashes - Three quick flashes)
sos_pattern = [
    (True, 0.1), (False, 0.1),  # S: quick flash
    (True, 0.1), (False, 0.1),
    (True, 0.1), (False, 0.3),  # end of S

    (True, 0.5), (False, 0.1),  # O: long flash
    (True, 0.5), (False, 0.1),
    (True, 0.5), (False, 0.3),  # end of O

    (True, 0.1), (False, 0.1),  # S: quick flash
    (True, 0.1), (False, 0.1),
    (True, 0.1), (False, 1.0)   # end of SOS cycle
]

# LED Bright Green (max brightness) for SOS mode
if sos_pattern[sos_index][0]:
    set_leds(0, 255, 0)  # Bright Green
else:
    set_leds(0, 0, 0)    # Off
```

Emergency mode can be manually reset by pressing a dedicated button connected with a pull-up resistor on Pin 41:
```
button = Pin(41, Pin.IN, Pin.PULL_UP)

if button.value() == 0:  # Button pressed (low signal)
    emergency_mode = False
    set_leds(0, 0, 0)  # Turn off LEDs

```

# UglyBot for MicroBlocks

Easy blocks for the **Myndsets UglyBot** two-wheel rover, built on an **ESP32**, a **TB6612FNG** motor driver and an **HC-SR04** ultrasonic sensor.

## Open it in one click

**[Open UglyBot in MicroBlocks](https://microblocks.fun/run/microblocks.html#project=https://raw.githubusercontent.com/levidontackerman/uglybot-microblocks/main/UglyBot.ubp)**

The link opens MicroBlocks in Chrome or Edge with the UglyBot blocks already loaded. There's nothing to install. Plug in the ESP32 and click **Connect → connect (USB)**.

## Wiring (ESP32, GPIO numbers)

This matches the current Myndsets *Two-Wheel Rover* pin map (PWMB and STBY moved in the last rewiring).

| Part | Pin | GPIO |
|---|---|---|
| Left motor (A, AO1/AO2) | PWMA | 25 |
| | AIN1 | 26 |
| | AIN2 | 27 |
| Right motor (B, BO1/BO2) | PWMB | 14 |
| | BIN1 | 18 |
| | BIN2 | 19 |
| Motor driver | STBY | 13 |
| Ultrasonic (HC-SR04) | TRIG | 22 |
| | ECHO | 34, through the 1 kΩ / 2 kΩ divider |

- **Power:** two 18650 cells in series feed an LM2596 buck module set to 5.0 V, which powers the driver's VM pin. All grounds are joined.
- **STBY** is switched HIGH by the library the first time any UglyBot block runs. Until then the driver stays asleep and the motors are off.
- **ECHO** is a 5 V signal. It must go through the divider before it reaches GPIO 34.
- MicroBlocks drives GPIO 25 and 26 as DAC outputs, so they can't do PWM. The library handles this for the left motor automatically: it holds PWMA (GPIO 25) HIGH and sends the left speed as PWM on AIN2 (GPIO 27).

## Blocks

### Drive

| Block | What it does |
|---|---|
| go [forward ▾] at speed 50 % | Keeps moving: forward, backward, turn left/right, spin left/right |
| go [forward ▾] at speed 50 % for 1000 ms | Moves for a time, then brakes (1000 ms = 1 second) |
| stop UglyBot | Brakes both wheels |
| go straight at full speed | Both wheels forward at 100 %, until a stop block runs |
| reverse at full speed | Both wheels backward at 100 % |
| turn left at full speed | Right wheel at 100 %, left wheel braked |
| turn right at full speed | Left wheel at 100 %, right wheel braked |
| set left wheel 50 % right wheel 50 % | Each wheel -100 to 100 (negative = backward, 0 = brake) |
| set [left ▾] wheel speed to 50 % | One wheel, or both |
| stop UglyBot with [brake ▾] | Brake, or coast to a stop |

### Web joystick

| Block | What it does |
|---|---|
| start web joystick WiFi name `UglyBot` password `12345678` | Turns UglyBot into a WiFi hotspot and serves a web page with a joystick and a STOP button |

How to use it:

1. Put the block under **when started** (it keeps running, so it goes last in the script).
2. On a phone, join the WiFi name you chose. The password needs 8 or more characters.
3. Open **http://192.168.4.1** in the phone's browser.
4. Drag the stick to drive. Let go, or press **STOP**, to brake.

If the phone stops talking to UglyBot for 1.5 seconds (out of range, screen locked), UglyBot stops by itself. In a class, give every robot its own WiFi name. Some phones warn that the WiFi has no internet; choose to stay connected.

### Ultrasonic

| Block | What it does |
|---|---|
| UglyBot distance (cm) | Distance to the nearest thing in front. 400 means nothing in range |
| obstacle closer than 15 cm | True or false, so it fits inside **if** and **when** blocks |

### Setup

| Block | What it does |
|---|---|
| flip [left ▾] wheel direction | Fixes a wheel that turns the wrong way |
| set wheel balance left 100 % right 100 % | Lower the faster wheel if UglyBot drifts |
| set UglyBot pins … standby … | Only for a robot wired differently. Use -1 for standby if STBY is wired straight to VCC |
| set ultrasonic pins trig 22 echo 34 | Only for a robot wired differently |

## Files

- **UglyBot.ubp**: starter project with the library inside (this is what the link opens)
- **UglyBot.ubl**: the library on its own (drag it into MicroBlocks, or use Add Library)

MIT License · Myndsets

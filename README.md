# Triko for MicroBlocks

Easy blocks for the **Myndsets Triko** two-wheel rover, built on an **ESP32**, a **TB6612FNG** motor driver and an **HC-SR04** ultrasonic sensor.

## Open it in one click

**[Open Triko in MicroBlocks](https://microblocks.fun/run/microblocks.html#project=https://raw.githubusercontent.com/levidontackerman/triko-microblocks/main/Triko.ubp)**

The link opens MicroBlocks in Chrome or Edge with the Triko blocks already loaded. There's nothing to install. Plug in the ESP32 and click **Connect → connect (USB)**.

## Wiring (ESP32, GPIO numbers)

Left and right are as seen from behind, with the ultrasonic sensor at the front. On Triko the motor on AO1/AO2 sits on the right side, so the library treats channel B as the left wheel and channel A as the right wheel.

| Part | Pin | GPIO |
|---|---|---|
| Left motor (channel B, BO1/BO2) | PWMB | 14 |
| | BIN1 | 18 |
| | BIN2 | 19 |
| Right motor (channel A, AO1/AO2) | PWMA | 25 |
| | AIN1 | 26 |
| | AIN2 | 27 |
| Motor driver | STBY | 13 |
| Ultrasonic (HC-SR04) | TRIG | 22 |
| | ECHO | 34, through the 1 kΩ / 2 kΩ divider |

- **Power:** two 18650 cells in series go to a two-cell charging module, then through the ON/OFF switch to an LM2596 step-down module set to 5.0 volts. That 5 volt rail powers the motor driver (VM), the ESP32 (VIN) and the ultrasonic sensor. All grounds are joined.
- **STBY** is switched HIGH by the library the first time any Triko block runs. Until then the driver stays asleep and the motors are off.
- **ECHO** is a 5 volt signal. It must go through the divider before it reaches GPIO 34.
- MicroBlocks drives GPIO 25 and 26 as DAC outputs, so they can't do PWM. The library handles this for the right motor automatically: it holds PWMA (GPIO 25) HIGH and sends the left speed as PWM on AIN2 (GPIO 27).

## Blocks

In the same order as the Triko palette.

### Moving

| Block | What it does |
|---|---|
| move [forward ▾] at speed 50 % for 1000 ms | Moves for a time, then brakes (1000 ms = 1 second) |
| move [forward ▾] at speed 50 % | Keeps moving: forward, backward, turn left/right, spin left/right |
| stop Triko | Brakes both wheels |

### Full speed

| Block | What it does |
|---|---|
| move forward at full speed | Both wheels forward at 100 %, until a stop block runs |
| move backward at full speed | Both wheels backward at 100 % |
| move left at full speed | Right wheel at 100 %, left wheel braked |
| move right at full speed | Left wheel at 100 %, right wheel braked |

### Wheels

| Block | What it does |
|---|---|
| set left wheel 50 % right wheel 50 % | Each wheel -100 to 100 (negative = backward, 0 = brake) |
| set [left ▾] wheel speed to 50 % | One wheel, or both |
| stop Triko with [brake ▾] | Brake, or coast to a stop |
| set wheel balance left 100 % right 100 % | Lower the faster wheel if Triko drifts |

### Ultrasonic

| Block | What it does |
|---|---|
| Triko distance (cm) | Distance to the nearest thing in front. 400 means nothing in range |
| obstacle closer than 15 cm | True or false, so it fits inside **if** and **when** blocks |

### Web joystick

| Block | What it does |
|---|---|
| start web joystick WiFi name `Triko` password `12345678` | Turns Triko into a WiFi hotspot and serves a web page with a joystick and a STOP button |

How to use it:

1. Put the block under **when started** (it keeps running, so it goes last in the script).
2. On a phone, join the WiFi name you chose. The password needs 8 or more characters.
3. Open **http://192.168.4.1** in the phone's browser.
4. Set the two wheel speed sliders, then drag the stick to drive. Let go, or press **STOP**, to brake.

The page also shows the ultrasonic **distance ahead** in centimetres, updated a few times a second. It turns red when something is closer than 15 cm, and shows "nothing in front" when nothing is in range.

Under the STOP button there are **left wheel speed** and **right wheel speed** sliders (0 to 100 %). They set how fast each wheel goes when you drive with the joystick, so you can tune each motor on its own. The sliders don't move Triko by themselves, and the joystick does nothing while both are at 0 (the page says "set the wheel speeds first"). **STOP** stops the wheels but keeps the speeds you set.

Below the sliders there are two tick boxes for when a wheel turns the wrong way (TT motors are often wired the opposite way round):

- **invert left motor** swaps the left motor's two direction pins in the code (BIN1 on GPIO 18 and BIN2 on GPIO 19).
- **invert right motor** swaps the right motor's two direction pins in the code (AIN1 on GPIO 26 and AIN2 on GPIO 27).

Triko stops for a moment when you tick or untick a box. The swap is only in the code, not the wiring, and it resets when Triko restarts.

If the phone stops talking to Triko for 1.5 seconds (out of range, screen locked), Triko stops by itself. In a class, give every robot its own WiFi name.

### Setup

| Block | What it does |
|---|---|
| flip [left ▾] wheel direction | Fixes a wheel that turns the wrong way |

The pin setup blocks are hidden. They are only needed for a robot wired differently from the table above.

## Files

- **Triko.ubp**: starter project with the library inside (this is what the link opens)
- **Triko.ubl**: the library on its own (drag it into MicroBlocks, or use Add Library)
- **Triko-ultrasonic-test.ubp**: shows the ultrasonic distance and lights the ESP32's blue LED when something is closer than 15 cm

MIT License · Myndsets

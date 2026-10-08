# Week 4 — PID Line Following Competition

PID/PD-based line following for a physical TRIK robot using two reflectance sensors, with sensor balancing and a special recovery mode for sharp 180° corners.

## Setup

- A4 — left reflectance sensor
- A3 — right reflectance sensor
- M3 — left motor
- M4 — right motor

## Sensor Calibration

At startup, the initial sensor readings are stored as reference values:

```text
left_reference = A4
right_reference = A3
```

The A3 sensor is multiplied by `2` to compensate for the difference between the two physical sensor readings:

```text
error = (A4 - left_reference) - 2 * (A3 - right_reference)
```

## Controller

The final controller uses proportional and derivative terms:

```text
P = Kp * error
D = Kd * (error - previous_error) / 10

u = P + D
u = limit(u, -45, 45)

M3 = speed + u
M4 = speed - u
```

The integral gain is set to zero in the final version, so the controller effectively works as a PD controller.

## Final Parameters

- Kp = 0.85
- Ki = 0
- Kd = 3
- Normal speed = 35
- Maximum normal correction = ±45
- Control interval = 10 ms
- Sharp-corner error threshold = 35

## 180° Corner Handling

The robot also includes a special mode for very sharp turns.

If the absolute error remains above `35` for more than 15 consecutive control cycles, the robot treats it as a hairpin/180° corner.

During this mode:

```text
speed = 18
u = 48 * sign(u)
```

This makes the robot slow down and turn more aggressively until it can follow the line through the sharp corner.

## Result

The final version follows the line reliably on straight sections and normal curves while compensating for the unequal A3/A4 sensor readings.

For sharp 180° turns, it automatically switches to a slower and stronger turning mode, helping the robot stay on the line instead of driving past the corner.

🎥 Demo: https://youtube.com/shorts/pKnfklFRIfY?feature=share

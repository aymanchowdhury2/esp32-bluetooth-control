Here is the complete, master wiring guide for your **StreetWatch Neo** project. It combines your custom direct-PWM wheel steering logic (no `RX2`/`TX2`) and all accessory pins into one clean, final reference sheet.

---

## 🔌 Master Pin Connection Diagram

```
                 +-------------------+
                 |       ESP32       |
                 |    DEVKIT V1      |
                 |                   |
 [LF Motor IN1] <| D4             D2 |> [Headlights Relay]
 [RF Motor IN2] <| D5            D25 |> [Garbage Hatch Relay]
 [LB Motor IN3] <| D18           D26 |> [Brush Motor Relay]
 [RB Motor IN4] <| D19           D27 |> [Water Spray Pump]
                 |               D32 |> [Rig Lift Actuator UP]
                 |               D33 |> [Rig Lift Actuator DOWN]
                 |               D13 |> [5V Active Horn Buzzer]
                 |                   |
                 +-------------------+

```

---

## 🚗 Part 1: Driving & Steering Connections (Direct PWM)

These 4 pins connect directly to your H-Bridge Motor Driver inputs (`IN1` through `IN4`). The ESP32 sends a variable PWM voltage ($0\text{V}$ to $3.3\text{V}$) to these pins based on your app's **Speed Slider** position.

| App Button | Bluetooth Code Sent | Active ESP32 Pins | Physical Action on the Vehicle |
| --- | --- | --- | --- |
| **▲ Forward** | `DIR:F,[speed]` | **D4** & **D5** | **Both Front Wheels Run:** Left Front (`D4`) and Right Front (`D5`) receive slider PWM power. *Back wheels stay off.* |
| **▼ Backward** | `DIR:B,[speed]` | **D18** & **D19** | **Both Back Wheels Run:** Left Back (`D18`) and Right Back (`D19`) receive slider PWM power. *Front wheels stay off.* |
| **◀ Left** | `DIR:L,[speed]` | **D5** & **D18** | **Diagonal Turn Left:** Right Front wheel (`D5`) and Left Back wheel (`D18`) run together to swing the chassis left. |
| **▶ Right** | `DIR:R,[speed]` | **D4** & **D19** | **Diagonal Turn Right:** Left Front wheel (`D4`) and Right Back wheel (`D19`) run together to swing the chassis right. |
| **↖ Fwd Left** | `DIR:FL,[speed]` | **D4**, **D5**, **D18** | Moves forward while blending extra power to the right side to curve left. |
| **↗ Fwd Right** | `DIR:FR,[speed]` | **D4**, **D5**, **D19** | Moves forward while blending extra power to the left side to curve right. |
| **■ Stop** | `DIR:S,0` | **All 4 Pins** | **Instant Safe Stop:** All 4 pins (`D4`, `D5`, `D18`, `D19`) instantly drop to $0\text{V}$. |

---

## 🛠️ Part 2: Accessory Toggles (Digital ON / OFF)

These pins connect to the signal pins of your relays, transistors, or electronic switches. They change states instantly between completely ON ($3.3\text{V}$) or completely OFF ($0\text{V}$).

| App Screen Switch | Bluetooth Code | Mapped ESP32 Pin | Logic State & Behavior |
| --- | --- | --- | --- |
| **LED Toggle ON** <br>

<br> **LED Toggle OFF** | `LED:1` <br>

<br> `LED:0` | **D2** (GPIO 2) | **HIGH ($3.3\text{V}$):** Front headlights turn ON. <br>

<br> **LOW ($0\text{V}$):** Headlights turn OFF. |
| **Hatch Toggle ON** <br>

<br> **Hatch Toggle OFF** | `GARBAGE:1` <br>

<br> `GARBAGE:0` | **D25** (GPIO 25) | **HIGH ($3.3\text{V}$):** Garbage collector hatch opens. <br>

<br> **LOW ($0\text{V}$):** Hatch closes. |
| **Brush Toggle ON** <br>

<br> **Brush Toggle OFF** | `CLEAN:1` <br>

<br> `CLEAN:0` | **D26** (GPIO 26) | **HIGH ($3.3\text{V}$):** Cleaning brush motor spins. <br>

<br> **LOW ($0\text{V}$):** Brush stops spinning. |
| **Spray Toggle ON** <br>

<br> **Spray Toggle OFF** | `WATER:1` <br>

<br> `WATER:0` | **D27** (GPIO 27) | **HIGH ($3.3\text{V}$):** Water pump spray activates. <br>

<br> **LOW ($0\text{V}$):** Water spray shuts off. |
| **Combo Clean ON** <br>

<br>

<br> **Combo Clean OFF** | `W_CLEAN:1` <br>

<br>

<br> `W_CLEAN:0` | **D26** & **D27** <br>

<br> *(Dual Execution)* | **HIGH ($3.3\text{V}$):** **Both** Brush and Water Spray pins turn on at the exact same time. <br>

<br> **LOW ($0\text{V}$):** Both turn off together. |

---

## 🔊 Part 3: Momentary Action Buttons (Press-and-Hold)

These pins are highly responsive and stay active **only while your finger is physically holding down the button** in your Android app. The millisecond you release your finger, the app transmits an execution stop command, dropping the pins back to neutral safety.

| App Screen Button | Touch Action | Bluetooth Code | Mapped ESP32 Pin | Logic State & Behavior |
| --- | --- | --- | --- | --- |
| **Rig ▲** | Press & Hold <br>

<br> Release Finger | `BRUSH:UP` <br>

<br> `BRUSH:OFF` | **D32** (GPIO 32) | **HIGH ($3.3\text{V}$):** Activates actuator to raise the cleaning rig. <br>

<br> **LOW ($0\text{V}$):** Rig stops moving. |
| **Rig ▼** | Press & Hold <br>

<br> Release Finger | `BRUSH:DN` <br>

<br> `BRUSH:OFF` | **D33** (GPIO 33) | **HIGH ($3.3\text{V}$):** Activates actuator to lower the cleaning rig. <br>

<br> **LOW ($0\text{V}$):** Rig stops moving. |
| **HORN 🔊** | Press & Hold <br>

<br> Release Finger | `HRN:1` <br>

<br> `HRN:0` | **D13** (GPIO 13) | **HIGH ($3.3\text{V}$):** Sounds the 5V active buzzer. <br>

<br> **LOW ($0\text{V}$):** Buzzer goes completely silent. |

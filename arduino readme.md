Converting this setup to an **Arduino Uno** requires a few changes because the Uno handles Bluetooth and PWM differently than the ESP32:

1. **Bluetooth Connection:** The Arduino Uno doesn't have built-in Bluetooth. You will need to connect an external module like an **HC-05 or HC-06** to pins **D11 (RX)** and **D12 (TX)** using a Software Serial connection.
2. **PWM Limitations:** The Uno only has 6 hardware PWM pins (D3, D5, D6, D9, D10, D11). Because D11 is busy handling Bluetooth data, we will use **D3, D5, D6, and D9** for your direct-PWM driving wheels.

---

## 🔌 Arduino Uno Master Pin Connection Blueprint

Here is how to wire your accessories, your motor driver, and your Bluetooth module directly to the Arduino Uno board.

```
                    +--------------------+
                    |    ARDUINO UNO     |
                    +--------------------+
 [LF Motor IN1] <---| D3 (PWM)   (PWM) D5|---> [RF Motor IN2]
[Relay Spray]   <---| D4         (PWM) D6|---> [LB Motor IN3]
[Relay Brush]   <---| D7               D8|---> [Relay Hatch]
 [RB Motor IN4] <---| D9 (PWM)        D10|---> [Relay LED]
   [HC-05 TXD]  --->| D11 (Rx)        D12|---> [HC-05 RXD]
[Rig Lift UP]   <---| D13             A0 |---> [5V Active Horn]
                    +--------------------+

```

### Complete Pin Assignment Table

| Uno Pin Label | Pin Function | Target Component Connection | Expected App Action |
| --- | --- | --- | --- |
| **D3** | **PWM Output** | Motor Driver Input 1 (`IN1` - Left Front) | `DIR:F`, `DIR:R` |
| **D5** | **PWM Output** | Motor Driver Input 2 (`IN2` - Right Front) | `DIR:F`, `DIR:L` |
| **D6** | **PWM Output** | Motor Driver Input 3 (`IN3` - Left Back) | `DIR:B`, `DIR:L` |
| **D9** | **PWM Output** | Motor Driver Input 4 (`IN4` - Right Back) | `DIR:B`, `DIR:R` |
| **D10** | Digital Output | Headlight Relay Switch (`LED`) | `LED:1` / `LED:0` |
| **D8** | Digital Output | Garbage Hatch Relay Actuator | `GARBAGE:1` / `GARBAGE:0` |
| **D7** | Digital Output | Cleaning Brush Motor Relay | `CLEAN:1` / `W_CLEAN:1` |
| **D4** | Digital Output | Water Spray Pump Module | `WATER:1` / `W_CLEAN:1` |
| **D13** | Digital Output | Structural Rig Lift Actuator **Up** | `BRUSH:UP` |
| **A1** (Digital 15) | Digital Output | Structural Rig Lift Actuator **Down** | `BRUSH:DN` |
| **A0** (Digital 14) | Digital Output | 5V Active Horn Audible Buzzer | `HRN:1` |
| **D11** | Software RX | Connect to HC-05 / HC-06 Bluetooth **TXD Pin** | Wireless Data Intake |
| **D12** | Software TX | Connect to HC-05 / HC-06 Bluetooth **RXD Pin** | Wireless Handshake |

---


### 🕹️ Button to Pin Mapping Reference for Arduino Uno

| App Interface Interface Component | Code Parsing Action | Active Physical Arduino Uno Pin Output |
| --- | --- | --- |
| **▲ Forward** | `DIR:F,[speed]` | **D3** and **D5** (Analog Speed Voltage) |
| **▼ Backward** | `DIR:B,[speed]` | **D6** and **D9** (Analog Speed Voltage) |
| **◀ Left** | `DIR:L,[speed]` | **D5** and **D6** (Analog Speed Voltage) |
| **▶ Right** | `DIR:R,[speed]` | **D3** and **D9** (Analog Speed Voltage) |
| **■ Stop Button** | `DIR:S,0` | All four motor pins **D3, D5, D6, D9 drop to 0V** |
| **LED Toggle** | `LED:1` / `LED:0` | **D10** (ON: 5V / OFF: 0V) |
| **Hatch Toggle** | `GARBAGE:1` / `GARBAGE:0` | **D8** (ON: 5V / OFF: 0V) |
| **Brush Toggle** | `CLEAN:1` / `CLEAN:0` | **D7** (ON: 5V / OFF: 0V) |
| **Spray Toggle** | `WATER:1` / `WATER:0` | **D4** (ON: 5V / OFF: 0V) |
| **Rig Lift ▲** | `BRUSH:UP` / `BRUSH:OFF` | **D13** (5V only while pressed down) |
| **Rig Lift ▼** | `BRUSH:DN` / `BRUSH:OFF` | **A1** (5V only while pressed down) |
| **HORN Button 🔊** | `HRN:1` / `HRN:0` | **A0** (5V only while pressed down) |

Here is the completely revised, master package for your **Arduino Uno** setup.

Since you requested **not to use the Analog pins (A0–A5)** for your accessories and confirmed you want to use the hardware serial pins **Pin 0 (RX)** and **Pin 1 (TX)** for your Bluetooth module, we have shifted the entire layout.

### ⚠️ Crucial Step for Uploading Code with Pins 0 & 1:

Because the Arduino Uno uses Pins 0 and 1 to talk to your computer over the USB cable, **you must temporarily unplug the Bluetooth module's RX and TX wires from Pin 0 and Pin 1 while clicking "Upload" in the Arduino IDE.** Once the upload is complete (`Done uploading`), plug the wires back in, and it will work perfectly!

---

## 🔌 Arduino Uno Master Connection Blueprint

Every single driving pin uses an official Hardware PWM pin (`D3`, `D5`, `D6`, `D9`) to accept direct variable speeds from your app's slider. All accessories use regular Digital pins.

```
                    +--------------------+
                    |    ARDUINO UNO     |
                    +--------------------+
   [HC-05 TXD]  --->| D0 (RX)        D12 |---> [Relay Spray]
   [HC-05 RXD]  <---| D1 (TX)        D11 |---> [Relay Brush]
[Headlights Relay] <| D2         (PWM) D10 |---> [Relay Hatch]
 [LF Motor IN1] <---| D3 (PWM)   (PWM) D9 |---> [RB Motor IN4]
 [Rig Lift UP]  <---| D4               D8 |---> [Rig Lift DOWN]
 [RF Motor IN2] <---| D5 (PWM)         D7 |---> [5V Active Horn]
 [LB Motor IN3] <---| D6 (PWM)         -- |---> (Analog Pins Free!)
                    +--------------------+

```

### Complete Pin Assignment Table

| Uno Board Pin | Pin Function Type | Connected Component Destination | Expected App Command |
| --- | --- | --- | --- |
| **D0 (RX)** | Hardware Serial Input | Connect to HC-05 / HC-06 Bluetooth **TXD** | Wireless Communication |
| **D1 (TX)** | Hardware Serial Output | Connect to HC-05 / HC-06 Bluetooth **RXD** | Wireless Communication |
| **D3** | **PWM Output** | Motor Driver Input 1 (`IN1` - Left Front Wheel) | `DIR:F`, `DIR:R` |
| **D5** | **PWM Output** | Motor Driver Input 2 (`IN2` - Right Front Wheel) | `DIR:F`, `DIR:L` |
| **D6** | **PWM Output** | Motor Driver Input 3 (`IN3` - Left Back Wheel) | `DIR:B`, `DIR:L` |
| **D9** | **PWM Output** | Motor Driver Input 4 (`IN4` - Right Back Wheel) | `DIR:B`, `DIR:R` |
| **D2** | Digital Output | Front Headlights Switching Relay (`LED`) | `LED:1` / `LED:0` |
| **D10** | Digital Output | Garbage Hatch Relay Actuator | `GARBAGE:1` / `GARBAGE:0` |
| **D11** | Digital Output | Cleaning Brush Motor Relay | `CLEAN:1` / `W_CLEAN:1` |
| **D12** | Digital Output | Water Spray Pump Module Relay | `WATER:1` / `W_CLEAN:1` |
| **D4** | Digital Output | Cleaning Rig Lift Mechanism **UP** | `BRUSH:UP` |
| **D8** | Digital Output | Cleaning Rig Lift Mechanism **DOWN** | `BRUSH:DN` |
| **D7** | Digital Output | 5V Active Horn Audible Buzzer | `HRN:1` |

---


```

---

## 🕹️ Which Button Triggers What Pin?

### 🚗 Movement Controls (Variable PWM Voltage via Slider Speed)

| App Layout Button | Bluetooth String | Activated Arduino Pins | Physical Vehicle Action |
| --- | --- | --- | --- |
| **▲ Forward** | `DIR:F,[speed]` | **D3** & **D5** | Runs Front Left and Front Right wheels together. |
| **▼ Backward** | `DIR:B,[speed]` | **D6** & **D9** | Runs Back Left and Back Right wheels together. |
| **◀ Left** | `DIR:L,[speed]` | **D5** & **D6** | Runs Front Right and Back Left wheels together to turn left. |
| **▶ Right** | `DIR:R,[speed]` | **D3** & **D9** | Runs Front Left and Back Right wheels together to turn right. |
| **■ Stop / Release** | `DIR:S,0` | **D3, D5, D6, D9** | Drops all 4 drive pins to **0V** for a dead stop. |

### 🛠️ Utilities Controls (Instant Digital 5V ON / 0V OFF)

| App Switch | Bluetooth String | Arduino Pin | What Happens on the Vehicle Chassis |
| --- | --- | --- | --- |
| **LED Toggle** | `LED:1` / `LED:0` | **D2** | **ON:** Pin goes 5V (Headlights) | **OFF:** Pin goes 0V |
| **Hatch Toggle** | `GARBAGE:1` / `GARBAGE:0` | **D10** | **ON:** Pin goes 5V (Hatch opens) | **OFF:** Pin goes 0V |
| **Brush Toggle** | `CLEAN:1` / `CLEAN:0` | **D11** | **ON:** Pin goes 5V (Brushes spin) | **OFF:** Pin goes 0V |
| **Spray Toggle** | `WATER:1` / `WATER:0` | **D12** | **ON:** Pin goes 5V (Water pump sprays) | **OFF:** Pin goes 0V |
| **Combo Clean** | `W_CLEAN:1` / `W_CLEAN:0` | **D11 & D12** | **ON:** Both Brush & Spray go 5V together | **OFF:** Both go 0V |

### 🔊 Momentary Actions (Active ONLY While Pressed)

| App Button | Handshake Code | Arduino Pin | What Happens on the Vehicle Chassis |
| --- | --- | --- | --- |
| **Rig Lift ▲** | `BRUSH:UP` / `BRUSH:OFF` | **D4** | Pin goes 5V to raise the cleaning rig. Drops to 0V when released. |
| **Rig Lift ▼** | `BRUSH:DN` / `BRUSH:OFF` | **D8** | Pin goes 5V to lower the cleaning rig. Drops to 0V when released. |
| **HORN 🔊** | `HRN:1` / `HRN:0` | **D7** | Pin goes 5V to fire the audible alarm. Drops to 0V when released. |

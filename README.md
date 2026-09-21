# ⚡ ESP32 Multi-Power Controller Board


The **ESP32 Multi-Power Controller** is a versatile embedded motherboard designed by **CircuitForge PCB**. Form-factor matched to the size of a standard 2.3" seven-segment display, this PCB integrates high-efficiency buck regulation, bidirectional level shifting, display drivers, and sensor breakouts into a single compact layout.

---

## ✨ Key Features

* **38-Pin ESP32 Footprint:** Universal support for standard ESP32 38-pin DevKit modules (WROOM-32 / WROVER).
* **Dual & Triple Power Flexibility:** Integrated slots for LM2596 and Step-down Mini360 modules to supply clean 3.3V, 5V, and high-voltage rails.
* **High-Speed Level Shifting:** Dual **74HCT125D** quad buffer ICs (U6 & U7) for 3.3V to 5V logic signal translation (100Ω series protection on 5V GPIO outputs)[cite: 8].
* **7-Segment Display Header:** Dedicated 8-pin IDC/header output (`SER_OUT`, `CLOCK`, `LATCH`, `BRIGHT`) driven via 5V level-shifted signals[cite: 8].
* **Peripherals & Sensor Interfaces:**
  * Dedicated **I2C Header** & **OLED Connector** (SDA: GPIO21, SCL: GPIO19 with 4.7kΩ pull-up resistors)[cite: 8].
  * **LDR Light Sensor Breakout** with onboard voltage divider[cite: 8].
  * **2x User Pushbuttons** (GPIO34 & GPIO35)[cite: 8].
* **Ultra-Compact Form Factor:** Physical dimensions engineered to match a standard **2.3-inch seven-segment digit**.

---

## 🛠️ Technical Specifications

| Parameter | Description / Value |
| :--- | :--- |
| **Main Controller** | ESP32 38-Pin DevKit V1 Footprint[cite: 8] |
| **Power Modules** | LM2596 DC-DC & Mini360 Step-down[cite: 8] |
| **Logic Buffers** | 2x 74HCT125D Quad Level Shifter ICs[cite: 8] |
| **Display Support** | 0.96" / 1.3" I2C SSD1306 OLED Header[cite: 8] |
| **Expansion Headers** | 5V GPIOs (H5), 3V3 GPIOs (H8), 7-Seg Out (H1/P1), I2C (H3)[cite: 8] |
| **Board Dimensions** | Matched to 2.3" Seven-Segment Digit |
| **Design Software** | EasyEDA Pro[cite: 8] |

---

## 📐 Board Architecture & Exact Pinout

### ESP32 GPIO Pin Mapping

| ESP32 Pin | Schematic Label | Function / Destination | Logic Level |
| :--- | :--- | :--- | :--- |
| **GPIO 34** | `d34` | Push Button 1 (SW1) | 3.3V[cite: 8] |
| **GPIO 35** | `d35` | Push Button 2 (SW2) | 3.3V[cite: 8] |
| **GPIO 2** | `d2` | LDR Light Sensor Analog Input | 3.3V Max[cite: 8] |
| **GPIO 21** | `d21` / `SDA` | I2C Data (Header H3 & OLED U4) | 3.3V (4.7kΩ Pull-up)[cite: 8] |
| **GPIO 19** | `d19` / `SCL` | I2C Clock (Header H3 & OLED U4) | 3.3V (4.7kΩ Pull-up)[cite: 8] |
| **GPIO 23** | `d23` / `SER_OUT` | 7-Seg Data Out (U6 Level Shifter) | 5V Shifted Output[cite: 8] |
| **GPIO 18** | `d18` / `CLOCK` | 7-Seg Clock Out (U6 Level Shifter) | 5V Shifted Output[cite: 8] |
| **GPIO 5** | `d5` / `LATCH` | 7-Seg Latch Out (U6 Level Shifter) | 5V Shifted Output[cite: 8] |
| **GPIO 14** | `d14` / `BRIGHT` | 7-Seg Brightness Control (U6) | 5V Shifted Output[cite: 8] |
| **GPIO 12** | `d12` / `OUT-d12` | Extra 5V Output 1 (U7 Level Shifter) | 5V Shifted (100Ω)[cite: 8] |
| **GPIO 13** | `d13` / `OUT-d13` | Extra 5V Output 2 (U7 Level Shifter) | 5V Shifted (100Ω)[cite: 8] |
| **GPIO 25** | `d25` / `OUT-d25` | Extra 5V Output 3 (U7 Level Shifter) | 5V Shifted (100Ω)[cite: 8] |
| **GPIO 26** | `d26` / `OUT-d26` | Extra 5V Output 4 (U7 Level Shifter) | 5V Shifted (100Ω)[cite: 8] |
| **GPIO 32** | `d32` | 3V3 Direct Breakout (Header H8) | 3.3V Native[cite: 8] |
| **GPIO 33** | `d33` | 3V3 Direct Breakout (Header H8) | 3.3V Native[cite: 8] |
| **GPIO 4** | `d4` | 3V3 Direct Breakout (Header H8) | 3.3V Native[cite: 8] |

---

## 💻 Firmware Example (Arduino IDE)

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// Hardware Pin Definitions
#define PIN_BUTTON_1      34
#define PIN_BUTTON_2      35
#define PIN_LDR_SENSOR    2

#define PIN_I2C_SDA       21
#define PIN_I2C_SCL       19

// 7-Segment Shift Register Pins (Shifted to 5V via U6)
#define PIN_7SEG_SER      23
#define PIN_7SEG_CLOCK    18
#define PIN_7SEG_LATCH    5
#define PIN_7SEG_BRIGHT   14

// Extra 5V Shifted GPIO Outputs (Shifted via U7)
#define PIN_OUT_5V_1      12
#define PIN_OUT_5V_2      13
#define PIN_OUT_5V_3      25
#define PIN_OUT_5V_4      26

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

void setup() {
  Serial.begin(115200);

  pinMode(PIN_BUTTON_1, INPUT);
  pinMode(PIN_BUTTON_2, INPUT);

  pinMode(PIN_7SEG_SER, OUTPUT);
  pinMode(PIN_7SEG_CLOCK, OUTPUT);
  pinMode(PIN_7SEG_LATCH, OUTPUT);
  pinMode(PIN_7SEG_BRIGHT, OUTPUT);

  pinMode(PIN_OUT_5V_1, OUTPUT);
  pinMode(PIN_OUT_5V_2, OUTPUT);
  pinMode(PIN_OUT_5V_3, OUTPUT);
  pinMode(PIN_OUT_5V_4, OUTPUT);

  // Initialize I2C with hardware-defined SDA (21) & SCL (19)
  Wire.begin(PIN_I2C_SDA, PIN_I2C_SCL);

  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);
  }
  
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0,0);
  display.println(F("CircuitForge PCB"));
  display.println(F("ESP32 Controller"));
  display.display();
}

void loop() {
  int ldrValue = analogRead(PIN_LDR_SENSOR);
  bool btn1 = digitalRead(PIN_BUTTON_1);
  bool btn2 = digitalRead(PIN_BUTTON_2);

  delay(100);
}

# 🎯 ArduStab — 3-Axis Arduino Gimbal Stabilizer

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Arduino%20Nano-00979D?style=for-the-badge&logo=arduino&logoColor=white"/>
  <img src="https://img.shields.io/badge/Sensor-MPU6050-FF6B6B?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Language-C%2B%2B-blue?style=for-the-badge&logo=cplusplus&logoColor=white"/>
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge"/>
</p>

<p align="center">
  <i>A real-time, 3-axis self-stabilizing gimbal powered by Arduino Nano and MPU-6050 IMU — keeping your payload rock-steady, no matter the motion.</i>
</p>

---

## 📌 Table of Contents

- [About the Project](#-about-the-project)
- [How It Works](#-how-it-works)
- [Circuit Diagram](#-circuit-diagram)
- [Components Required](#-components-required)
- [Pin Configuration](#-pin-configuration)
- [Libraries Required](#-libraries-required)
- [Getting Started](#-getting-started)
- [Calibration](#-calibration)
- [Project Structure](#-project-structure)
- [Future Improvements](#-future-improvements)

---

## 🚀 About the Project

**ArduStab** is a compact, low-cost 3-axis gimbal stabilizer built using an **Arduino Nano** and the **MPU-6050 IMU (Inertial Measurement Unit)**. It uses real-time gyroscope and accelerometer data processed through the MPU's onboard **DMP (Digital Motion Processor)** to calculate orientation and automatically counter-correct three servo motors — preventing unwanted tilts and rotations.

Whether mounted on a drone, handheld rig, or RC vehicle, this gimbal keeps any attached payload (camera, sensor, etc.) stable and level in all three axes: **Yaw**, **Pitch**, and **Roll**.

---

## ⚙️ How It Works

```
MPU-6050 (IMU)
     │
     │  Raw motion data (via I2C @ 400kHz)
     ▼
Arduino Nano
     │
     │  DMP processes Quaternion → YPR angles
     │  Maps angles → Servo positions (0°–180°)
     ▼
3x Servo Motors
  ├── Servo 1 → YAW   (Pin 6)
  ├── Servo 2 → PITCH (Pin 3)
  └── Servo 3 → ROLL  (Pin 5)
```

1. **IMU Data Acquisition** — The MPU-6050 captures motion data and sends it to the Arduino via I2C.
2. **DMP Processing** — The onboard DMP converts raw sensor data into stable **Quaternion** values.
3. **Angle Extraction** — Quaternions are converted to **Yaw, Pitch, Roll** angles in degrees.
4. **Servo Control** — Angles are mapped to servo positions and written in real time to maintain stability.
5. **Auto-Calibration** — On startup, the system samples 300 readings to establish a stable baseline reference.

---

## 🔌 Circuit Diagram

```
Arduino Nano          MPU-6050
─────────────         ────────────
   A4 (SDA)  ◄──────►  SDA
   A5 (SCL)  ◄──────►  SCL
   D2        ◄──────►  INT
   5V        ─────────  VCC
   GND       ─────────  GND

Arduino Nano          Servo Motors
─────────────         ────────────
   D6        ────────►  Servo YAW   (Signal)
   D3        ────────►  Servo PITCH (Signal)
   D5        ────────►  Servo ROLL  (Signal)

Power Supply: 3.7V LiPo Battery (110mAh)
```

> 📷 See the full wiring diagram image included in this repository.

---

## 🛒 Components Required

| Component | Quantity | Description |
|---|---|---|
| Arduino Nano V3 | 1 | Microcontroller (ATmega328P) |
| MPU-6050 (ITG/MPU) | 1 | 6-axis IMU (Gyro + Accelerometer) |
| SG90 / MG90S Servo Motor | 3 | For Yaw, Pitch, and Roll axes |
| LiPo Battery 3.7V 110mAh | 1 | Compact power source |
| Jumper Wires | — | For connections |
| Gimbal Frame | 1 | 3D printed or off-the-shelf |

---

## 📍 Pin Configuration

| Arduino Pin | Connected To | Purpose |
|---|---|---|
| A4 (SDA) | MPU-6050 SDA | I2C Data Line |
| A5 (SCL) | MPU-6050 SCL | I2C Clock Line |
| D2 | MPU-6050 INT | Interrupt Signal |
| D3 | Servo PITCH | PWM Control |
| D5 | Servo ROLL | PWM Control |
| D6 | Servo YAW | PWM Control |
| 5V | MPU-6050 VCC | Power |
| GND | Common Ground | Ground |

---

## 📦 Libraries Required

Install these libraries via the Arduino Library Manager or manually:

- [`I2Cdev`](https://github.com/jrowberg/i2cdevlib) — I2C device abstraction layer
- [`MPU6050_6Axis_MotionApps20`](https://github.com/jrowberg/i2cdevlib/tree/master/Arduino/MPU6050) — DMP-based MPU6050 driver
- `Wire` — Built-in Arduino I2C library
- `Servo` — Built-in Arduino servo control library

---

## 🏁 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/ArduStab.git
cd ArduStab
```

### 2. Install Libraries

In Arduino IDE:
> `Sketch` → `Include Library` → `Manage Libraries`

Search and install:
- `I2Cdev`
- `MPU6050` by Electronic Cats or jrowberg

### 3. Upload the Code

- Connect your Arduino Nano via USB
- Select **Board:** `Arduino Nano`
- Select correct **Port**
- Click **Upload**

### 4. Power Up

Connect the LiPo battery and mount the gimbal. The system will auto-calibrate on startup (first 300 readings).

---

## 🎛️ Calibration

The MPU-6050 requires individual offset tuning for stable results. Default offsets in the code:

```cpp
mpu.setXGyroOffset(17);
mpu.setYGyroOffset(-69);
mpu.setZGyroOffset(80);
mpu.setZAccelOffset(1450);
```

> ⚠️ **These values are hardware-specific.** You must find your own offsets using the [MPU6050 Calibration Sketch](https://github.com/jrowberg/i2cdevlib/blob/master/Arduino/MPU6050/examples/MPU6050_raw/MPU6050_raw.ino).

You may also need to adjust servo directions:

```cpp
int yawValue   = map(ypr[0], -90, 90, 0, 180);
int pitchValue = map(ypr[1], -90, 90, 180, 0);  // Reversed direction
int rollValue  = map(ypr[2], -90, 90, 0, 180);
```

Swap `0` and `180` in the `map()` function to reverse a servo's direction if needed.

---

## 📁 Project Structure

```
ArduStab/
│
├── Arduino_Gimbal_Stable.ino   # Main Arduino sketch
├── circuit_diagram.png         # Wiring diagram
└── README.md                   # Project documentation
```

---

## 🔮 Future Improvements

- [ ] Add Bluetooth / Wi-Fi remote control (ESP8266 / HC-05)
- [ ] Implement PID controller for smoother corrections
- [ ] Add a Follow Mode (semi-stable tracking)
- [ ] Enclose in a 3D-printed gimbal housing
- [ ] Support for brushless motors (BLDC) with ESC

---

## 🧑‍💻 Author

Made with ❤️ and a soldering iron.  
Feel free to fork, improve, and share!

---

## 📄 License

This project is licensed under the **MIT License** — free to use, modify, and distribute.

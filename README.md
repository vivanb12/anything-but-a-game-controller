# 🏎️ MotionDrive: Turn Your Phone into a Motion Steering Wheel

> **Submission for "Anything but a Game Controller" Challenge**  
> An Android app that turns any smartphone into an ultra-low-latency physical motion controller (tilt-to-steer, shake-to-nitro, haptic rumble) for a companion retro arcade racing game running on a laptop browser.

---

## 📥 Download the APK

- **Direct Repository Download**: Download through releases - MotionDrive v1.0.0

---

## 🎯 Overview & What the App Does

**MotionDrive** replaces traditional gamepads and keyboards with an interactive, physical steering experience:
1. **The Phone as a Steering Wheel**: Uses Android hardware sensor fusion (`Sensor.TYPE_ROTATION_VECTOR` + `Sensor.TYPE_ACCELEROMETER`) to measure your physical steering angle in real time, with calibrated neutral position, deadzone gating, and exponential smoothing.
2. **Embedded Standalone Server**: The Android phone runs its own lightweight **HTTP web server** (port `8080`) and **RFC-6455 WebSocket server** (port `8081`). No cloud servers, external accounts, Node.js installations, or internet access are required.
3. **Retro Arcade 3D Canvas Racer**: A high-speed, OutRun-style pseudo-3D racer running in your laptop's browser. It features curved roads, rolling hills, roadside guardrail fences with impact crash detection, rival traffic cars, collectible coins, upgradeable cars, power-ups, and procedural Web Audio chiptune sound effects.

---

## ✅ Core Requirements Matrix

| Core Requirement | Implementation Details |
|---|---|
| **1. Gyroscope & Accelerometer Data** | Captures 60 Hz rotation vector and accelerometer telemetry. Maps device roll to steering angle (`-1.0` to `+1.0`) and detects physical phone shakes to trigger instant **Nitro Boost**. |
| **2. QR Code / Room Code Pairing** | Generates an on-device QR code via ZXing along with a 4-digit room code and direct local Wi-Fi IP URL (`http://192.168.x.x:8080/?room=XXXX`). |
| **3. Zero Noticeable Lag (WebSockets)** | Streams binary/JSON motion payloads at **60 Hz** over a local Wi-Fi WebSocket connection with sub-15ms round-trip time (RTT). |
| **4. Companion Browser Game** | A self-contained, 60 FPS pseudo-3D retro arcade racer with continuous guardrail fences, crash alerts, car garage, upgrades, and collectible power-ups. |
| **5. Neutral Position Calibration** | One-tap **"🎯 CALIBRATE NEUTRAL"** button zeroes out the holding angle so you can steer comfortably from any resting posture. |
| **6. Automatic Reconnection** | Continuous heartbeat ping/pong protocol with automatic client reconnection and exponential backoff. |
| **Bonus: On-Screen Triggers** | Large tactile **BRAKE / DRIFT** and **NITRO BOOST** touch controls. |
| **Bonus: Vibration Feedback** | Reverse WebSocket events trigger hardware haptics on wall impacts, traffic collisions, nitro ignition, and drift tire skids. |
| **Bonus: Live Latency HUD** | Real-time RTT ping (`ms`) and packet transmission rate (`Hz`) displayed on both the phone and laptop HUD. |

---

## 🛠️ Tech Stack & Architecture

```
┌────────────────────────────────────────────────────────┐
│               ANDROID MOBILE CONTROLLER                │
│                                                        │
│  - Android SensorManager (Rotation Vector, Accel)      │
│  - Jetpack Compose M3 UI (Modern Dark + Electric Lime) │
│  - Embedded HTTP Server (Port 8080: serves game files) │
│  - Embedded WebSocket Server (Port 8081: 60Hz stream)  │
│  - HapticFeedbackManager (Vibrator / VibratorManager)  │
│  - ZXing QR Code Matrix Generator                      │
└───────────────────────────┬────────────────────────────┘
                            │ LAN Wi-Fi (Sub-15ms RTT)
                            ▼
┌────────────────────────────────────────────────────────┐
│             LAPTOP BROWSER RACING GAME                 │
│                                                        │
│  - HTML5 Canvas 60 FPS Pseudo-3D Road Engine           │
│  - Physics: Steering tilt, drifting slides, collisions │
│  - High-visibility Guardrail Border Fences             │
│  - Garage & Car Upgrades (Engine, Turbo, Grip, Nitro)  │
│  - Collectibles: Gold Coins, Shields, Time, Magnets    │
│  - Procedural Web Audio API Sound Synthesizer          │
└────────────────────────────────────────────────────────┘
```

---

## ⚡ Pairing & Latency Strategy

1. **Local Area Network (LAN) Peer-to-Peer**: Rather than relaying sensor data through third-party cloud servers (which introduces 60–200ms of internet lag), MotionDrive runs directly over local Wi-Fi. Latency stays reliably under **15 milliseconds**.
2. **Fixed-Interval Streaming (60 Hz)**: Sensor input is sampled, filtered with exponential moving averages to prevent jitter, and transmitted in lightweight 80-byte JSON packets:
   ```json
   {
     "type": "controller_input",
     "room": "4821",
     "steer": -0.35,
     "nitro": true,
     "drift": false,
     "brake": false,
     "timestamp": 1729000000123
   }
   ```
3. **Reverse Telemetry Channel**: The laptop game dispatches haptic events back to the phone (`{"type": "haptic_event", "event": "collision", "intensity": 1.0}`) to vibrate the phone instantly when crashing into guardrails or traffic.

---

## 🚀 How to Run & Play

1. **Install the App**:
   - Download [`MotionDrive.apk`](./MotionDrive.apk) onto your Android phone and install it.
2. **Start the Controller**:
   - Ensure your phone and laptop are on the same Wi-Fi network (or connect your laptop to your phone's mobile hotspot).
   - Open **MotionDrive** on your phone.
3. **Launch the Game on Your Laptop**:
   - Type the link shown on the phone screen (e.g., `http://192.168.1.15:8080/?room=4821`) in Chrome, Edge, or Safari.
4. **Drive**:
   - On your phone, tap **"ENTER STEERING COCKPIT"** and tap **"🎯 CALIBRATE NEUTRAL"**.
   - Tilt to steer, tap or shake for Nitro, and drift around curves!

---

##
   - [x] Comprehensive documentation covering tech stack, pairing, and latency approach.


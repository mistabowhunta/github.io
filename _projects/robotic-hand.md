---
name: Humanoid Robotic Hand & Modular WiFi Telemetry
tools: [CircuitPython, RP2040, WiFi Sockets, PWM Control, Mechatronics, 3D Printing]
image: /assets/images/Gauntlet.png
description: A high-DOF humanoid robotic hand engineered for precise kinematic sequencing, utilizing a modular CircuitPython architecture for WiFi telemetry and simultaneous PWM control.
---

### **Executive Summary**
This project encompasses the mechatronic design and low-level software control of a fully articulated humanoid robotic hand. Originally prototyped as the terminal effector for a larger humanoid robotics platform, the system was engineered to translate digital network payloads into precise physical gestures. The overarching objective was to establish a robust kinematic foundation capable of seamless integration with remote, WiFi-driven teleoperation systems.

Tech Stack: CircuitPython, RP2040 Microcontroller, socketpool WiFi, PWM Servos, 3D Printing.

Documentation: View the kinematic control scripts on GitHub (Link soon to come!)

---

### **System Architecture**
The software architecture rigidly separates the network connection logic from the physical actuation loops, utilizing a modular file structure orchestrated by a main control script.

#### **1. Telemetry & Network Pipeline (CircuitPython Sockets)**
To handle remote commands, a dedicated network module (wifi_connect.py) handles the SSID handshake via environment variables securely stored in settings.toml. Once authenticated, it establishes a persistent socketpool, which is returned to the main loop to ingest UDP/TCP telemetry data seamlessly without blocking the mechanical kinematics.

#### **2. Hardware Integration & Limit Tuning**
The hand utilizes five independent servo motors driven by the RP2040 microcontroller. To prevent mechanical binding and electrical burnout, physical stop angles (e.g., thumb_stop_angle = 115) and precise minimum/maximum PWM pulse widths were hardcoded for each unique actuator, matching the physical tolerances of the 3D-printed chassis.

### **3. Kinematic Control Logic**
The control logic bypasses simple binary open/close commands in favor of programmable kinematic sequences. The Python pipeline defines specific speeds and positional loops to execute complex multi-axis gestures smoothly, driven by the data ingested from the WiFi socket pool.

```python
# System Architecture Snippet: Modular WiFi & PWM Integration

# 1. wifi_connect.py: Establishing the modular SocketPool
import os, wifi, socketpool

def connect():
    print("Connecting to WiFi...")
    # Isolate credentials using settings.toml environment variables
    wifi.radio.connect(os.getenv('WIFI_SSID'), os.getenv('WIFI_PASSWORD'))
    print("IP address is", wifi.radio.ipv4_address)
    
    # Return a persistent socket pool to the main loop for telemetry ingestion
    return socketpool.SocketPool(wifi.radio)

# ---------------------------------------------------------------------

# 2. code.py / left.py: Linking telemetry to kinematics (RP2040)
import board, pwmio, time
from adafruit_motor import servo
import wifi_connect

# Initialize modular network pool
pool = wifi_connect.connect()

# Initialize independent PWM outputs for high-DOF actuation
pwm_thumb = pwmio.PWMOut(board.GP10, frequency=50)
thumb = servo.Servo(pwm_thumb, min_pulse=550, max_pulse=2600)

# Hardware-calibrated limit to prevent mechanical binding
thumb_stop_angle = 115 

def execute_telemetry_gesture(finger_speed):
    """Translates ingested network commands into physical sequences."""
    try:
        for angle in range(thumb_stop_angle, 0, -1):
            thumb.angle = angle
            time.sleep(finger_speed)
    except KeyboardInterrupt:
        print('Sequence interrupted. Failsafe triggered.')
```

---
name: Aegis Control Sentry Turret
tools: [Python, MQTT, Hardware, 3D Printing]
image: 1759271604070.jpg # Replace with actual photo
description: AI-powered mechatronic sentry turret featuring closed-loop MQTT telemetry and a custom PETG-CF chassis.
---

### **Executive Summary**
The Aegis Control system is a fully autonomous, AI-driven sentry turret. Designed and rapidly prototyped from scratch, the system integrates a custom 3D-printed chassis with real-time target tracking and closed-loop MQTT telemetry for remote monitoring.

**Tech Stack:** Python, C#, MQTT, Bambu Studio, PETG-CF.  
**Source Code:** [View the full repository on GitHub](#) ---

### **System Architecture**
To maintain high-speed target acquisition while managing sensor data, the architecture was split between physical actuation and external telemetry processing. 

#### **1. Hardware & Fabrication**
The physical chassis was engineered for high rigidity to handle rapid servo movements without structural flex. 
* **Materials:** Structural components printed in PETG-CF (Carbon Fiber) using a Bambu Lab X1-Carbon.
* **Actuation:** Dual-axis servo control for continuous pan and tilt tracking.

#### **2. Telemetry & Control Logic (Python/MQTT)**
Instead of relying on standard serial print debugging, the system utilizes an MQTT broker to broadcast telemetry data (servo positioning, target lock status, latency) in real time. 

```python
# Example: MQTT Telemetry Publishing Loop
import paho.mqtt.client as mqtt
import json
import time

def publish_telemetry(client, pan_angle, tilt_angle, target_locked):
    payload = {
        "device_id": "aegis_unit_01",
        "status": "ENGAGED" if target_locked else "SCANNING",
        "coordinates": {"pan": pan_angle, "tilt": tilt_angle},
        "timestamp": time.time()
    }
    client.publish("nasonnation/aegis/telemetry", json.dumps(payload))

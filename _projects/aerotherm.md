---
name: AeroTherm Autonomous Radiometric Thermal UAV
tools: [Python, OpenCV, Hardware, 3D Printing, ArduPilot]
image: /assets/images/AeroThermProfile.jpg
description: A 1588g heavy-lift quadcopter utilizing a custom headless Python pipeline and FLIR Lepton 3.5 for absolute thermal anomaly detection.
---

### **Executive Summary**
The AeroTherm system is a fully autonomous, heavy-lift drone designed for Search and Rescue (SAR) and industrial inspection. Designed to bypass standard relative thermal vision, the system integrates a headless Raspberry Pi 3 A+ and a FLIR Lepton 3.5 to detect, categorize, and log absolute temperature anomalies in real-time. 

**Tech Stack:** Python, OpenCV, PixHawk 6C, ArduPilot, SPI/I2C, ROS 2, Linux.

**Documentation:** [View the official publication on Hackster.io](https://www.hackster.io/NasonNation_Robotics/aerotherm-autonomous-radiometric-thermal-drone-68ca3f)

![Thermal Image Example](/assets/images/ATThermalImage.jpg)

---

### **System Architecture**
To process absolute radiometric data at 26Hz while maintaining stable autonomous flight, the system separates flight dynamics from the compute payload.

![Thermal Image Example](/assets/images/ATSideView.jpg)

#### **1. Hardware & Fabrication**
The physical chassis was engineered for high rigidity and payload capacity, coming in at a 1588g All-Up Weight (AUW).
* **Materials:** Custom structural mounts printed in PETG-CF utilizing a Bambu Lab X1-Carbon, integrated onto a 7-inch Mark4 carbon fiber frame.
* **Power Isolation:** A custom power delivery system utilizing a Matek 12S BEC. This ensures the 5V logic hardware (Raspberry Pi and PixHawk) are safely isolated from the raw 6S LiPo voltage feeding the ESC.

#### **2. Perception-to-Action Pipeline (Python)**
The core brain operates on a Raspberry Pi 3 A+, utilizing a custom Python daemon to extract raw 16-bit VoSPI arrays from the FLIR Lepton 3.5. The Python backend applies absolute Celsius conversions and utilizes a localized threshold dictionary to trigger automated captures upon detecting specific heat signatures.

```python 
# Core logic snippet: Fault-tolerant SPI architecture for headless execution
import spidev

def thermal_capture_thread():
    spi = spidev.SpiDev()
    spi.open(0, 0)
    spi.max_speed_hz = 16000000

    try:
        while True:
            frame = get_lepton_frame(spi)
            process_thermal_data(frame)
    except Exception as e:
        log_fatal_error(e)
    finally:
        spi.close()
```

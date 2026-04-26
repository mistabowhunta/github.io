---
name: Autonomous UAV Systems & MAVLink Telemetry
tools: [Python, ArduPilot, MAVLink, Pixhawk 6C, Sensor Fusion, 3D Printing, iNav, BetaFlight, Mission Planner, Express LRS, BLHeli Suites, ESC Configurator Online]
image: /assets/images/1777155190813.png
description: A fleet of custom multi-rotor UAVs utilizing MAVLink telemetry and Python-driven Finite State Machines (FSMs) for autonomous, GPS-denied indoor navigation.
---

### **Executive Summary**
The Custom UAV Platforms project focuses on the design, fabrication, and mechatronic integration of a fleet of multi-rotor drones. The overarching engineering objective is to bypass standard, GPS-reliant mission planners by implementing custom telemetry-driven Finite State Machines (FSMs) for robust autonomous operation in indoor and GPS-denied environments.
Tech Stack: Python, ArduPilot, MAVLink Protocol, Pixhawk 6C, BNO085 IMUs, SITL.
Documentation: View the MAVLink control scripts on GitHub (Note: link this to your repo if you have one)

**Tech Stack:** Python, ArduPilot, MAVLink Protocol, Pixhawk 6C, BNO085 IMUs, SITL.  
**Documentation:** [View the MAVLink control scripts on GitHub](https://github.com/mistabowhunta/Autonomous-UAV-Systems)

---

### **System Architecture**
The architecture adheres to a distributed control methodology, decoupling deterministic low-level physical flight stabilization (handled by the flight controller) from high-level decision loops and state logic.

#### **1. Hardware & Fabrication**
Multiple UAV platforms were custom-designed to emphasize payload distribution, mechanical vibration dampening, and access to internal electronics for iterative debugging.
Materials: Custom airframes 3D-printed utilizing a Bambu Lab X1 Carbon.
Sensor Fusion & Control: Integration of Pixhawk 6C flight controllers and high-precision BNO085 IMUs. Extensive practical PID loop tuning was performed on the diverse airframes to stabilize flight mechanics and achieve a predictable kinematic response.

#### **2. Telemetry-Driven Control Pipeline (Python/MAVLink)**
To overcome the limitations of standard GPS waypoints, a custom Python script acts as a high-level "mission commander." Utilizing the MAVLink protocol, the script maintains a continuous telemetry stream with the flight controller, monitoring critical data points like orientation and logical state. The core logical flow is executed through a deterministic Finite State Machine (FSM), allowing the UAV to react dynamically to real-world variables and perception triggers rather than static coordinates.

```python
class XP5FlightController:
    def __init__(self, connection_string, target_altitude=10):
        self.vehicle_name = "XP5"
        self.target_altitude = target_altitude
        
        # Flight profiles
        self.thrust_ascend = 0.50001
        self.thrust_hover = 0.5
        self.thrust_descend = 0.49999
        self.flight_mode = "GUIDED_NOGPS"
        self.failsafe_mode = "STABILIZE"

        # Encapsulated telemetry state: FS: forward sensor, LS: left sensor, RS: right sensor, BS: back sensor, ALT: down sensor, US: up sensor
        self.telemetry = {
            'FS': 0.0, 'LS': 0.0, 'RS': 0.0, 
            'BS': 0.0, 'ALT': 0.0, 'US': 0.0
        }

        print(f"Connecting to {self.vehicle_name} on {connection_string}...")
        self.vehicle = connect(connection_string, wait_ready=False, baud=57600)
        self.vehicle.wait_ready(True, timeout=60)
        
        self._setup_telemetry_listener()
        self._run_pre_flight_checks()

    def _setup_telemetry_listener(self):
        """Asynchronous listener for LiDAR distance sensors."""
        @self.vehicle.on_message('DISTANCE_SENSOR')
        def listener(vehicle, name, message):
            sensor_map = {0: 'FS', 1: 'LS', 2: 'RS', 3: 'BS', 4: 'ALT', 5: 'US'}
            if message.id in sensor_map:
                distance = float(message.current_distance)
                # Adjust downward sensor for ground clearance
                if message.id == 4:
                    distance -= 2.2 
                
                self.telemetry[sensor_map[message.id]] = distance
                
            # Optional: Log output (can be commented out for cleaner terminal during flight)
            # print(f"[{datetime.now()}] Telemetry Update: {self.telemetry}")


#...
#... More code lives here, this is just a sample 
#...


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='XP5 Autonomous Flight Controller')
    parser.add_argument('--connect', default='COM12', help='Vehicle connection target string.')
    args = parser.parse_args()

    # Initialize and run the UAV controller
    uav = XP5FlightController(connection_string=args.connect, target_altitude=10)
    
    try:
        uav.execute_mission()
    except KeyboardInterrupt:
        print("\nManual override triggered. Initiating emergency shutdown.")
        uav.deinitialize()

```

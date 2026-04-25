Executive Summary

The Custom UAV Platforms project focuses on the design, fabrication, and mechatronic integration of a fleet of multi-rotor drones. The overarching engineering objective is to bypass standard, GPS-reliant mission planners by implementing custom telemetry-driven Finite State Machines (FSMs) for robust autonomous operation in indoor and GPS-denied environments.
Tech Stack: Python, ArduPilot, MAVLink Protocol, Pixhawk 6C, BNO085 IMUs, SITL.
Documentation: View the MAVLink control scripts on GitHub (Note: link this to your repo if you have one)

System Architecture

The architecture adheres to a distributed control methodology, decoupling deterministic low-level physical flight stabilization (handled by the flight controller) from high-level decision loops and state logic.

Hardware & Fabrication

Multiple UAV platforms were custom-designed to emphasize payload distribution, mechanical vibration dampening, and access to internal electronics for iterative debugging.
Materials: Custom airframes 3D-printed utilizing a Bambu Lab X1 Carbon.
Sensor Fusion & Control: Integration of Pixhawk 6C flight controllers and high-precision BNO085 IMUs. Extensive practical PID loop tuning was performed on the diverse airframes to stabilize flight mechanics and achieve a predictable kinematic response.

Telemetry-Driven Control Pipeline (Python/MAVLink)

To overcome the limitations of standard GPS waypoints, a custom Python script acts as a high-level "mission commander." Utilizing the MAVLink protocol, the script maintains a continuous telemetry stream with the flight controller, monitoring critical data points like orientation and logical state. The core logical flow is executed through a deterministic Finite State Machine (FSM), allowing the UAV to react dynamically to real-world variables and perception triggers rather than static coordinates.

Python
# Core logic snippet: MAVLink FSM for GPS-Denied Navigation
class UavMissionController:
    def __init__(self):
        self.logical_state = "IDLE"

    def execute_state_machine(self):
        while True:
            self.telemetry = self.read_mavlink_stream()

            if self.logical_state == "IDLE":
                if self.check_mission_ready():
                    self.logical_state = "TAKEOFF"

            elif self.logical_state == "TAKEOFF":
                # Execute automated takeoff logic, monitoring altitude
                self.automated_takeoff(target_altitude=5.0)
                if self.telemetry.altitude >= 5.0:
                    self.logical_state = "NAVIGATE_TO_TARGET"

            elif self.logical_state == "NAVIGATE_TO_TARGET":
                # Execute complex, GPS-denied navigation and perception logic
                if self.detect_obstacles():
                    self.logical_state = "PIVOT_AND_AVOID"

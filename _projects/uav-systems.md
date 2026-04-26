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
**Documentation:** [View the MAVLink control scripts on GitHub]([https://github.com/mistabowhunta](https://github.com/mistabowhunta/Autonomous-UAV-Systems))

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
# Small logic snippet: PyMavLink (MAVLink) and DroneKit for GPS-Denied Navigation
################################################################################################
# Connect vehicle
################################################################################################

print "Connecting to " + vehicleName + "..."
connection_string       = 'com12'
vehicle = connect(connection_string, wait_ready=False, baud=57600)
vehicle.wait_ready(True, timeout=60)

################################################################################################
# Listeners
################################################################################################

# Message listener for lidar sensors
	# id 0 = forward, id 1 = left, id 2 = right, id 3 = back, id 4 = down, id 5 = up
	# FS: forward sensor, LS: left sensor, RS: right sensor, BS: back sensor, ALT: down sensor, US: up sensor
FS = 0
LS = 0
RS = 0
BS = 0
ALT = 0
US = 0
@vehicle.on_message('DISTANCE_SENSOR')
def listener(self, name, message):
	global FS
	global LS
	global RS
	global BS
	global ALT
	global US
	if(message.id == 0):
		FS = float(message.current_distance)
	elif(message.id == 1):
		LS = float(message.current_distance)
	elif(message.id == 2):
		RS = float(message.current_distance)
	elif(message.id == 3):
		BS = float(message.current_distance)
	elif(message.id == 4):
		ALT = float(message.current_distance) - 2.2 # Sensor is 20 cm from the ground so need to adjust for that
	elif(message.id == 5):
		US = float(message.current_distance)
		#if(US <= 20):
			#emergency_land('ALT')
	print('TimeStamp: ' + str(datetime.now()) + ' FS: ' + str(FS) + ' LS: ' + str(LS) + ' RS: ' + str(RS) + ' BS: ' + str(BS) + ' ALT: ' + str(ALT) + ' US: ' + str(US))

#...
#... More code here. This is just a sample
#...


# Arm vehicle
print " Arming " + vehicleName + "!"
vehicle.armed = True

################################################################################################
# FLYING!
################################################################################################
	
print(" Taking off!")
print('FS: ' + str(FS) + ' LS: ' + str(LS) + ' RS: ' + str(RS) + ' BS: ' + str(BS) + ' ALT: ' + str(ALT) + ' US: ' + str(US))

roll_angle = -1
yaw_angle = -20
pitch_angle = 7
duration = 2

time.sleep(5)
thrust = thrustAscend
t_endAscend = time.time() + 15 # times by 60 is 5 minutes 
while not ALT >= aTargetAltitude:
	set_attitude(thrust = thrust)
	# if(ALT >= 0):
		# print "ALT >= 0: Start steadying"
		# set_attitude(roll_angle = roll_angle, yaw_angle = yaw_angle, pitch_angle = pitch_angle, thrust = thrust, duration = duration)
		# print "Exited steadying while ascending"
	# else:
		# set_attitude(thrust = thrust)
	print " ********************************************************ALT: " + str(ALT)
	time.sleep(0.2)
	if(ALT >= aTargetAltitude + 20): # safety net - will exit while loop if alt is +0.04 of target alt
		print("EXITED ON >= Alt")
		break
	if(time.time() >= t_endAscend):
		print "  EXITED ON SECONDS Ascending ALT: " + str(ALT)
		break

# Hover for 5 seconds
print "HOVERING FOR 5 SECONDS"
thrust = thrustHover
set_attitude(thrust = thrust, duration = 5)
```

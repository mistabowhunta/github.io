---
name: Hand Cannon - Heavyweight Haptics
tools: [Hardware Architecture, Microsoft 3D Builder, Blender, 3D Printing, Prototyping]
image: /assets/images/PolishedDisplay.png
description: An exploratory mechatronic build testing high-force linear actuators for VR recoil, resulting in an open-source hardware chassis release.
---

### **Executive Summary**
The "Hand Cannon" project was an experimental hardware build designed to push the limits of physical recoil in virtual reality by replacing standard vibration motors with heavy linear actuators. While the electronic synchronization was successful, the physical output did not meet the required threshold. The project ultimately pivoted into an open-source mechanical release, providing the community with a robust, load-bearing 3D-printed chassis.

**Tech Stack:** Microsoft 3D Builder, Blender, Electromechanical Actuation.  
**Documentation:** [View the STL and BOM release on Hackster.io](https://www.hackster.io/NasonNation_Robotics/hand-cannon-the-journey-from-haptics-to-heavyweight-0194d8)

---

### **System Architecture & The Engineering Pivot**
The initial design goal was to achieve "heavyweight" kinetic feedback by synchronizing multiple high-draw solenoids. 

#### **1. Hardware & Electromechanics**
* **Actuation:** The system was engineered around three parallel uxcell DC 12V 25N (10mm stroke) push-type tubular solenoids (XRN-25x50T).
* **Chassis Design:** Prototyped on a Bambu Lab X1-Carbon. The frame required reinforced infill and load-bearing geometries to house the heavy solenoids and absorb the physical shock of repeated actuation cycles.

#### **2. The Pivot to Open Source**
During testing, the circuit successfully fired all three solenoids simultaneously. However, the combined 75N of kinetic force did not meet the desired threshold for realistic "heavyweight" recoil when scaled against the physical weight of the hardware. 

Engineered the electrical integration for the high-draw solenoids, utilizing MOSFETs and flyback diodes to isolate inductive voltage spikes and protect the core logic board during simultaneous firing.

Instead of over-engineering a heavier, tethered power delivery system, the active control loop was scrapped. The project was finalized as an open-source mechanical release. The engineered frame STLs and complete Bill of Materials (BOM) were published for the community to utilize in their own robotics or haptic builds.

### Hardware Release: Core Bill of Materials
Assembly and Primary Components: "Hand_Cannon_Chassis_v1"
  - part: "Frame Housing"
    material: "PETG-CF"
    infill: "40% Gyroid for structural integrity"
  - part: "Solenoid"
    model: "uxcell XRN-25x50T"
    specs: "12V DC, 25N Force, 10mm Stroke"
    quantity: 3

# Soft-Robotic-FPCB-Gripper
Prototype flexible PCB sensing architecture for multi-modal feedback in soft robotic grippers.


The main question behind the project was:

**How do you add useful sensing to a soft robotic finger without making the finger rigid, difficult to wire, or hard to debug?**

The design explored a thin polyimide FPCB that could follow the shape of a soft gripper while carrying multiple sensing modes over a shared I2C bus.

---

## Project Snapshot

| Item | Details |
|---|---|
| Area | Soft Robotics / Flexible Electronics |
| Project Type | Undergraduate Research Prototype |
| Duration | ~2 months |
| Substrate | Polyimide FPCB |
| Communication | I2C |
| Sensing Modes | Motion, pressure-proxy, temperature |
| Supply | 3.3 V |
| Status | Design-stage prototype |

---

## The Problem

Soft robotic grippers work because they can bend and conform around objects.

That becomes harder once conventional electronics are embedded inside them.

A rigid PCB can create a stiff section inside the finger. Extra wiring can resist bending and introduce more failure points. External cameras can also lose visibility once the gripper closes around the object.

The goal was therefore not just to place sensors on a board, but to design the electronics around the mechanical behaviour of the gripper itself.

The proposed solution was a narrow **flexible sensing spine** that follows the shape of the finger instead of forcing a rectangular rigid PCB into it.

---

## Proposed FPCB Architecture

The prototype uses a thin polyimide flexible PCB with sensors distributed along the finger based on what each location is best suited to measure.

### Prototype sensing layout

| Sensor | Purpose |
|---|---|
| **MPU-6050** | Motion, orientation, bending behaviour |
| **BMP280** | Pressure-proxy / deformation feedback |
| **LM75** | Local temperature monitoring |

The three sensing nodes share the same communication and power backbone:

**SDA + SCL + 3.3 V + GND**

This lets several sensors communicate without needing separate wiring for every device.

The idea was to make the PCB act more like a **distributed sensing spine** than a normal sensor board.

---

## Sensor Selection & Consolidation

A large part of the research work involved reading existing soft-robotics and flexible-electronics literature to understand what sensors other groups were using and why.

One of the main design goals was to avoid adding a separate sensor for every possible measurement.

Every extra sensor adds:

- PCB area
- Cost
- Copper routing
- Solder joints
- Firmware
- Calibration work
- Debugging time
- Another possible failure point

That made **multi-function sensing** especially interesting.

For example, an IMU does not provide only one useful measurement. The same device can provide acceleration and angular-rate data that may help describe orientation, motion, bending, and potentially vibration associated with grasp instability.

### Devices investigated during the research

| Sensing Need | Devices Investigated | Design Goal |
|---|---|---|
| Motion / orientation / vibration | LSM6DSOX, MPU-6050 | Use one inertial device for several motion-related measurements |
| Pressure / deformation | MS5837, BMP280 | Obtain pressure-related information with minimal mechanical complexity |
| Temperature | MAX31875, LM75 | Add thermal feedback without significantly increasing wiring or board area |

The sensor list changed as the project developed. The goal was not to lock onto the first device found, but to compare options based on size, sensing capability, bus compatibility, integration difficulty, and usefulness inside a soft structure.

---

## Why I2C?

One of the main logistical problems in an embedded soft-robotic system is wiring.

Every extra conductor running through a flexible finger adds stiffness and gives another place for the system to fail.

Using I2C allows several sensors to share the same two communication lines:

- **SDA** — data
- **SCL** — clock

along with the common power and ground rails.

The prototype architecture also used:

- **4.7 kΩ pull-up resistors** on the I2C bus
- **0.1 µF decoupling capacitors** near the sensor electronics

The shared bus helped reduce the number of traces that had to run through the flexible structure.

---

## Flexible PCB & Mechanical Design

The board itself had to be treated as part of the mechanical system.

A standard FR4 PCB would be much stiffer than the surrounding soft material, so the prototype instead used a thin polyimide flex construction.

| Design Parameter | Proposed Value |
|---|---:|
| Base Material | Polyimide |
| Substrate Thickness | **0.1 mm** |
| Layer Count | **2 layers** |
| Copper Weight | **1 oz / 0.5 oz for increased flexibility** |
| Stiffeners | Localized under sensor ICs only |

The copper traces also matter mechanically.

Even though the substrate is flexible, copper still adds stiffness and experiences strain while the finger bends. The design therefore considered routing the traces near the neutral bending region of the flex stack to reduce mechanical stress.

The goal was to keep rigid material only where it was actually needed.

---

## Encapsulation & Mechanical Signal Integrity

One of the biggest unknowns was what would happen **after the FPCB was embedded inside the silicone gripper**.

The electronics may work correctly on their own, but once they are encapsulated, the silicone becomes part of the sensing system.

Several failure mechanisms were considered.

### Pressure redistribution

A force applied to the outside of the finger may not reach the embedded pressure sensor in the same form.

The silicone can spread or absorb part of the load before it reaches the sensing element.

That means a pressure reading cannot automatically be treated as the true external contact force.

The pressure sensor would need to be calibrated after embedding.

### Vibration damping

Soft silicone can absorb high-frequency mechanical vibration.

This is important if inertial sensing is being used to detect events such as object slip or small contact vibrations.

The vibration measured by the IMU may therefore be weaker than the vibration that actually occurred at the gripper surface.

### Calibration shift

A sensor calibrated before encapsulation may behave differently after it is surrounded by silicone, mechanically compressed, or bent with the finger.

The embedded environment has to be treated as part of the calibration process.

### Added stiffness

The flexible substrate can bend, but the sensor packages themselves are still rigid.

Poor component placement could create local stiff spots or change how the finger deforms.

### Repeated bending

Repeated actuation can place stress on:

- Copper traces
- Solder joints
- Component pads
- Connectors
- Rigid sensor packages

These would need to be tested over many bending cycles before the design could be considered reliable.

---

## Foreshadowed Failure Modes

| Risk | Possible Effect | Planned Response |
|---|---|---|
| Silicone pressure damping | Sensor reading does not directly match external force | Calibrate after embedding |
| Vibration absorption | Slip-related vibration becomes weaker | Improve sensor placement / mechanical coupling |
| Repeated bending | Trace or solder-joint fatigue | Bend-cycle testing |
| Added electronics stiffness | Gripper becomes less compliant | Compare gripper motion with and without FPCB |
| Moisture / environmental exposure | Electrical failure or corrosion | Protective coating / encapsulation strategy |
| Increasing sensor count | More wiring and debugging complexity | Shared bus and sensor consolidation |

---

## System-Level Scaling

Another question was what happens if the same sensing system is eventually placed across multiple gripper fingers.

A single microcontroller may be sufficient for a small number of sensors, but larger systems introduce new problems:

- More devices sharing the communication bus
- Higher polling requirements
- More data to process
- Longer wiring paths
- Increased parasitic capacitance
- More complicated debugging

The early design work considered keeping the sensors on a shared bus and eventually using more parallel processing if the system scaled to many fingers.

The larger lesson was that the electronics could not be designed separately from the communication and data-processing architecture.

---

## Prototype Status

This project remained at the **design and research stage** during my time in the lab.

The sensing architecture, sensor trade study, flexible stack-up, communication strategy, failure analysis, and validation plan were developed, but the final FPCB was not fabricated or experimentally validated.

The intended next steps were:

1. Fabricate the FPCB
2. Verify electrical operation outside the gripper
3. Perform repeated bend-cycle testing
4. Embed the board inside the silicone finger
5. Calibrate the sensors after encapsulation
6. Measure how much the FPCB changes gripper compliance
7. Run grasp tests using the combined sensor feedback

---

## Research Deliverables

This repository includes the main technical documentation produced during the project.

### Technical Paper

A concise research-style overview of the proposed FPCB architecture, sensing strategy, mechanical integration, limitations, and validation roadmap.

[View Technical Paper](ELIXR_Report%20(6).pdf)

### Design Documentation

More detailed notes covering the proposed flex stack, sensor architecture, I2C design, component choices, mechanical considerations, and future scaling concerns.

[View Design Documentation](Design%20Documentation%20(1).pdf)

---

## Skills / Topics

- Flexible PCB architecture
- Soft robotics
- Sensor selection and trade studies
- Multi-modal sensing
- I2C communication
- Flexible-electronics research
- Mechanical / electrical co-design
- Embedded sensor integration
- Failure-mode analysis
- Technical literature review
- Research documentation

---

## Research Context

Undergraduate research work completed in the **ELIXR Lab at Toronto Metropolitan University**.

The project focused on exploring how flexible electronics and embedded sensing could be integrated into soft robotic grippers without introducing the stiffness, wiring complexity, and sensing limitations of more conventional approaches.

# Multi-Modal FPCB for Soft Robotic Grippers

Prototype flexible-PCB sensing architecture developed during my undergraduate research work in soft robotics.

The project focused on integrating sensing into a soft robotic gripper without adding too much stiffness, wiring, or hardware complexity.

The proposed design used a thin polyimide FPCB with motion, pressure-proxy, and temperature sensing connected over a shared I2C bus.

Research area: Soft robotics, flexible electronics, embedded sensing  
Duration: **~2 months**  
Project stage: Research and prototype design

---

## Design Goal

The main problem was fitting useful sensing into a soft gripper without making the finger harder to bend or unnecessarily complicated to build.

A standard rigid PCB would be easier to design electrically, but it would create a stiff section inside the silicone finger. The electronics needed to follow the motion of the gripper, so the design moved toward a thin flexible PCB instead.

Sensor count was also important.

Adding another sensor meant:

- Another IC to purchase
- More board area
- More traces to route
- More solder joints
- More firmware
- More calibration
- Another point to debug if something failed after encapsulation

The design therefore focused on using a small number of sensors that could provide several useful measurements while keeping the board practical to route and fabricate.

---

## Proposed FPCB Architecture

The later prototype used three sensing nodes:

| Sensor | Function |
|---|---|
| **MPU-6050** | Motion, orientation, and bending information |
| **BMP280** | Pressure-proxy / deformation feedback |
| **LM75** | Local temperature monitoring |

The sensors shared the same basic interface:

SDA · SCL · **3.3 V** · GND

The board was intended to run along the inside of the gripper rather than placing a rectangular PCB in one location.

Sensor placement was also considered as part of the design. The motion sensor could be placed farther along the finger where movement is more pronounced, while the pressure-proxy and thermal sensors could be positioned where their measurements would be more useful.

---

## Sensor Research and Selection

A large part of the project involved reading papers and comparing sensors used in soft robotics, tactile sensing, and flexible electronics.

The goal was not just to find one sensor for each measurement. I was also looking for devices that could provide several useful measurements from one IC so the total number of components could be reduced.

For example, a **6-axis** IMU provides both acceleration and angular-rate data. One package can therefore contribute to several motion-related measurements instead of using separate devices for each one.

The sensors investigated changed during the project:

| Sensing Need | Sensors Considered |
|---|---|
| Motion / vibration | **LSM6DSOX**, **MPU-6050** |
| Pressure / deformation | **MS5837**, **BMP280** |
| Temperature | **MAX31875**, **LM75** |

Sensor selection was based on more than performance alone. I also considered:

- Package size
- Number of useful measurements available from one IC
- I2C compatibility
- Routing requirements
- Cost
- Part availability
- Firmware support
- Calibration requirements
- Ease of debugging

For a single prototype, one extra sensor may not seem important. On a multi-finger gripper, the same choice gets repeated several times, so component count, cost, and wiring can increase quickly.

---

## Why I2C

I2C allowed multiple sensors to share the same SDA and SCL lines.

That was useful on a flex PCB because every additional electrical connection eventually becomes another copper trace that has to be routed through a long, narrow board.

The FPCB also has mechanical constraints that a normal rectangular PCB does not. The traces need to follow the shape of the gripper and pass through areas that repeatedly bend.

Using a shared communication bus reduced the number of signal traces running along the finger.

This helped with:

- Routing
- Board width
- Fabrication
- Copper density
- Debugging
- Scaling to multiple sensors

The prototype also included:

- **4.7 kΩ** I2C pull-up resistors
- **0.1 µF** decoupling capacitors
- Shared **3.3 V** and ground rails

The routing was intended to stay near the lower-strain region of the flex stack where possible, reducing the mechanical stress seen by the copper during bending.

Fewer communication traces also meant fewer paths to inspect if a sensor stopped responding.

---

## Flexible PCB Construction

The proposed board used a thin polyimide construction.

| Parameter | Proposed Value |
|---|---|
| Substrate | Polyimide |
| Thickness | **0.1 mm** |
| Layers | **2** |
| Copper | **1 oz**, with **0.5 oz** considered for greater flexibility |
| Stiffeners | Localized underneath sensor ICs |

Polyimide was selected because it could bend with the gripper more easily than a rigid FR4 board.

The board still could not be treated as completely flexible.

Copper traces, sensor packages, solder joints, connectors, and stiffeners all add local stiffness. Component placement therefore had to consider both the electrical layout and how the board would bend once it was inside the actuator.

---

## Effect of Silicone Encapsulation

One of the main open questions was how the sensor data would change once the FPCB was embedded inside silicone.

The surrounding material becomes part of the sensing system.

For pressure sensing, the force applied to the outside of the gripper has to pass through the silicone before reaching the embedded sensor.

The silicone can spread or absorb some of that force, meaning the sensor output would need to be calibrated after embedding rather than treated as a direct measurement of external contact force.

A similar problem exists for vibration.

If the IMU were used to detect small vibrations associated with slip, the soft material surrounding it could damp some of those vibrations before they reached the sensor.

The electronics could also affect the gripper itself.

The FPCB can bend, but the IC packages are still rigid. Poor component placement could create stiff regions or concentrate stress around pads and solder joints.

The embedded system would eventually need to be characterized as:

External event → Silicone deformation → Embedded sensor response

rather than evaluating the sensors only on a bench.

---

## Reliability and Failure Concerns

Several possible problems were considered before fabrication.

| Concern | Possible Effect | Planned Approach |
|---|---|---|
| Pressure damping through silicone | Sensor output does not directly match external force | Calibrate after embedding |
| Vibration damping | Small slip-related vibrations may be weakened | Evaluate sensor placement and mechanical coupling |
| Repeated bending | Trace or solder-joint fatigue | Bend-cycle testing |
| Rigid sensor packages | Local stiffness inside the finger | Use local stiffeners only where required |
| Moisture exposure | Electrical degradation | Protective coating / encapsulation |
| Too many sensors | More routing, cost, and debugging | Shared bus and sensor consolidation |

Electrical functionality alone would not be enough. The sensing system also had to survive repeated motion without significantly changing the mechanical behaviour of the gripper.

---

## Cost and Integration Considerations

Sensor performance was only one part of the component-selection process.

There were also practical questions around fabrication and scaling:

- Is the package small enough for the FPCB?
- Can one device replace several separate sensing elements?
- Does it use the same communication bus as the other sensors?
- Is the part reasonably priced?
- Is it easy to source?
- Does it require extra supporting circuitry?
- How difficult will it be to route?
- How difficult will it be to debug after encapsulation?

This was one reason multi-function sensors were attractive.

A device that costs slightly more on its own may still reduce the overall system cost if it removes another IC, extra traces, assembly work, or debugging effort.

These tradeoffs become more important when the same FPCB is repeated across several fingers.

---

## Scaling Beyond One Finger

The initial architecture mainly focused on one FPCB.

A larger gripper with several instrumented fingers would introduce additional problems:

- More devices sharing communication resources
- Higher polling requirements
- Longer communication paths
- Increased parasitic capacitance
- More sensor data to process
- More synchronization
- Harder debugging

The design documentation also considered how the sensor acquisition architecture might eventually need more parallel processing as the number of sensors increased.

That work remained conceptual, but it helped frame the FPCB as one part of a larger sensing system rather than an isolated board.

---

## Documentation

The repository includes:

- Research paper covering the proposed FPCB architecture, sensing approach, mechanical integration, and validation plan
- Design documentation covering the flex construction, sensor architecture, I2C interface, component choices, and possible long-term issues

---

## Research Context

Undergraduate research work completed in the ELIXR Lab at Toronto Metropolitan University.

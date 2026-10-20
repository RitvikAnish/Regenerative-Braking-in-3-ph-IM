# Regenerative Braking System Using a 3-Phase Induction Motor

Simulation (MATLAB/Simulink) and proposed hardware demonstration of **regenerative braking** in a 3-phase induction motor. When the supply frequency is reduced suddenly, the rotor spins faster than the synchronous speed, the motor acts as a generator, and braking energy flows back to the supply instead of being lost as heat.

> **Course project, Drives:** Simulation report plus hardware demonstration plan.

## Table of Contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Working Principle](#working-principle)
- [Existing Methods and Their Limitations](#existing-methods-and-their-limitations)
- [Simulink Model](#simulink-model)
- [Hardware Demonstration Plan](#hardware-demonstration-plan)
- [Results](#results)
- [Key Takeaways](#key-takeaways)
- [Repository Contents](#repository-contents)
- [How to Run the Simulation](#how-to-run-the-simulation)
- [Team](#team)
- [License](#license)

## Overview

Conventional braking dissipates a motor's kinetic energy as heat. Regenerative braking recovers that energy as electricity. It is the core technology behind electric and hybrid vehicles and industrial systems such as elevators and cranes.

This project has two parts:

1. **Simulation:** a MATLAB/Simulink model of a 3-phase induction motor that first runs normally and then undergoes regenerative braking. The model produces speed, torque and power graphs.
2. **Hardware demonstration plan:** a small-scale model using a VFD-driven motor and a bi-directional energy meter to show power flow reversing.

## Objectives

- Simulate regenerative braking using a 3-phase induction motor
- Analyse motor performance during braking
- Observe power flow reversal (positive in motoring, negative in generating)
- Plot speed, torque and power graphs
- Improve understanding of energy recovery systems

**Hardware model goals:** show power reversal with a meter, use a VFD to initiate braking on command by changing the output frequency, and display key data such as motor speed and direction of power flow.

## Working Principle

1. The motor runs normally at the standard supply frequency and consumes power (motoring).
2. To brake, the drive rapidly **reduces the supply frequency**, which lowers the synchronous speed.
3. The rotor speed is now **higher than the synchronous speed**, so the slip becomes negative.
4. The motor's role flips: it stops consuming power and starts **generating**.
5. Kinetic energy is converted into electrical energy and sent back through the drive to the supply.
6. A bi-directional energy meter (hardware) or the power plot (simulation) shows **positive power while motoring and negative power while braking**, confirming regeneration.

## Existing Methods and Their Limitations

| Existing method | Limitation |
|---|---|
| Mechanical braking | Energy is lost as heat; wear and low efficiency |
| Dynamic braking (resistors) | Absorbs energy in resistors instead of recovering it |
| Plugging (reversing the motor) | Wastes power |
| Existing Simulink models | Mostly cover EVs, BLDC motors and single-phase motors, not 3-phase induction motors |

Together these mean energy loss, low efficiency, no regeneration and higher operating cost.

## Simulink Model

**File:** [`simulation/ac1_2025a.slx`](ac1_2025a.slx)
**Created with:** MATLAB R2025a, Simulink and Simscape Electrical (Specialized Power Systems blocks)

### Main blocks and parameters

| Block | Setting |
|---|---|
| 3-phase AC supply (3 × AC Voltage Source) | 400 V amplitude, 50 Hz, phases 0° / 120° / −120° |
| Universal Bridge (×2, 3 arms, IGBT/diodes) | Converter stages between the supply, DC link and motor |
| PWM Generator, three-phase two-level (×2) | Continuous PWM, sinusoidal PWM (SPWM) |
| DC-link capacitor (Series RLC Branch) | Capacitance-type branch, C = 1 µF |
| Asynchronous Machine (SI Units) | Nominal 18.45 kW, 400 V, 50 Hz; 2 pole pairs |
| Machine electrical parameters | Rs = 0.5968 Ω, Lls = 0.3495 mH, Rr' = 0.6258 Ω, Llr' = 5.473 mH, Lm = 35.4 mH |
| Machine mechanical parameters | Inertia J = 0.05 kg·m², friction F = 0.005879 N·m·s |
| Mechanical input | Torque Tm |
| Measurements | Three-Phase V-I Measurement, voltage measurements, Power Measurement (Three-Phase), Bus Selector for rotor speed, electromagnetic torque and stator currents |
| Scopes | `Outputs` (4 channels) and `Power` (2 channels) |
| Solver | Discrete, powergui sample time 5 µs, simulation stop time 3 s |

### Signals recorded

- Rotor speed
- Electromagnetic torque (Te)
- Stator currents (phases a, b, c)
- Input and output power / voltage

## Hardware Demonstration Plan

A small-scale model to prove power flow reversal in practice. The deck and flowchart are in [`docs/regen.pptx`](docs/regen.pptx).

| Component | Purpose |
|---|---|
| 3-phase induction motor (small, about 0.25 HP or less) | Machine under test |
| Variable Frequency Drive (VFD) | Controls motor speed and enables braking by changing output frequency. It must be a regenerative type or have a braking resistor for the back-fed energy |
| 3-phase power supply | Source for the VFD and motor |
| Mechanical load | Simulates external inertia or force on the shaft |
| Microcontroller (Arduino / Raspberry Pi) | Controls the VFD, reads sensors and drives the display |
| Current and voltage sensors / bi-directional energy meter | Measure power flowing to and from the motor |
| Speed sensor (tachometer or Hall effect) | Measures rotor speed |
| LCD / LED display | Shows speed, power consumption and power returned |

## Results

> **TODO:** add the simulation plots and a short discussion of each.

Suggested figures from the Simulink scopes:

| Figure | What it should show |
|---|---|
| Rotor speed vs. time | Normal running, then deceleration after the frequency reduction |
| Electromagnetic torque vs. time | Torque going negative during braking |
| Power vs. time | Power changing from positive (motoring) to negative (generating) |
| Stator currents | Current behaviour at the braking instant |

```markdown
![Speed](docs/images/speed.png)
![Torque](docs/images/torque.png)
![Power](docs/images/power.png)
```

## Key Takeaways

- **Energy efficiency:** regenerative braking conserves energy instead of dissipating it as heat.
- **Control with a VFD:** lowering the frequency forces the motor into a generating state in a controlled way.
- **Real-world use:** electric vehicles, hybrid cars, elevators and cranes.
- **Educational value:** a clear picture of slip and power flow reversal.

## Repository Contents

```
.
├── README.md
├── simulation/
│   └── ac1_2025a.slx            # Simulink model (R2025a)
├── docs/
│   ├── drives_project_report.docx
│   ├── regen.pptx               # Hardware demonstration deck
│   └── images/                  # Plots, model screenshot, hardware photos
└── LICENSE
```

Links: [Simulink model](simulation/ac1_2025a.slx) · [Project report](docs/drives_project_report.docx) · [Presentation](docs/regen.pptx)

## How to Run the Simulation

**Requirements:** MATLAB R2025a (or newer), Simulink, Simscape Electrical.

1. Clone or download this repository.
2. Open MATLAB and run `open_system('simulation/ac1_2025a.slx')`.
3. Click **Run** (the stop time is 3 s).
4. Open the `Outputs` and `Power` scopes to view speed, torque, currents and power.

(The model was saved in R2025a and may not open in older MATLAB versions.)

## Team

| Name | Registration No. |
|---|---|
| Ritvik Anish | 23BEE1018 |
| Sanjh Yadav | 23BEE1043 |
| Adithyan D Ajay | 23BEE1009 |



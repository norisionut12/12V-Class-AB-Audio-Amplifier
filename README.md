# 12V Single-Supply Class AB Audio Amplifier with Speaker Protection

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![EDA: KiCad 10](https://img.shields.io/badge/EDA-KiCad_10.0-green.svg)
![Simulation: LTspice](https://img.shields.io/badge/Simulation-LTspice-orange.svg)
![Technology: Discrete BJT THT](https://img.shields.io/badge/Hardware-Discrete_BJT_THT-red.svg)

**Author:** Buta Noris Ionuț  

---

## Overview

This repository contains the complete hardware design, mathematical analysis, SPICE simulation validation, and physical prototype implementation of a **discrete 12V single-supply Class AB audio amplifier** with an integrated **RC anti-thump speaker protection circuit** and **linear voltage regulator**.

Designed from the ground up without integrated circuits (using 5 discrete bipolar junction transistors), the amplifier takes a stereo line-level input from a laptop, PC, or smartphone, sums it into a mono signal, and drives an $8\text{ }\Omega$ speaker from a standard $12\text{ V}$ DC power adapter. Because a single-supply topology biases the output stage at approximately half the supply rail ($6.76\text{ V}$ DC), a large $1000\text{ }\mu\text{F}$ output coupling capacitor is required, which produces a severe voltage spike at power-up ($11.6\text{ V}$ peak at $6.8\text{ ms}$). To protect the loudspeaker and eliminate start-up pop/thump, an on-board RC delay circuit holds the speaker disconnected via an external relay for $\approx 0.27\text{ s}$ until the DC operating point settles.

![Assembled Prototype and Test Setup](Images/prototype_test_setup.jpg)

---

## Key Features

* **100% Discrete Analog Topology:** Built around 5 bipolar transistors (`2N2222` for small-signal amplification and switching, complementary `TIP31C` / `TIP32C` for power stages).
* **Stereo-to-Mono Input Mixer:** Passive resistive summing network ($1\text{ k}\Omega + 1\text{ k}\Omega$) with $2\text{ k}\Omega$ inter-channel isolation and AC coupling.
* **High-Gain Voltage Amplifier Stage (VAS):** Common-emitter `2N2222` stage with AC-bypassed emitter degeneration ($A_{VAS} \approx 62$, overall $A_v \approx 51\times$ from the VAS base) and $100\text{ pF}$ Miller frequency compensation for RF immunity.
* **Diode-Biased Class AB Output Stage:** Complementary `TIP31C` (NPN) and `TIP32C` (PNP) emitter followers biased by two `1N4148` diodes ($I_Q = 8.2\text{ mA}$) to eliminate crossover distortion.
* **Output Zobel (Boucherot) Network:** $10\text{ }\Omega + 100\text{ nF}$ series network ($f_{Zobel} = 159\text{ kHz}$) ensuring unconditional stability when driving inductive loudspeaker loads.
* **Dedicated On-Board Linear Regulator:** Zener-referenced (`5V1` + `TIP31C`) emitter-follower regulator stepping down $12\text{ V}$ to $4.33\text{ V}$ under load to safely power the external $5\text{ V}$ relay module.
* **Active Anti-Thump Speaker Protection:** RC timing network ($\tau = 4.7\text{ s}$) driving an open-collector `2N2222` stage that delays relay actuation by $\approx 266\text{ ms}$ at power-up.
* **Custom 2-Layer THT PCB:** Manufactured with blue soldermask, wide power traces, dual-layer ground planes (hatched bottom plane), and full component value silkscreen.

---

## Summary of Design Specifications

| Parameter | Design Choice / Condition | Value (Simulated / Verified at $27^\circ\text{C}$) | Circuit Reference |
| :--- | :--- | :--- | :--- |
| **Power Supply** | Single-rail standard DC adapter | $12\text{ V}$ DC, $98\text{ mA}$ total ($32\text{ mA}$ board + $66\text{ mA}$ relay) | J5, C5, C6 |
| **Speaker Load** | Capacitively coupled loudspeaker | $8\text{ }\Omega$ | C10, J2 (Relay COM-NO), J4 |
| **Audio Input** | $3.5\text{ mm}$ stereo jack summed to mono | $(V_L + V_R)/2$, $Z_{in} = 5.1\text{ k}\Omega$/channel at $1\text{ kHz}$ | J1, R2, R3, C4 |
| **Voltage Gain** | Inverting VAS + unity follower | $32.1\text{ dB}$ ($40\times$) from source; $34.2\text{ dB}$ ($51\times$) from Q2 base | Q2, R7, R8, C8 |
| **Bandwidth ($-3\text{ dB}$)** | High-pass coupling + Miller pole | $301\text{ Hz} \dots 31\text{ kHz}$ | C4, C7, C8, C10 |
| **DC Output Bias** | Near $V_{CC}/2$ | $6.76\text{ V}$ (thermal drift: $-20\text{ mV/}^\circ\text{C}$) | R4, R5, R6, R7, R8 |
| **Output Stage Bias** | Complementary Class AB push-pull | $I_Q = 8.2\text{ mA}$ | R7, D2, D3, Q3, Q4 |
| **Output Impedance** | Open-loop emitter follower | $18\text{ }\Omega$ | R7, Q3, Q4 |
| **Regulator Output** | Active-low $5\text{ V}$ relay supply | $V_Z - V_{BE1} - I_E R_E = 4.33\text{ V}$ ($5.05\text{ V}$ unloaded) | R1, D1, Q1, C2, C3 |
| **Protection Delay** | RC ramp to $V_{BE5(on)} \approx 0.66\text{ V}$ | $\tau = 4.7\text{ s} \rightarrow t_d = 0.27\text{ s}$ | R10, C11, Q5, J3 |

---

## Hardware Showcase

### Full Circuit Schematic (KiCad)
![Full Schematic](Images/schematic.png)

### 2D PCB Layout & 3D Renders
| 2D PCB Routing (Front & Back) | 3D Rendered PCB (Top & Bottom) |
| :---: | :---: |
| ![PCB Layout](Images/pcb_layout.png) | ![3D Render](Images/pcb_3d_render.png) |

### Manufactured Bare PCB & Assembled Prototype
| Manufactured Bare PCB (Front & Back) | Assembled Board & Relay Wiring |
| :---: | :---: |
| ![Bare PCB](Images/pcb_bare_front_back.jpg) | ![Assembled Prototype](Images/prototype_assembled.jpg) |

---

## Repository Structure

```text
├── KiCad_Design_Files/          # Native KiCad 10.0 schematic (.kicad_sch) and PCB (.kicad_pcb) files
├── Fabrication_Gerbers/         # Ready-to-manufacture Gerber and Excellon drill archive (.zip)
├── LTspice_Simulations/         # SPICE simulation schematics (.asc) for DC OP, Transient, and AC Sweep
├── Docs/                        # Full Romanian engineering report (PDF/DOCX) and printable schematic PDF
├── Images/                      # Block diagrams, LTspice plots, 3D renders, and hardware photos
├── README.md                    # Project overview and quick-start guide
├── Documentation.md             # Deep-dive theoretical analysis, equations, SOA, and SPICE verification
└── BOM.md                       # Categorized Bill of Materials
```

---

## Getting Started & Hardware Setup

1. **PCB Assembly:** Solder the components in ascending order of profile height:
   * $0.25\text{ W}$ resistors (`R1`–`R10`) and small-signal/Zener diodes (`D1`–`D3`).
   * Ceramic capacitors (`C1`, `C5`, `C7`, `C9`) and `TO-92` transistors (`Q2`, `Q5`).
   * Radial electrolytic capacitors (`C2`, `C3`, `C4`, `C6`, `C8`, `C10`, `C11`) — *strictly observe polarity markings on the silkscreen*.
   * Power transistors in `TO-220` packages (`Q1`, `Q3`, `Q4`).
2. **External Relay & Speaker Wiring:**
   * Connect `J3` (`J_Comanda_Releu`: `5V`, `GND`, `IN`) to the control header of a $5\text{ V}$ active-low relay module (`JQC-3FF-S-Z`).
   * Connect `J2` (`J_Audio`: `COM`, `NO`) to theNormally Open (NO) and Common (COM) screw terminals of the relay.
   * Connect the $8\text{ }\Omega$ loudspeaker to `J4` (`Boxa`).
3. **Power-Up:** Apply $12\text{ V}$ DC (minimum $250\text{ mA}$ capability) to `J5` (`Alimentare`). After $\approx 0.27\text{ s}$, the relay clicks, connecting the speaker without any start-up thump.

For complete mathematical derivations, thermal drift tables, and Safe Operating Area (SOA) verification, see **[Documentation.md](Documentation.md)**.

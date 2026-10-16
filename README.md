# 12V Single-Supply Class AB Audio Amplifier with Speaker Protection

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![EDA: KiCad 10](https://img.shields.io/badge/EDA-KiCad_10.0-green.svg)
![Simulation: LTspice](https://img.shields.io/badge/Simulation-LTspice-orange.svg)
![Technology: Discrete BJT THT](https://img.shields.io/badge/Hardware-Discrete_BJT_THT-red.svg)

**Author:** Buta Noris Ionuț  

---

## Overview

This repository contains the complete hardware design, mathematical analysis, SPICE simulation validation, and physical prototype implementation of a **discrete 12V single-supply Class AB audio amplifier** with an integrated **RC anti-thump speaker protection circuit** and **linear voltage regulator**.

Designed entirely without integrated circuits (utilizing 5 discrete bipolar junction transistors), the amplifier processes a stereo line-level input from a laptop, PC, or smartphone, sums it into a mono signal, and drives an $8\text{ }\Omega$ speaker from a standard $12\text{ V}$ DC power adapter. Because a single-rail supply biases the push-pull output stage at approximately half the supply rail ($6.76\text{ V}$ DC), a large $1000\text{ }\mu\text{F}$ output coupling capacitor is required. At power-up, charging this capacitor produces a severe transient spike ($11.6\text{ V}$ peak at $6.8\text{ ms}$). To protect the speaker voice coil and eliminate the audible turn-on "thump", an on-board RC delay circuit holds the speaker disconnected via an external relay for $\approx 0.27\text{ s}$ until the circuit settles into its DC quiescent operating point.

<p align="center">
  <img src="Images/Screenshot%202026-10-15%20194245.png" alt="Full Working Test Setup" width="850">
</p>

---

## Key Features

* **100% Discrete Analog Topology:** Built around 5 bipolar transistors (`2N2222` for small-signal amplification and switching, complementary `TIP31C` / `TIP32C` for power stages).
* **Stereo-to-Mono Input Mixer:** Passive resistive summing network ($1\text{ k}\Omega + 1\text{ k}\Omega$) providing $2\text{ k}\Omega$ inter-channel isolation and AC coupling.
* **High-Gain Voltage Amplifier Stage (VAS):** Common-emitter `2N2222` stage with AC-bypassed emitter degeneration ($A_{VAS} \approx 62$, overall $A_v \approx 51\times$ from the VAS base) and $100\text{ pF}$ Miller frequency compensation for RF immunity.
* **Diode-Biased Class AB Output Stage:** Complementary `TIP31C` (NPN) and `TIP32C` (PNP) emitter followers biased by two `1N4148` diodes ($I_Q = 8.2\text{ mA}$) to eliminate crossover dead-zones.
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
<p align="center">
  <img src="Images/Screenshot%202026-10-15%20193117.png" alt="KiCad Schematic" width="900">
</p>

### 2D PCB Layout & 3D Visualizations
| 2D PCB Routing (Front & Back) | 3D Rendered Top View |
| :---: | :---: |
| <img src="Images/Screenshot%202026-10-15%20193328.png" width="450"> | <img src="Images/Screenshot%202026-10-15%20193354.png" width="450"> |

| 2D PCB Traces | 3D Rendered Bottom View ("BUTA NORIS") |
| :---: | :---: |
| <img src="Images/Screenshot%202026-10-15%20193342.png" width="450"> | <img src="Images/Screenshot%202026-10-15%20193403.png" width="450"> |

### Physical Bare PCB & Assembled Prototype
| Manufactured Bare PCB (Front & Back) | Assembled Board Close-up |
| :---: | :---: |
| <img src="Images/Screenshot%202026-10-15%20194205.png" width="450"> | <img src="Images/Screenshot%202026-10-15%20194222.png" width="450"> |

---

## Repository Structure

```text
├── Docs/
│   ├── Documentatie_Romana.docx  # Comprehensive engineering report (Romanian)   
├── Fabrication_Gerbers/
│   └── gerber_files_final.zip                # Complete Gerber and Excellon drill production files
├── Images/                                   # High-resolution schematic captures, renders, and photos
├── KiCad_Design_Files/
│   ├── final.kicad_pro                       # KiCad 10.0 project file
│   ├── final.kicad_sch                       # KiCad schematic sheet
│   └── final.kicad_pcb                       # KiCad PCB layout file
├── LTspice_Simulations/
│   ├── Simulare_acBode.asc                   # AC frequency sweep simulation (.ac)
│   ├── Simulare_boxa.asc                     # Large-signal transient audio simulation (.tran)
│   └── Stabilizator_12_5.asc                 # Linear regulator DC operating point simulation (.op)
├── .gitignore                                # Git ignore configuration for KiCad & LTspice cache
├── LICENSE                                   # MIT Open-Source License
├── README.md                                 # Main project showcase and technical summary
├── bill_of_materials.md                     # Categorized component table (BOM)
└── hardware_design_documentation.md          # Theoretical circuit derivations, SOA & simulation data
```

---

## Getting Started & Hardware Setup

1. **PCB Assembly:** Solder components in ascending order of profile height:
   * $0.25\text{ W}$ metal film resistors (`R1`–`R10`) and small-signal/Zener diodes (`D1`–`D3`).
   * Ceramic disc capacitors (`C1`, `C5`, `C7`, `C9`) and `TO-92` transistors (`Q2`, `Q5`).
   * Radial electrolytic capacitors (`C2`, `C3`, `C4`, `C6`, `C8`, `C10`, `C11`) — *strictly observe polarity markings on the silkscreen*.
   * Power transistors in `TO-220` packages (`Q1`, `Q3`, `Q4`).
2. **External Relay & Speaker Wiring:**
   * Connect `J3` (`J_Comanda_Releu`: `5V`, `GND`, `IN`) to the control header of a standard $5\text{ V}$ active-low relay module (`JQC-3FF-S-Z`).
   * Connect `J2` (`J_Audio`: `COM`, `NO`) to the Normally Open (NO) and Common (COM) screw terminals of the relay module.
   * Connect an $8\text{ }\Omega$ loudspeaker to terminal `J4` (`Boxa`).
3. **Power-Up:** Apply $12\text{ V}$ DC (minimum $250\text{ mA}$ capability) to connector `J5` (`Alimentare`). After $\approx 0.27\text{ s}$, the relay clicks, connecting the speaker without any audible thump.

For complete mathematical derivations, thermal drift analysis, and Safe Operating Area (SOA) verification, see [**hardware_design_documentation.md**](hardware_design_documentation.md).  
For the complete component purchasing list, see [**bill_of_materials.md**](bill_of_materials.md).

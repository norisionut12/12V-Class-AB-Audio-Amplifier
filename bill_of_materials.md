# Bill of Materials (BOM)

**Project:** 12V Single-Supply Class AB Audio Amplifier with Speaker Protection  
**Author:** Buta Noris Ionuț  
**PCB Technology:** 2-Layer Through-Hole Technology (THT)

---

## 1. Semiconductors (Transistors & Diodes)

| Item | Quantity | Reference Designators | Value / Part Number | Package | Role in Circuit |
| :---: | :---: | :--- | :--- | :--- | :--- |
| 1 | 2 | `Q1`, `Q3` | `TIP31C` | `TO-220` | NPN Power Transistor ($100\text{ V}$, $3\text{ A}$) — Regulator Pass & Upper Push-Pull |
| 2 | 1 | `Q4` | `TIP32C` | `TO-220` | PNP Power Transistor ($100\text{ V}$, $3\text{ A}$) — Lower Push-Pull Output |
| 3 | 2 | `Q2`, `Q5` | `2N2222` | `TO-92` | NPN Small-Signal Transistor — VAS Amplifier & Relay Delay Switch |
| 4 | 1 | `D1` | `5V1` (`1N4733A`) | `DO-41` | $5.1\text{ V}$, $1\text{ W}$ Zener Diode — Regulator Voltage Reference |
| 5 | 2 | `D2`, `D3` | `1N4148` | `DO-35` | Fast Switching Diode — Class AB Thermal & Crossover Biasing |

---

## 2. Resistors ($0.25\text{ W}$ THT Axial)

| Item | Quantity | Reference Designators | Value | Role in Circuit |
| :---: | :---: | :--- | :--- | :--- |
| 6 | 1 | `R9` | $10\text{ }\Omega$ | Zobel Network Series Resistor |
| 7 | 1 | `R8` | $150\text{ }\Omega$ | VAS Emitter Degeneration / DC Bias Resistor |
| 8 | 1 | `R6` | $220\text{ }\Omega$ | Lower Bias Divider Fine-Tuning Resistor |
| 9 | 1 | `R1` | $330\text{ }\Omega$ | Zener Bias Resistor ($0.5\text{ W}$ rating optional for cooler operation) |
| 10 | 2 | `R2`, `R3` | $1\text{ k}\Omega$ | Stereo-to-Mono Input Summing Resistors (L+R) |
| 11 | 1 | `R7` | $2\text{ k}\Omega$ | VAS Collector Load & Upper Output Base Drive Resistor |
| 12 | 1 | `R5` | $10\text{ k}\Omega$ | Lower VAS Base Biasing Divider Resistor |
| 13 | 2 | `R4`, `R10` | $100\text{ k}\Omega$ | Upper VAS Base Biasing Divider (`R4`) & Protection RC Timer (`R10`) |

---

## 3. Capacitors (Ceramic & Radial Electrolytic)

| Item | Quantity | Reference Designators | Value | Type / Voltage Rating | Role in Circuit |
| :---: | :---: | :--- | :--- | :--- | :--- |
| 14 | 1 | `C7` | $100\text{ pF}$ | Ceramic ($\ge 50\text{ V}$) | VAS Miller Frequency Compensation Capacitor |
| 15 | 3 | `C1`, `C5`, `C9` | $100\text{ nF}$ | Ceramic ($\ge 25\text{ V}$) | High-Frequency Supply Decoupling (`C1`, `C5`) & Zobel Network (`C9`) |
| 16 | 1 | `C4` | $1\text{ }\mu\text{F}$ | Electrolytic ($\ge 16\text{ V}$) | Audio Input DC Blocking / AC Coupling Capacitor |
| 17 | 1 | `C2` | $10\text{ }\mu\text{F}$ | Electrolytic ($\ge 16\text{ V}$) | Zener Reference Noise Bypass Capacitor |
| 18 | 2 | `C8`, `C11` | $47\text{ }\mu\text{F}$ | Electrolytic ($\ge 16\text{ V}$) | VAS AC Emitter Bypass (`C8`) & Relay Delay Timing Capacitor (`C11`) |
| 19 | 1 | `C3` | $100\text{ }\mu\text{F}$ | Electrolytic ($\ge 16\text{ V}$) | Regulator Output Buffer Capacitor |
| 20 | 2 | `C6`, `C10` | $1000\text{ }\mu\text{F}$ | Electrolytic ($\ge 16\text{ V}$) | Main Power Supply Bulk Reservoir (`C6`) & Output DC Blocking (`C10`) |

---

## 4. Connectors & External Modules

| Item | Quantity | Reference Designators | Description | Role in Circuit |
| :---: | :---: | :--- | :--- | :--- |
| 21 | 1 | `J1` | 3-Pin Header / Direct Wire Pads | Stereo Audio Input (`Mufa Jack`: `L`, `R`, `GND`) |
| 22 | 1 | `J5` | 2-Pin Header / Direct Wire Pads | Main Power Supply Input (`Alimentare`: `12V`, `GND`) |
| 23 | 1 | `J3` | 3-Pin Header / Direct Wire Pads | Relay Control Interface (`J_Comanda_Releu`: `5V`, `GND`, `IN`) |
| 24 | 1 | `J2` | 2-Pin Header / Direct Wire Pads | Relay Contact Series Routing (`J_Audio`: `COM`, `NO`) |
| 25 | 1 | `J4` | 2-Pin Header / Direct Wire Pads | Loudspeaker Output (`Boxa`: `OUT`, `GND`) |
| 26 | 1 | External | `JQC-3FF-S-Z` ($5\text{ V}$ DC) | External Active-Low $5\text{ V}$ Relay Module ($10\text{ A}$ contact rating) |
| 27 | 1 | External | $8\text{ }\Omega$ Loudspeaker | Test & Playback Acoustic Load |
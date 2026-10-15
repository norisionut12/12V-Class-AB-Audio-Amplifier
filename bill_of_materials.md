# Bill of Materials (BOM)

**Project:** 12V Single-Supply Class AB Audio Amplifier with Speaker Protection  
**Author:** Buta Noris Ionuț  

---

## 1. Semiconductors

| Item | Reference | Quantity | Part / Value | Package | Description |
| :---: | :--- | :---: | :--- | :---: | :--- |
| 1 | Q1, Q3 | 2 | `TIP31C` | TO-220 | NPN Power Transistor (100V, 3A) |
| 2 | Q4 | 1 | `TIP32C` | TO-220 | PNP Power Transistor (100V, 3A) |
| 3 | Q2, Q5 | 2 | `2N2222` | TO-92 | NPN Small-Signal Transistor (40V, 600mA) |
| 4 | D1 | 1 | `5V1` (`1N4733A`) | DO-41 | 5.1V Zener Diode, 1W |
| 5 | D2, D3 | 2 | `1N4148` | DO-35 | High-Speed Switching Diode |

---

## 2. Resistors (Through-Hole, 0.25W, 5% or 1%)

| Item | Reference | Quantity | Resistance | Power Rating | Function in Circuit |
| :---: | :--- | :---: | :--- | :---: | :--- |
| 1 | R1 | 1 | $330\text{ }\Omega$ | $0.25\text{ W}$ (or $0.5\text{ W}$) | Zener bias resistor (Linear Regulator) |
| 2 | R2, R3 | 2 | $1\text{ k}\Omega$ | $0.25\text{ W}$ | Audio input summing mixer (L + R) |
| 3 | R4 | 1 | $100\text{ k}\Omega$ | $0.25\text{ W}$ | VAS base voltage divider (upper leg) |
| 4 | R5 | 1 | $10\text{ k}\Omega$ | $0.25\text{ W}$ | VAS base voltage divider (lower leg) |
| 5 | R6 | 1 | $220\text{ }\Omega$ | $0.25\text{ W}$ | VAS base voltage divider trim |
| 6 | R7 | 1 | $2\text{ k}\Omega$ | $0.25\text{ W}$ | VAS collector load & Q3 base pull-up |
| 7 | R8 | 1 | $150\text{ }\Omega$ | $0.25\text{ W}$ | VAS emitter degeneration resistor |
| 8 | R9 | 1 | $10\text{ }\Omega$ | $0.25\text{ W}$ | Zobel (Boucherot) damping resistor |
| 9 | R10 | 1 | $100\text{ k}\Omega$ | $0.25\text{ W}$ | Speaker protection RC timing resistor |

---

## 3. Capacitors

| Item | Reference | Quantity | Capacitance | Voltage | Dielectric / Type | Function in Circuit |
| :---: | :--- | :---: | :--- | :---: | :---: | :--- |
| 1 | C7 | 1 | $100\text{ pF}$ | $\ge 50\text{ V}$ | Ceramic Disc | VAS Miller frequency compensation |
| 2 | C1, C5, C9 | 3 | $100\text{ nF}$ ($0.1\text{ }\mu\text{F}$) | $\ge 25\text{ V}$ | Ceramic Disc | Supply decoupling & Zobel capacitor |
| 3 | C4 | 1 | $1\text{ }\mu\text{F}$ | $\ge 16\text{ V}$ | Electrolytic Radial | Audio input DC blocking capacitor |
| 4 | C2 | 1 | $10\text{ }\mu\text{F}$ | $\ge 16\text{ V}$ | Electrolytic Radial | Zener diode noise filter |
| 5 | C8, C11 | 2 | $47\text{ }\mu\text{F}$ | $\ge 16\text{ V}$ | Electrolytic Radial | VAS AC emitter bypass & Protection RC timer |
| 6 | C3 | 1 | $100\text{ }\mu\text{F}$ | $\ge 16\text{ V}$ | Electrolytic Radial | Regulator output transient reservoir |
| 7 | C6, C10 | 2 | $1000\text{ }\mu\text{F}$ | $\ge 16\text{ V}$ | Electrolytic Radial | Main rail decoupling & Speaker output DC block |

---

## 4. Connectors & External Modules

| Item | Reference | Quantity | Type | Specification / Notes |
| :---: | :--- | :---: | :--- | :--- |
| 1 | J1 | 1 | Pin Header (1x3, 2.54mm) | Audio input (`L`, `R`, `GND`) or direct 3.5mm jack wiring |
| 2 | J2 | 1 | Pin Header (1x2, 2.54mm) | Relay audio contact loop (`COM`, `NO`) |
| 3 | J3 | 1 | Pin Header (1x3, 2.54mm) | Relay module control (`5V`, `GND`, `IN`) |
| 4 | J4 | 1 | Pin Header (1x2, 2.54mm) | Loudspeaker output (`SPK+`, `GND`) |
| 5 | J5 | 1 | Pin Header (1x2, 2.54mm) | DC power supply input (`12V`, `GND`) |
| 6 | MODULE1 | 1 | Relay Module | External $5\text{ V}$ active-low relay module (`JQC-3FF-S-Z`) |
| 7 | SPK1 | 1 | Loudspeaker | $8\text{ }\Omega$ dynamic voice-coil speaker |
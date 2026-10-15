# Hardware Design & Engineering Documentation

**Project:** 12V Single-Supply Class AB Audio Amplifier with Speaker Protection  
**Author:** Buta Noris Ionuț  

---

## 1. System Architecture & Functional Blocks

The KiCad schematic is organized into 6 visual boxes representing **7 distinct functional blocks** (the main "Amplificator audio" box houses both the Voltage Amplifier Stage and the Class AB Output Stage).

![System Architecture](Images/Screenshot%202026-10-15%20193117.png)

| Block | Components | Function | Engineering Justification |
| :--- | :--- | :--- | :--- |
| **1. Audio Input (L+R Mixer)** | J1, R2, R3, C4 | Sums stereo L and R channels via two $1\text{ k}\Omega$ resistors; blocks DC via C4 | Drives a single speaker while providing $2\text{ k}\Omega$ isolation between left and right audio sources |
| **2. VAS (Voltage Amplifier Stage)** | Q2, R4, R5, R6, R8, C8, C7 | Converts millivolt base signals into hundreds of millivolts at the collector | Provides all voltage gain ($51\times$ from base); C7 limits high-frequency bandwidth for RF stability |
| **3. Class AB Output Stage** | R7, D2, D3, Q3, Q4, C5, C6 | Buffers VAS voltage and delivers current to the $8\text{ }\Omega$ load | Lowers output impedance; D2 and D3 eliminate Class B crossover dead-zone |
| **4. Output Network** | R9, C9, C10 | Zobel network at $159\text{ kHz}$; C10 blocks $6.76\text{ V}$ DC | Prevents high-frequency oscillation with inductive speaker coils; protects speaker from DC |
| **5. Speaker & Relay Connectors** | J2 (COM–NO), J4 | Places relay contact in series with the loudspeaker | Physically isolates the speaker until the start-up transient settles |
| **6. Linear Voltage Regulator** | C1, R1, D1, C2, Q1, C3 | Steps down $12\text{ V}$ to $4.33\text{ V}$ for the relay module | Protects the $5\text{ V}$ relay coil from $12\text{ V}$ overvoltage |
| **7. Speaker Protection** | R10, C11, Q5, J3 | Waits $\approx 0.27\text{ s}$ after power-up before pulling relay `IN` low | Keeps speaker disconnected while the output node spikes to $11.6\text{ V}$ and settles to $6.76\text{ V}$ |

---

## 2. Detailed Block-by-Block Analysis

### 2.1 Linear Voltage Regulator (`C1`, `R1`, `D1`, `C2`, `Q1`, `C3`)

The linear regulator generates $\approx 4.33\text{ V}$ from the $12\text{ V}$ rail exclusively for the external relay module using an NPN emitter follower (`Q1`, `TIP31C`) referenced to a $5.1\text{ V}$ Zener diode (`D1`, `1N4733A`).

#### DC Operating Point Calculation
`D1` operates in reverse breakdown at $\approx 20\text{ mA}$, where $V_Z = 5.05\text{ V}$ (simulated: $5.052\text{ V}$). With `Q1` drawing $I_{B1} = 0.77\text{ mA}$ into its base when the relay is active:

$$I_{R1} = \frac{V_{CC} - V_Z}{R_1} = \frac{12 - 5.05}{330\text{ }\Omega} = 21.1\text{ mA}$$

$$I_Z = I_{R1} - I_{B1} = 21.1\text{ mA} - 0.77\text{ mA} = 20.3\text{ mA}$$

With the relay energized, the module draws $I_{E1} = 66.4\text{ mA}$. At this current, $V_{BE1} = 0.68\text{ V}$, and the internal emitter resistance of the `TIP31C` ($R_E = 0.56\text{ }\Omega$) drops an additional $0.04\text{ V}$:

$$V_{5V} = V_Z - V_{BE1} - I_{E1}R_E = 5.05\text{ V} - 0.68\text{ V} - 0.04\text{ V} = 4.33\text{ V}$$

$$P_{Q1} \approx (12\text{ V} - 4.33\text{ V}) \cdot 66.4\text{ mA} = 0.51\text{ W}$$

* **Unloaded Behavior:** Without the relay connected, $I_{E1} \approx 0$, $V_{BE1} \approx 0$, and the output rises to the Zener voltage ($5.05\text{ V}$).
* **Relay Pull-In Margin:** `JQC-3FF` series $5\text{ V}$ relays are guaranteed to pull in at $\le 75\%$ of nominal coil voltage ($3.75\text{ V}$), making $4.33\text{ V}$ completely reliable while reducing coil heating.
* **Capacitor Roles:** `C1` ($100\text{ nF}$ ceramic) decouples high frequencies at the input; `C2` ($10\text{ }\mu\text{F}$) shunts Zener noise above $\approx 2.3\text{ kHz}$ (pole formed with $R_1 \parallel r_z \approx 6.9\text{ }\Omega$); `C3` ($100\text{ }\mu\text{F}$) supplies transient current when the relay coil switches.
* **Thermal Verification:** Without a heatsink, `Q1` experiences a temperature rise of $0.50\text{ W} \cdot 62.5^\circ\text{C/W} \approx 31^\circ\text{C}$, reaching $\approx 56^\circ\text{C}$ at $25^\circ\text{C}$ ambient (well below the $150^\circ\text{C}$ maximum junction rating).

---

### 2.2 Audio Input – Stereo-to-Mono Mixer (`J1`, `R2`, `R3`, `C4`)

`R2` and `R3` ($1\text{ k}\Omega$ each) sum the Left and Right audio channels into a single node (`MIX`), while `C4` ($1\text{ }\mu\text{F}$) blocks the $1.01\text{ V}$ DC bias present at the base of `Q2`. Directly shorting L and R would cause the two output drivers of the audio source to fight each other; using $1\text{ k}\Omega$ resistors provides $2\text{ k}\Omega$ of series isolation between channels. Seen from the amplifier input, the mixer acts as a Thévenin source with $R_2 \parallel R_3 = 500\text{ }\Omega$:

$$V_{MIX} = \frac{V_L + V_R}{2} \cdot \frac{R_{in}}{R_{in} + (R_2 \parallel R_3)}, \quad R_2 \parallel R_3 = 500\text{ }\Omega$$

---

### 2.3 Voltage Amplifier Stage (VAS) – Biasing & Thermal Drift (`R4`, `R5`, `R6`, `Q2`, `R8`, `C8`)

`Q2` (`2N2222`) operates as a common-emitter amplifier and provides all voltage gain in the circuit. The voltage divider `R4` ($100\text{ k}\Omega$) and `R5` + `R6` ($10\text{ k}\Omega + 220\text{ }\Omega = 10.22\text{ k}\Omega$) sets the base voltage, `R8` ($150\text{ }\Omega$) stabilizes the emitter current, and `Q2`'s collector current flows through `R7` ($2\text{ k}\Omega$) and biasing diodes `D2`–`D3`, simultaneously establishing the DC operating point of the output stage.

#### Hand Calculation vs. SPICE Simulation

$$V_{TH} = 12\text{ V} \cdot \frac{10.22\text{ k}\Omega}{110.22\text{ k}\Omega} = 1.113\text{ V}, \quad R_{TH} = 100\text{ k}\Omega \parallel 10.22\text{ k}\Omega = 9.27\text{ k}\Omega$$

$$I_{C2} \approx \frac{V_{TH} - V_{BE2}}{R_8 + R_{TH}/(\beta_2 + 1)} = \frac{1.113\text{ V} - 0.675\text{ V}}{150\text{ }\Omega + 9270\text{ }\Omega / 210} = 2.26\text{ mA}$$

$$V_{OUT} = V_{CC} - I_{R7}R_7 - V_{BE3} = 12\text{ V} - 2.30\text{ mA} \cdot 2\text{ k}\Omega - 0.63\text{ V} = 6.77\text{ V}$$

| Parameter / Node | Hand Calculated | LTspice Simulated |
| :--- | :---: | :---: |
| **Base of Q2** | $1.01\text{ V}$ | $1.013\text{ V}$ |
| **Emitter of Q2 ($V_{R8}$)** | $0.34\text{ V}$ | $0.337\text{ V}$ |
| **Collector Current ($I_{C2}$)** | $2.26\text{ mA}$ | $2.24\text{ mA}$ |
| **Voltage Drop Across R7** | $4.64\text{ V}$ | $4.59\text{ V}$ |
| **Collector of Q2 (VAS)** | $6.12\text{ V}$ | $6.16\text{ V}$ |
| **Diode Bias ($V_{D2} + V_{D3}$)** | $1.24\text{ V}$ | $1.24\text{ V}$ |
| **DC Output Node (`OUT`)** | $6.73\text{ V}$ | $6.76\text{ V}$ |
| **Collector-Emitter Voltage ($V_{CE2}$)** | $5.78\text{ V}$ | $5.82\text{ V}$ |

#### Thermal Drift Analysis
Because $V_{BE2}$ decreases by $\approx 2\text{ mV/}^\circ\text{C}$, $I_{C2}$ increases with temperature, increasing the drop across $R_7$:

$$\frac{dV_{OUT}}{dT} \approx -\frac{R_7}{R_8 + R_{TH}/\beta_2} \cdot \left|\frac{dV_{BE}}{dT}\right| = -\frac{2000\text{ }\Omega}{194\text{ }\Omega} \cdot 2\text{ mV/}^\circ\text{C} \approx -21\text{ mV/}^\circ\text{C}$$

| Board Temperature | $I_{C2}$ | $V(\text{OUT})$ | $I_Q$ (`Q3`/`Q4`) | Voltage Gain | $V(\text{5V})$ |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **$0^\circ\text{C}$** | $1.96\text{ mA}$ | $7.29\text{ V}$ | $6.4\text{ mA}$ | $30.8\text{ dB}$ | $4.29\text{ V}$ |
| **$27^\circ\text{C}$** | $2.24\text{ mA}$ | $6.76\text{ V}$ | $8.2\text{ mA}$ | $32.1\text{ dB}$ | $4.33\text{ V}$ |
| **$50^\circ\text{C}$** | $2.48\text{ mA}$ | $6.31\text{ V}$ | $10.0\text{ mA}$ | $33.0\text{ dB}$ | $4.36\text{ V}$ |
| **$70^\circ\text{C}$** | $2.70\text{ mA}$ | $5.91\text{ V}$ | $11.6\text{ mA}$ | $33.8\text{ dB}$ | $4.39\text{ V}$ |

---

### 2.4 VAS Gain, Output Impedance & Miller Compensation (`Q2`, `C8`, `C7`)

`C8` ($47\text{ }\mu\text{F}$) bypasses `R8` in AC, so the small-signal voltage gain is set by `Q2`'s transconductance ($g_{m2}$) and the parallel combination of $R_7$ and the reflected input impedance of the output stage ($Z_{in,fin}$):

$$g_{m2} = \frac{I_{C2}}{V_T} = \frac{2.24\text{ mA}}{25.9\text{ mV}} = 86.5\text{ mS}$$

$$A_{VAS} \approx g_{m2}(R_7 \parallel Z_{in,fin}) = 86.5\text{ mS} \cdot 718\text{ }\Omega \approx 62, \quad A_v \approx 62 \cdot \frac{8\text{ }\Omega}{9.6\text{ }\Omega} \approx 52$$

#### Output Impedance
Viewed from the speaker terminal, the reflected resistance of $R_7$ divided by the effective current gain ($\beta_{ech}$) of the output pair sets the open-loop output impedance:

$$Z_{out} \approx \frac{R_7}{\beta_{ech}} + \frac{r_e}{2} \approx \frac{2000\text{ }\Omega}{117} + 1.6\text{ }\Omega \approx 18.7\text{ }\Omega$$

#### Miller Compensation (`C7` = $100\text{ pF}$)
Connected between the collector and base of `Q2`, `C7` is multiplied by the Miller effect ($1 + |A_{VAS}| = 63$):

$$C_{in,M} = C_7(1 + |A_{VAS}|) = 100\text{ pF} \cdot 63 = 6.3\text{ nF}$$

$$f_M = \frac{1}{2\pi (R_s \parallel R_{in}) C_{in,M}} = \frac{1}{2\pi \cdot 400\text{ }\Omega \cdot 6.3\text{ nF}} \approx 63\text{ kHz}$$

Combined with the $\beta$ roll-off pole of the power transistors ($f_T \approx 6.7\text{ MHz}$, $f_\beta = f_T / \beta_{ech} \approx 57\text{ kHz}$), `C7` maintains the overall $-3\text{ dB}$ bandwidth at $31\text{ kHz}$ while rejecting RF interference picked up by the input leads.

---

### 2.5 Class AB Output Stage (`R7`, `D2`, `D3`, `Q3`, `Q4`) & Decoupling (`C5`, `C6`)

`Q3` (`TIP31C`, NPN) and `Q4` (`TIP32C`, PNP) form a complementary push-pull emitter follower. Without biasing (Class B), neither transistor conducts for input swings within $\pm 0.6\text{ V}$, causing severe crossover distortion. Passing the $2.24\text{ mA}$ VAS current through two `1N4148` diodes (`D2`, `D3`) generates a voltage drop matching the two base-emitter junctions, keeping both power transistors quiescently conducting at $I_Q = 8.2\text{ mA}$:

$$V_{D2} + V_{D3} = 1.242\text{ V} \approx V_{BE3} + V_{EB4} = 0.630\text{ V} + 0.600\text{ V} = 1.230\text{ V}$$

`C6` ($1000\text{ }\mu\text{F}$) acts as the local bulk energy reservoir for bass transients, while `C5` ($100\text{ nF}$) decouples high-frequency supply impedance right next to the collectors.

---

### 2.6 Output Network (`R9`, `C9`, `C10`)

* **Zobel (Boucherot) Network (`R9` + `C9`):** Loudspeaker voice coils are inductive, causing their impedance to rise at high frequencies and potentially triggering parasitic oscillations in emitter followers. `R9` ($10\text{ }\Omega$) and `C9` ($100\text{ nF}$) maintain a resistive load above:

$$f_{Zobel} = \frac{1}{2\pi R_9 C_9} = \frac{1}{2\pi \cdot 10\text{ }\Omega \cdot 100\text{ nF}} = 159\text{ kHz}, \quad |Z(20\text{ kHz})| = \sqrt{10^2 + 79.6^2} \approx 80\text{ }\Omega$$

* **DC Blocking Capacitor (`C10`):** A $1000\text{ }\mu\text{F}$ electrolytic capacitor blocks the $6.76\text{ V}$ DC quiescent voltage from reaching the $8\text{ }\Omega$ speaker coil while passing AC audio signals.

---

### 2.7 Speaker Protection & Turn-On Delay (`R10`, `C11`, `Q5`, `J3`, `J2`, `J4`)

At power-up, `Q2` is initially cut off for the first few milliseconds until `C4` charges through $R_{TH}$ ($\tau \approx 9\text{ ms}$). During this interval, `R7` pulls the output node up to **$11.6\text{ V}$ at $6.8\text{ ms}$**, after which it decays to $6.76\text{ V}$. Without a relay delay, this $+11.6\text{ V}$ spike and subsequent $4.8\text{ V}$ drop would drive a heavy transient current through the speaker.

`R10` ($100\text{ k}\Omega$) charges `C11` ($47\text{ }\mu\text{F}$) exponentially toward $12\text{ V}$. When $v_{C11}(t)$ reaches $V_{BE5(on)} \approx 0.66\text{ V}$, `Q5` (`2N2222`) saturates and pulls pin 3 (`IN`) of `J3` to ground, energizing the active-low relay:

$$\tau = R_{10}C_{11} = 100\text{ k}\Omega \cdot 47\text{ }\mu\text{F} = 4.7\text{ s}, \quad v_{C11}(t) = V_{CC}\left(1 - e^{-t/\tau}\right)$$

$$t_d = -\tau \ln\left(1 - \frac{V_{BE5(on)}}{V_{CC}}\right) = -4.7\text{ s} \cdot \ln\left(1 - \frac{0.66\text{ V}}{12\text{ V}}\right) \approx 0.266\text{ s}$$

---

## 3. LTspice Simulation Verification

### 3.1 Linear Voltage Regulator (`.op`)
In the unloaded `.op` simulation, $I_C(Q_1) = 7\text{ pA}$, so `Q1`'s base-emitter junction is essentially zero-biased and $V(\text{out}) = 5.052\text{ V}$, while all $21.05\text{ mA}$ through `R1` flow into the Zener diode `D1`.

### 3.2 Amplifier Transient (`.tran`) and AC Sweep (`.ac`)
Driven with a $10\text{ mV}$ peak sine wave at $1\text{ kHz}$ directly at the input coupling capacitor:
* **Transient Response (`0–400 ms` & `380–400 ms`):** The output across the $8\text{ }\Omega$ load stabilizes into a clean waveform swinging between $+0.40\text{ V}$ and $-0.55\text{ V}$ (asymmetry is normal for a single-ended VAS pull-up resistor $R_7$, which clips the positive half-cycle earlier at $+1.35\text{ V}$ vs. $-4.10\text{ V}$ on the negative excursion).
* **AC Frequency Response (`.ac`):** Demonstrates a mid-band gain of $\approx 19.5\text{ dB}$ in the simplified test schematic (and $32.1\text{ dB}$ / $40\times$ in the full circuit configuration), with a phase of $-160^\circ$ at $1\text{ kHz}$ and a $-3\text{ dB}$ passband from $301\text{ Hz}$ to $31\text{ kHz}$.

| Transient Output (`0–400 ms`) | Steady-State Sine Wave (`380–400 ms`) |
| :---: | :---: |
| ![Transient Full](Images/Screenshot%202026-10-15%20222015.png) | ![Transient Zoom](Images/Screenshot%202026-10-15%20222034.png) |

#### AC Frequency Respons (Bode Plot)
![Bode Plot](Images/Screenshot%202026-10-16%20012844.png)
---

## 4. Safe Operating Area (SOA) Verification

All components operate well within their datasheet ratings across DC quiescent conditions, $1\text{ kHz}$ sine wave up to line-level clipping, power-up, and power-down transients:

| Component | Max Current | Max Voltage | Average / Peak Power | Datasheet Limit |
| :--- | :---: | :---: | :---: | :---: |
| **Q1 (`TIP31C`)** | $66\text{ mA}$ ($238\text{ mA}$ start-up) | $9.5\text{ V}$ | $0.50\text{ W}$ / $2.2\text{ W}$ | $3\text{ A}$, $100\text{ V}$, $2\text{ W}$ (no heatsink) |
| **Q2 (`2N2222`)** | $10\text{ mA}$ | $11.9\text{ V}$ | $13\text{ mW}$ / $17\text{ mW}$ | $600\text{ mA}$, $40\text{ V}$, $625\text{ mW}$ |
| **Q3 (`TIP31C`)** | $216\text{ mA}$ (start), $167\text{ mA}$ (signal) | $10.2\text{ V}$ | $0.53\text{ W}$ / $2.1\text{ W}$ | $3\text{ A}$, $100\text{ V}$, $2\text{ W}$ (no heatsink) |
| **Q4 (`TIP32C`)** | $503\text{ mA}$ | $11.6\text{ V}$ | $0.30\text{ W}$ / $1.1\text{ W}$ | $3\text{ A}$, $100\text{ V}$, $2\text{ W}$ (no heatsink) |
| **Q5 (`2N2222`)** | $2.8\text{ mA}$ | $4.5\text{ V}$ | $0.3\text{ mW}$ / $2\text{ mW}$ | $600\text{ mA}$, $40\text{ V}$ |
| **D1 (`5V1` Zener)** | $21\text{ mA}$ | $5.05\text{ V}$ | $0.10\text{ W}$ | $1\text{ W}$ (`1N4733A`) |
| **D2, D3 (`1N4148`)** | $5.1\text{ mA}$ | $1.9\text{ V}$ reverse (shut-off) | $1.4\text{ mW}$ | $200\text{ mA}$, $100\text{ V}$ |
| **R1 ($330\text{ }\Omega$)** | $21\text{ mA}$ | $6.95\text{ V}$ | $0.15\text{ W}$ / $0.25\text{ W}$ | $0.25\text{ W}$ ($0.5\text{ W}$ recommended) |
| **R7 ($2\text{ k}\Omega$)** | $5\text{ mA}$ | $10\text{ V}$ | $19\text{ mW}$ / $52\text{ mW}$ | $0.25\text{ W}$ |
| **R9, R10** | — | — | $< 3\text{ mW}$ | $0.25\text{ W}$ |
| **C1, C5, C6** | — | $12\text{ V}$ | — | $\ge 16\text{ V}$ |
| **C7 ($100\text{ pF}$)** | — | $11.5\text{ V}$ (at start-up) | — | $50\text{ V}$ |
| **C9 ($100\text{ nF}$)** | — | $11.6\text{ V}$ (at start-up) | — | $\ge 25\text{ V}$ |
| **C10 ($1000\text{ }\mu\text{F}$)** | $0.51\text{ A}$ peak | $6.8\text{ V}$ | — | $\ge 16\text{ V}$ |
| **Relay Contact** | $0.51\text{ A}$ | $4.1\text{ V}$ | — | $10\text{ A}$ |

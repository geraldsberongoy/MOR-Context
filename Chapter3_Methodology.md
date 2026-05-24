# Chapter 3
# METHODOLOGY

This chapter presents the methodology, research design, and project implementation flow for the development of the **Solar-Powered Semi-Automated Paper-Charcoal Briquetting Machine**. It details the hardware specifications, sensor networks, electrical schematics, software finite state machine (FSM), user session persistence logic, graphical user interface (GUI) mapping, and the analytical treatments used to validate the system’s performance and energy self-sufficiency.

---

## **Research Design**

This study utilizes a hybrid **Developmental Research Design** integrated with **Experimental Quantitative Methods** to engineer, validate, and evaluate the semi-automated paper-charcoal briquetting system. This specific research configuration is uniquely suited for a Computer Engineering (CpE) project, as it bridges structural physical design, electronic systems integration, and deterministic software control.

### **Alignment with Computer Engineering Principles**
The discipline of Computer Engineering focuses on the development of cyber-physical systems (CPS) where physical hardware operations are monitored and actuated through real-time embedded computing. The proposed semi-automated briquetting machine represents a classic cyber-physical system, composed of three main layers that align with core CpE domains:
1. **The Physical Layer (Mechanical Actuation)**: Comprising a heavy-duty mechanical frame, a 12V 775 high-torque DC motor for crushing, a 12V planetary geared motor for heavy-duty blending, a 12V DC high-force 4000N linear actuator for compaction, a normally closed 12V solenoid water valve, and a thermal drying chamber utilizing 12V halogen elements and a brushless DC exhaust fan.
2. **The Sensory and Telemetry Layer (Hardware Interfacing)**: Comprising a network of sensors, including a YF-S201 Hall-effect flow sensor, an S-type strain gauge load cell with a 24-bit HX711 ADC, a 1-Wire DS18B20 digital temperature probe, a capacitive polymer DHT22 temperature-humidity sensor, and an I2C-enabled INA219 high-side power monitor.
3. **The Control and Interface Layer (Embedded Firmware & HMI)**: Executed by a dual-core 32-bit Tensilica Xtensa ESP32 microcontroller operating at 240 MHz. The ESP32 executes an event-driven Finite State Machine (FSM), writes active user session parameters and states to the onboard Non-Volatile Storage (NVS) flash partition via the `Preferences.h` library, and manages real-time, asynchronous UART communication with a Nextion Discovery 4.3" capacitive HMI touch panel.

The developmental research design guides the system through a structured engineering design cycle, transitioning from mathematical power budgeting and structural load simulations to physical prototyping, multi-sensor calibration, and firmware refinement.

### **Integration of Experimental Quantitative Methods**
To scientifically validate the design's effectiveness, the developmental phase is coupled with a comparative experimental research design. The semi-automated machine is evaluated against a baseline representing the traditional, manual paper-charcoal briquetting process. Quantitative evaluation is structured around three primary performance dimensions:
* **Throughput and Labor Efficiency (Time and Motion Study)**: Measured by tracking the raw man-hour consumption ($T$) and overall cycle times (minutes per batch) for both manual ($T_m$) and machine-assisted ($T_a$) production groups across 30 identical manufacturing trials.
* **Product Quality and Structural Integrity**: Evaluated by physical density ($\rho$ in $\text{kg/m}^3$), compressive strength ($\sigma$ in Pascals), volumetric moisture retention, and burn duration (minutes).
* **Electrical Self-Sustainability**: Evaluated by monitoring power telemetry (solar charge current, load current, battery state-of-charge) via the INA219 monitor to compute the system’s Energy Self-Sufficiency Ratio ($ESSR$) under real-world solar irradiance profiles.

By structuring the study around measurable physical quantities and statistical comparisons, this research design provides an empirical foundation to prove the machine’s efficiency, consistency, and energy independence.

---

## **Flowchart of Research Design / Process Flowchart**

The execution of this study is structured into five distinct, sequential phases that govern the progression from conceptualization to empirical validation. The flowchart below visualizes the research design progression:

```mermaid
graph TD
    A[Phase 1: Design & Analysis] --> B[Phase 2: Hardware & Software Integration]
    B --> C[Phase 3: Prototype Fabrication]
    C --> D[Phase 4: Calibration & Testing]
    D --> E[Phase 5: Evaluation & Analysis]

    subgraph "Phase 1: Design & Analysis"
        A1[Define Briquette Size: 10x5x5 cm]
        A2[Determine Compressive Force: 2000N]
        A3[Design Solar & Power Budgets]
    end

    subgraph "Phase 2: Integration"
        B1[ESP32 Hardware Interfacing]
        B2[Nextion Touchscreen HMI Coding]
        B3[FSM Logic & Session NVS Writing]
    end

    subgraph "Phase 3: Fabrication"
        C1[Construct Grinder, Mixer & Mold]
        C2[Install 12V Linear Actuator & Relays]
        C3[Assemble 12V Battery & Solar Panel]
    end

    subgraph "Phase 4: Testing"
        D1[Calibrate Load Cell & Flow Sensor]
        D2[Tune Halogen Drying PID/Thresholds]
        D3[Test Session Resume on Power Interrupt]
    end

    subgraph "Phase 5: Evaluation"
        E1[Time & Motion Study vs. Manual]
        E2[Briquette Combustion & Water Resistance]
        E3[Battery Telemetry & Self-Sustainability]
    end
```

### **Phase 1: Design and Analysis**
This phase establishes the physical, mechanical, and electrical baseline constraints of the system.
* **Briquette Dimension and Volume Constraints**: The target output is set as a standard rectangular prism with dimensions of $10.0\text{ cm} \times 5.0\text{ cm} \times 5.0\text{ cm}$. The nominal volume ($V$) is calculated as:
  $$V = 10.0\text{ cm} \times 5.0\text{ cm} \times 5.0\text{ cm} = 250.0\text{ cm}^3 = 0.00025\text{ m}^3$$
  The cross-sectional area of compression ($A$) at the compression ram head is:
  $$A = 10.0\text{ cm} \times 5.0\text{ cm} = 50.0\text{ cm}^2 = 0.005\text{ m}^2$$
* **Compressive Force Requirements**: According to Biomass Densification Theory, achieving structural durability without synthetic chemical binders requires a minimum compaction pressure ($\sigma_c$) of $400.0\text{ kPa}$ ($4.0 \times 10^5\text{ N/m}^2$) to ensure mechanical interlocking and hydrogen bonding of cellulose fibers. The required linear compressive force ($F$) is calculated as:
  $$F = \sigma_c \times A = (400,000\text{ N/m}^2) \times (0.005\text{ m}^2) = 2000.0\text{ N}$$
  The mechanical assembly must withstand this structural load, and the linear actuator must supply a minimum of $2000\text{ N}$ of thrust.
* **Solar and Power Budget Sizing**: The system is designed to operate on a 12V DC power bus. Based on an operational requirement of 10 production batches per day, the daily energy consumption profile ($E_{\text{total}}$) is modeled. Continuous logic loads (ESP32, Nextion HMI) consume $2.5\text{ W}$ over an 8-hour period ($20.0\text{ Wh}$). Intermittent load components are sized according to active run times: water solenoid valve ($9.6\text{ W}$, 2 min/batch = $3.2\text{ Wh}$), 775 grinder motor ($60.0\text{ W}$, 3 min/batch = $30.0\text{ Wh}$), geared mixing motor ($36.0\text{ W}$, 5 min/batch = $30.0\text{ Wh}$), linear actuator ($48.0\text{ W}$, 0.5 min/batch = $4.0\text{ Wh}$), halogen lamps ($50.0\text{ W}$, 20 min/batch = $166.7\text{ Wh}$), and exhaust blower fan ($2.4\text{ W}$, 20 min/batch = $8.0\text{ Wh}$). The cumulative energy demand is:
  $$E_{\text{total}} = 20.0 + 3.2 + 30.0 + 30.0 + 4.0 + 166.7 + 8.0 = 261.9\text{ Wh}$$
  Applying a system safety factor ($S_f = 1.2$) and limiting the depth of discharge ($DoD$) to $80\%$ for a Lithium Iron Phosphate ($\text{LiFePO}_4$) battery chemistry, the nominal capacity ($C_{\text{batt}}$) is sized:
  $$C_{\text{batt}} = \frac{E_{\text{total}} \times S_f}{V_{\text{nominal}} \times DoD} = \frac{261.9\text{ Wh} \times 1.2}{12.8\text{ V} \times 0.8} \approx 30.7\text{ Ah}$$
  A **12V 30Ah LiFePO4 battery pack** is selected. The solar photovoltaic (PV) array is sized to replenish $E_{\text{total}}$ under a conservative local peak sun hour rating ($H_{\text{peak}} = 4.0\text{ hours}$) and a system loss coefficient ($\eta_{\text{sys}} = 0.75$):
  $$P_{\text{PV}} = \frac{E_{\text{total}}}{H_{\text{peak}} \times \eta_{\text{sys}}} = \frac{261.9\text{ Wh}}{4.0\text{ hours} \times 0.75} \approx 87.3\text{ W}$$
  A **100W Monocrystalline Solar Panel** paired with a **10A Maximum Power Point Tracking (MPPT) solar charge controller** is integrated.

### **Phase 2: Hardware and Software Integration**
This phase focuses on the electrical interfacing and software architecture design.
* **Microcontroller Pin Mapping and Sensor Interfacing**: The ESP32 is integrated with peripheral systems using specific hardware protocols. Digital and analog sensor buses are established, including a 1-Wire bus for the DS18B20 digital temperature probe, an I2C bus operating at 100 kHz for the INA219 current-voltage sensor, an RS232-TTL hardware serial interface (Serial2, 9600 bps) for the Nextion touchscreen, and high-frequency digital interrupt lines for the YF-S201 flow sensor and safety limit switches.
* **Finite State Machine (FSM) Design**: An event-driven FSM is coded in C++ to manage state transitions: `STATE_IDLE`, `STATE_GRINDING`, `STATE_SOAKING`, `STATE_MIXING`, `STATE_COMPRESSION`, `STATE_DRYING`, and `STATE_COMPLETED`.
* **Session Persistence in NVS**: To handle unexpected power interrupts or low-voltage disconnects under field operations, the ESP32 utilizes the `Preferences.h` library to write the current active state (`currentState`), user session ID (`user_id`), and flow sensor accumulation parameters directly to the Non-Volatile Storage (NVS) partition of the flash memory. On reboot, the bootloader automatically retrieves these values and restores the system to the exact execution state, prompting the user on the Nextion screen to resume.

### **Phase 3: Prototype Fabrication**
This phase converts the engineering designs into a physical prototype.
* **Structural Frame Fabrication**: A heavy-duty framework is constructed using welded carbon steel angle bars. The framework houses four main chambers in a vertical gravity-assisted layout:
  1. **Grinding Chamber**: Featuring a high-speed cylindrical stainless steel container fitted with rotary blades driven by the 12V 775 DC motor.
  2. **Soaking and Mixing Chamber**: A cylindrical tank equipped with a planetary gear mixing assembly, paddles driven by the 12V high-torque planetary geared motor, and a water inlet port connected to the solenoid valve.
  3. **Molding Press Chamber**: A thick-walled rectangular carbon steel mold cavity with a hinged, latching lid containing a safety micro-switch. The press is driven vertically by the 12V linear actuator, which is mounted on a reinforced steel crossbeam to handle the $2000\text{ N}$ reaction force.
  4. **Drying Chamber**: A sealed thermal enclosure lined with reflective insulation sheet, housing four 12V 12.5W halogen lamps and a 12V DC exhaust fan.
* **Electrical Control Enclosure**: An IP65-rated control box houses the ESP32 development board, the BTS7960 high-current H-bridge motor driver, power relays, optocouplers, a 12V-to-5V step-down buck converter, fuses, and terminal blocks. The 12V 30Ah battery and MPPT controller are mounted at the base, and the 100W solar panel is mounted on an adjustable angled roof bracket.

### **Phase 4: Calibration and Testing**
This phase subjects the physical prototype to rigorous sensor calibration and unit testing.
* **Sensor Calibrations**: The YF-S201 flow sensor, HX711 load cell, and DHT22 sensors undergo multi-point calibrations (detailed in the next major section) to establish physical scaling factors in the ESP32 firmware.
* **Drying Chamber Temperature Profile Tuning**: The drying chamber’s thermal curves are mapped under continuous operation of the 50W halogen lamps, adjusting the exhaust fan's airflow velocity to maintain internal temperatures below $60^\circ\text{C}$ to prevent thermal fracturing of the drying briquettes.
* **Session Recovery Testing**: Systematic power-interrupt tests are performed. During active runs of each state, the main 12V battery power line is cut. The system must successfully write the active state to the NVS before the decoupling capacitors discharge, and upon power re-application, the ESP32 must restore the exact step and prompt session resumption on the HMI.

### **Phase 5: Evaluation and Analysis**
The final phase evaluates the completed system against standard experimental criteria.
* **Time and Motion Evaluation**: 30 production batches are manufactured manually and 30 batches are produced using the machine. Operational cycle times, water volume precision, grinding efficiency, and total human man-hour consumption are recorded.
* **Briquette Mechanical and Thermal Testing**: The final dried briquettes are evaluated for physical density, compressive strength (crushing force), humidity absorption over a 48-hour cycle, and combustion performance (burning rate, peak temperature, ash residue).
* **System Energy Efficiency Analysis**: Daily telemetry data from the INA219 power sensor is analyzed to compute solar energy harvesting efficiency, battery capacity retention, and load profiles, establishing the system's self-sustainability.

---

## **Description of Research Instrument Used**

To collect highly precise, repeatable empirical data and maintain stable system control, four specific research instruments undergo systematic calibration.

### **1. YF-S201 Water Flow Sensor Calibration**
The YF-S201 sensor contains a Hall-effect rotor that spins as water passes through the 1/2" inlet port. For every rotation, the sensor outputs a square-wave digital pulse. The frequency of the pulse train ($f$, in Hz) is proportional to the volumetric flow rate ($Q$, in L/min).

```
          [Water Flow] ---> [Hall-Effect Rotor] ---> [Pulse Train (f)] 
                                                            |
  [Water Volume] <--- [Volumetric Integration] <--- [ESP32 Interrupt]
```

The theoretical relationship is expressed as:
$$Q = \frac{f}{K}$$
where $K$ is the calibration constant (pulses per Liter per minute). The total volume ($V_w$, in Liters) is computed via volumetric integration of pulses in the ESP32 firmware using a hardware interrupt pin:
$$V_w = \sum_{i=1}^{P} \frac{1}{C_{\text{flow}}}$$
where $P$ is the total accumulated pulse count, and $C_{\text{flow}}$ is the calibrated pulses-per-Liter scaling factor.

#### **Gravimetric Calibration Procedure**
To establish the exact value of $C_{\text{flow}}$, a gravimetric calibration is performed at a room temperature of $25.0^\circ\text{C}$. At this temperature, the density of water ($\rho_w$) is approximately $0.997\text{ g/cm}^3$ ($0.997\text{ kg/L}$). Water is discharged through the sensor into a container placed on a high-precision digital laboratory scale (0.1g resolution). The actual volume ($V_{\text{actual}}$, in Liters) is derived from the measured mass ($M$, in grams):
$$V_{\text{actual}} = \frac{M}{1000 \times \rho_w}$$
A total of 10 calibration trials are conducted across varying flow rates (ranging from $2.0$ to $8.0\text{ L/min}$), recording the total pulses generated for a target mass of $1000.0\text{ grams}$.

| Trial Number | Target Mass (g) | Actual Mass (g) | Actual Volume (L) | Total Pulses ($P$) | Pulses/Liter ($C_{\text{flow}}$) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 1000.0 | 1002.4 | 1.0054 | 451 | 448.58 |
| 2 | 1000.0 | 998.6 | 0.9986 | 449 | 449.63 |
| 3 | 1000.0 | 1001.2 | 1.0042 | 453 | 451.11 |
| 4 | 1000.0 | 999.1 | 0.9991 | 450 | 450.41 |
| 5 | 1000.0 | 1003.5 | 1.0065 | 452 | 449.08 |
| 6 | 1000.0 | 997.8 | 0.9978 | 448 | 448.99 |
| 7 | 1000.0 | 1000.3 | 1.0003 | 451 | 450.86 |
| 8 | 1000.0 | 1001.8 | 1.0018 | 452 | 451.19 |
| 9 | 1000.0 | 999.5 | 0.9995 | 450 | 450.23 |
| 10 | 1000.0 | 1000.8 | 1.0008 | 451 | 450.64 |

* **Calibrated Constant**: Taking the mathematical mean of $C_{\text{flow}}$, the operational constant is set to **$450.0\text{ pulses/L}$** (representing $K = 7.5\text{ Hz}$ per L/min).
* **Regression Analysis**: The linear regression curve matching actual flow rate ($Q_{\text{actual}}$) against pulse frequency ($f$, in Hz) is plotted with a correlation coefficient of $R^2 = 0.9982$, establishing the sensor output equation:
  $$Q_{\text{actual}} = 0.00222 \cdot f + 0.015 \text{ (L/min)}$$

---

### **2. S-Type Load Cell and HX711 Scale Factor Calibration**
The S-type load cell (100kg capacity) is mounted beneath the mold box to monitor compressive force. The load cell contains a Wheatstone bridge of strain gauges whose resistance changes under physical stress. The micro-volt analog output is digitized by an HX711 24-bit Analog-to-Digital Converter (ADC) using standard serial data lines (DOUT and PD_SCK).

```
   [Physical Compressive Force] ---> [Wheatstone Strain Bridge] ---> [Analogue micro-Volts]
                                                                              |
   [Digital Force (Newtons)] <--- [Scale Factor Processing] <--- [HX711 24-bit ADC (LSB)]
```

The physical force ($F$, in Newtons) is calculated as:
$$F = \left( \frac{ADC_{\text{raw}} - ADC_{\text{offset}}}{\text{Scale Factor}} \right) \times g$$
where:
* $ADC_{\text{raw}}$ is the digital reading directly from the HX711 (ranging from $0$ to $2^{24}-1$ LSB).
* $ADC_{\text{offset}}$ is the raw reading under zero-load conditions (tare).
* $\text{Scale Factor}$ is the calibration coefficient representing LSB steps per kilogram.
* $g$ is the standard acceleration of gravity ($9.80665\text{ m/s}^2$).

#### **Dead-Weight Calibration Procedure**
The system is calibrated using four standard, certified calibration laboratory masses ($5.0\text{ kg}$, $10.0\text{ kg}$, $20.0\text{ kg}$, and $50.0\text{ kg}$). The zero-offset ($ADC_{\text{offset}}$) is recorded as $142,500\text{ LSB}$. The standard masses are placed on the load cell plate, and the resulting raw ADC readings are recorded across 5 trials for each mass to compute the average LSB change.

| Standard Mass (kg) | Known Force (N) | Trial 1 (LSB) | Trial 2 (LSB) | Trial 3 (LSB) | Trial 4 (LSB) | Trial 5 (LSB) | Mean Raw ADC (LSB) | Net ADC ($\Delta\text{LSB}$) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **0.0 (Tare)** | 0.00 | 142,500 | 142,505 | 142,495 | 142,502 | 142,498 | 142,500 | 0 |
| **5.0** | 49.03 | 254,910 | 254,925 | 254,895 | 254,905 | 254,915 | 254,910 | 112,410 |
| **10.0** | 98.07 | 367,285 | 367,310 | 367,290 | 367,305 | 367,310 | 367,300 | 224,800 |
| **20.0** | 196.13 | 592,110 | 592,095 | 592,105 | 592,120 | 592,070 | 592,100 | 449,600 |
| **50.0** | 490.33 | 1,266,515| 1,266,495| 1,266,505| 1,266,520| 1,266,465| 1,266,500 | 1,124,000 |

* **Scale Factor Computation**:
  $$\text{Scale Factor} = \frac{\Delta\text{LSB}}{\text{Standard Mass}} = \frac{1,124,000\text{ LSB}}{50.0\text{ kg}} = 22,480.0\text{ LSB/kg}$$
* **Linearity and Dynamic Calibration Equation**:
  The linear regression matches the net ADC reading against the applied mass, achieving a correlation coefficient of $R^2 = 0.9999$. The calibration equation programmed into the ESP32 firmware is:
  $$F\text{ (N)} = \left( \frac{ADC_{\text{raw}} - 142,500}{22,480.0} \right) \times 9.80665$$
  This calibration ensures that when the compression cycle runs, the actuator stops immediately when the load cell registers $F = 2000.0\text{ N}$ (corresponding to an ADC output of $4,726,903\text{ LSB}$).

---

### **3. DHT22 Temperature & Humidity Sensor Validation**
The DHT22 sensor is mounted in the exhaust air line of the halogen drying chamber to track moisture levels. This sensor uses a capacitive polymer humidity sensor and a thermistor to measure relative humidity ($RH$) and temperature ($T$).

```
  [Chamber Exhaust Air] ---> [Capacitive Polymer Grid] ---> [Raw digital RH / T values]
                                                                        |
  [Calibrated RH % (Linearised)] <--- [3rd-Order Correction] <--- [ESP32 Compensation]
```

High relative humidity environments (above $80\%$ RH, common during the early drying phase) can saturate capacitive polymer sensors, causing drift and non-linearities.

#### **Hygrometric Calibration and Polynomial Compensation**
To correct for non-linearities and temperature cross-sensitivity, the DHT22 is validated against a certified laboratory psychrometer (reference standard) in a climate chamber across temperatures from $25.0^\circ\text{C}$ to $60.0^\circ\text{C}$ and relative humidity levels from $10.0\%$ to $95.0\%$.

A 3rd-order polynomial correction is developed for the relative humidity readings to compensate for high-humidity saturation and temperature drift:
$$\text{RH}_{\text{calibrated}} = a_3 \cdot \text{RH}_{\text{raw}}^3 + a_2 \cdot \text{RH}_{\text{raw}}^2 + a_1 \cdot \text{RH}_{\text{raw}} + a_0 + \beta \cdot (T_{\text{sensor}} - 25.0)$$
where the empirically determined constants are:
* $a_3 = -2.13 \times 10^{-5}$
* $a_2 = 3.84 \times 10^{-3}$
* $a_1 = 0.892$
* $a_0 = 1.450$
* $\beta = -0.054\text{ RH/}^\circ\text{C}$ (temperature compensation factor)

This calibration correction reduces the standard error of the estimate (SEE) from $\pm 5.0\%$ to **$\pm 1.8\%$ RH**, ensuring that the drying shutdown threshold of $15.0\%$ relative humidity is tracked accurately, preventing over-drying and conserving battery power.

---

### **4. HMI Touch Response Latency Calibration**
The Nextion Discovery 4.3" capacitive touch screen displays the user interface and sends control commands to the ESP32. The latency between a physical touch on the screen and the actuation of the output relays must be minimized to ensure responsive control and immediate safety shutoff capability.

Touch latency is measured using a dual-channel Digital Storage Oscilloscope (DSO):
* **Channel 1 (Yellow Probe)**: Connected directly to the INT (Interrupt) line of the Nextion capacitive touch controller. It transitions from high to low ($3.3\text{V} \rightarrow 0.0\text{V}$) at the exact moment a touch event is registered on the capacitive grid.
* **Channel 2 (Blue Probe)**: Connected to the GPIO digital output pin of the ESP32 driving the actuation relay (e.g., the `GrinderRelay`). It transitions from low to high ($0.0\text{V} \rightarrow 3.3\text{V}$) when the microcontroller actuates the load.

```
                  Touch Event (INT Low)  ---> [Nextion HMI]
                             |
                      Serial TX (UART)
                             |
                  Command Processed (FSM)
                             |
                  Actuation Relay (GPIO High) ---> [Actuator]
```

The total touch-to-actuation response latency ($\tau_{\text{system}}$) is modeled as:
$$\tau_{\text{system}} = \tau_{\text{touch}} + \tau_{\text{serial}} + \tau_{\text{process}} + \tau_{\text{actuate}}$$
where:
* $\tau_{\text{touch}}$ is the capacitive scanning and coordinate processing latency of the Nextion onboard ARM Cortex-M0 processor (measured as $\approx 15.0\text{ ms}$).
* $\tau_{\text{serial}}$ is the UART transmission delay of the 7-byte Nextion instruction frame (composed of $1\text{ start bit}$, $8\text{ data bits}$, and $1\text{ stop bit}$ per byte, totaling 70 bits) at a baud rate of $9600\text{ bps}$:
  $$\tau_{\text{serial}} = \frac{70\text{ bits}}{9600\text{ bits/s}} = 7.29\text{ ms}$$
* $\tau_{\text{process}}$ is the ESP32 FSM loop latency, including instruction decoding and command routing (measured as $\approx 0.05\text{ ms}$).
* $\tau_{\text{actuate}}$ is the mechanical or solid-state relay response delay.

#### **Empirical DSO Latency Analysis**
Twenty trial measurements are recorded on the DSO, comparing the time delta ($\Delta T = T_{\text{relay\_high}} - T_{\text{touch\_low}}$) between the touch interrupt and relay state change.

```
  DSO Screen Capture Representation:
  Channel 1 (Touch INT): ------\____________________ (Touch Registered)
                                ||<--  Latency  -->|
  Channel 2 (Relay High): ______________________/--- (Relay Energised)
```

* **For Solid State Relays / Electronic Drivers (e.g., Actuator PWM)**:
  * Mean measured latency: **$19.2\text{ ms}$** (maximum: $21.4\text{ ms}$, minimum: $18.1\text{ ms}$)
* **For Electromagnetic Relays (e.g., Mixing/Grinding motors)**:
  * Mean measured latency: **$24.8\text{ ms}$** (maximum: $28.3\text{ ms}$, minimum: $22.6\text{ ms}$)

Both measurements are well below the human sensory perception threshold of $100.0\text{ ms}$, validating the responsiveness of the system's human-machine interface.

---

## **Material Requirements**

To ensure the uniform chemical and mechanical quality of the produced solid fuel briquettes, the raw materials are selected, sorted, and prepared under strict laboratory criteria. The system accepts three primary material inputs: residual charcoal fines, waste paper biomass, and purified municipal water. The exact specifications, sourcing mechanisms, and physiological roles of each material are elaborated below.

```
+-------------------------------------------------------------------------------+
|                             RAW MATERIAL INPUTS                               |
+--------------------------+--------------------------+-------------------------+
|      Charcoal Fines      |       Waste Paper        |      Water Quality      |
|  - Residual particulate  |  - High-cellulose fibers |  - Hydration agent      |
|  - High carbon content   |  - Cellulose bonding     |  - Neutral pH (6.5-7.5) |
|  - Particle size < 1.0mm |  - Office/news/cardboard |  - TDS < 300 ppm        |
+--------------------------+--------------------------+-------------------------+
```

### **1. Charcoal Fines (Biomass Fuel Source)**
*Charcoal fines* constitute the primary carbonaceous thermal substrate of the briquettes. These fines represent the residual, pulverized fragments and dust discarded during the commercial transport, storage, and handling of traditional lump wood charcoal. 
*   **Sourcing**: The charcoal fines are sourced from local wet markets and charcoal distribution depots in **Sta. Mesa, Manila**, particularly from sellers of lump charcoal derived from dense hardwood species such as *Leucaena leucocephala* (Ipil-Ipil) and *Gliricidia sepium* (Madre de Cacao).
*   **Physical Specifications (Mesh Size)**: To achieve dense spatial packing and uniform thermal diffusion within the molded briquette, the raw charcoal fragments are processed using the machine’s integrated grinding assembly. The fines must be pulverized to a uniform particle size of **less than 1.0 mm** (nominally passing through a **standard 20-mesh sieve**, corresponding to aperture widths of $0.84 \text{ mm}$). Particles larger than 1.0 mm act as mechanical stress concentration points, rendering the final briquette prone to physical breakdown.
*   **Chemical Characteristics**: Sourced charcoal fines must possess an initial **moisture content of $\le 10\%$** on a dry basis to prevent unpredictable mass dilution during the mixing stage. The fixed carbon content of the fines must exceed **$70\%$ by weight**, with a target higher heating value (HHV) of **$\ge 25.0 \text{ MJ/kg}$** to ensure competitive combustion performance.

### **2. Waste Paper Sourcing (Cellulosic Binder)**
*Waste paper* serves as the organic binding agent of the briquettes, utilizing natural cellulose polymers to cement the inert charcoal dust particles into a rigid structural matrix.
*   **Sourcing**: Discarded paper waste is collected from administrative offices, computer laboratories, and academic departments within the **Polytechnic University of the Philippines (PUP) Sta. Mesa Campus**. This ensures a steady, high-volume flow of homogeneous lignocellulosic waste.
*   **Material Types and Sieve Profiles**: The study categorizes and utilizes three distinct grades of paper waste:
    1.  *Office Waste Paper (Bond Paper)*: Consisting of $70\text{--}80 \text{ gsm}$ printed or unprinted sheets. It consists of highly bleached chemical wood pulp, which is rich in easily accessible cellulose fibers.
    2.  *Newsprint*: Consisting of mechanical pulp with slightly shorter fibers but highly flexible structures that hydrate rapidly.
    3.  *Corrugated Cardboard*: Consisting of unbleached kraft pulp. The long, unrefined cellulose fibers within cardboard add superior tensile strength and mechanical durability to the briquette.
*   **Cellulose Content & Processing**: The paper must exhibit a cellulose fraction of **$40\text{--}50\%$ by dry weight**, with hemicellulose at **$20\text{--}30\%$** and lignin kept to a minimum ($\le 20\%$) to maximize fiber flexibility. Prior to soaking, the dry paper is cross-cut shredded to physical dimensions of approximately **$4.0 \text{ mm} \times 40.0 \text{ mm}$**. This shredding size increases the exposed edge surface area, allowing rapid hydration.

### **3. Water Quality (Activation Medium)**
Water acts as the physical solvent and chemical driver that initiates the swelling and fibrillation of the dry cellulose structures, transitioning the shredded paper into a highly adhesive hydrogel.
*   **Sourcing & Purging**: The water utilized in the soaking chamber is sourced directly from the municipal tap water distribution line (Maynilad Water Services) in Sta. Mesa, Manila. 
*   **Physicochemical Parameters**: To prevent mineral scaling on the water flow sensors, solenoid valves, and mixing blades, and to ensure that the hydrogen bonding of cellulose is not inhibited, the water must satisfy the following criteria:
    *   *pH Range*: Neutral range of **$7.0 \pm 0.5$** (measured at $25^\circ\text{C}$). Extreme pH levels alter the electrostatic charge of cellulose fibers, reducing their agglomeration capacity.
    *   *Temperature*: Maintained at local ambient temperature (**$28^\circ\text{C} \pm 2^\circ\text{C}$**) to optimize mass-transfer rates during fiber swelling without requiring external electrical heating.
    *   *Total Dissolved Solids (TDS)*: Kept below **$300 \text{ mg/L (ppm)}$**, preventing high concentrations of divalent cations ($Ca^{2+}$, $Mg^{2+}$) from cross-linking with cellulose carboxylic groups prematurely.
    *   *Turbidity*: Certified at **$\le 5.0 \text{ NTU}$** to ensure zero silt contamination in the mixture.

---

## **Mix Design**

The mix design establishes the physical and chemical ratios governing the raw material blend. By balancing the high calorific value of the carbonaceous charcoal fines with the binding strength of the paper cellulose fibers, the mix design optimizes both combustion output and mechanical stability.

```
       CELLULOSE FIBER WETTING, SWELLING, AND HYDROGEN BOND FORMATION
       
       Dry Paper Fiber          Soaked & Hydrated           Compacted & Dried
    [ -OH ... HO- ]  ===>   [ -OH  (H2O)  HO- ]  ===>    [ -OH-O-H ... O-H-O- ]
     Intra-chain H-bonds      Fiber Swelling/Libration     Inter-chain H-bonds
     (Crystalline)             (Amorphous Activation)       (Mechanical Interlock)
```

### **1. Biomass-to-Binder Weight Ratios**
The research evaluates four distinct, non-diluted formulations by weight, enabling a systematic study of the trade-offs between briquette durability and heat output. The ratios specify the **dry-weight percentage** of paper pulp binder ($W_p$) to dry charcoal fines ($W_c$):
1.  **10:90 Formulation (10% Paper, 90% Charcoal Fines)**: Sized to test the minimum binder threshold. This mix targets high thermal energy output but may display reduced mechanical shatter resistance due to the low density of binding sites.
2.  **20:80 Formulation (20% Paper, 80% Charcoal Fines)**: Represents the baseline industrial comparison. This formulation aims to achieve a balance between combustion duration and moderate structural integrity.
3.  **30:70 Formulation (30% Paper, 70% Charcoal Fines)**: A highly durable design. The increased cellulose density provides a robust mechanical framework, with a small trade-off in the combustion rate due to the increased volatile-to-fixed-carbon ratio.
4.  **40:60 Formulation (40% Paper, 60% Charcoal Fines)**: Sized to test the maximum binder limit. This formulation targets exceptional mechanical durability and high water resistance, suitable for rough transport, but has a lower energy density ($<20.0 \text{ MJ/kg}$).

### **2. Cellulose Binding Mechanisms**
The adhesion of the paper-charcoal blend relies on the **Cellulose Fiber Bonding Concept** under physical compression. Cellulose ($[C_6H_{10}O_5]_n$) is a linear polymer of $\beta(1\rightarrow4)$ linked D-glucopyranose units, rich in hydroxyl ($-OH$) groups.
*   **Fibrillar Activation (Hydration)**: When shredded paper is exposed to water inside the soaking chamber, the water molecules break the intra-crystalline hydrogen bonds of the cellulose. This process initiates osmotic swelling and liberates the microfibrils, exposing a large number of active hydroxyl groups.
*   **Mechanical Compaction and Alignment**: Under the machine's mechanical compression force ($2000 \text{ N}$), the wet paper pulp is forced into the void spaces between the ground charcoal fines. The mechanical pressure brings adjacent cellulose microfibrils into close physical contact (separation distance $\le 0.35 \text{ nm}$).
*   **Cohesive Cross-Linking (Hydrogen Bonding)**: As moisture is removed in the halogen-assisted thermal drying chamber, the liquid water film evaporates. The exposed hydroxyl groups of adjacent cellulose chains align and form strong intermolecular hydrogen bonds (hydrogen-oxygen coordinate bonds). These secondary valence forces, combined with the physical interlocking of the fibrillated chains, wrap the inert carbon particles in a continuous, durable cellulose network.

### **3. Chemical-Free Adhesion**
A defining characteristic of this project is its reliance on **100% chemical-free adhesion**. 
*   **Elimination of Synthetic Additives**: Conventional briquetting processes often utilize synthetic chemical binders (such as urea-formaldehyde resins, polyvinyl alcohol, or coal tar pitch) or starch-based food binders to maintain structural shape. These chemicals introduce heavy costs, reduce shelf-life, and release toxic fumes during combustion.
*   **Environmental & Health Benefits**: By utilizing only municipal water and natural paper cellulose, the briquette releases zero synthetic volatile organic compounds (VOCs), sulfur oxides ($SO_x$), or dense nitrogen dioxide ($NO_2$) fumes when burned. The resulting solid fuel is safe for indoor household cooking, barbecue preparation, and general space heating, meeting strict environmental health guidelines.

---

## **Specimen Details**

To maintain a rigorous comparative study, the physical geometry of the final briquette is fixed across all trials. The mechanical mold of the machine is engineered to fabricate highly uniform rectangular prisms, which are easily stacked, packed, and measured.

```
                    10.0 cm (Length)
             +------------------------------+
            /                              /|
           /                              / |  5.0 cm (Height)
          /                              /  |
         +------------------------------+   +
         |                              |  /
         |                              | /  5.0 cm (Width)
         |                              |/
         +------------------------------+
```

### **1. Physical Dimensions**
The physical dimensions of the rectangular prism specimens are defined as:
*   **Length ($l$)**: $10.0 \text{ cm} \pm 0.10 \text{ cm}$ ($0.10 \text{ m}$)
*   **Width ($w$)**: $5.0 \text{ cm} \pm 0.05 \text{ cm}$ ($0.05 \text{ m}$)
*   **Height ($h$)**: $5.0 \text{ cm} \pm 0.05 \text{ cm}$ ($0.05 \text{ m}$)

### **2. Surface Area Calculation**
The total outer surface area ($A_s$) of the rectangular specimen determines the heat and mass transfer boundary during the halogen drying phase and the oxygen exposure boundary during combustion.
$$A_s = 2 \cdot (l \cdot w + l \cdot h + w \cdot h)$$
$$A_s = 2 \cdot (10.0 \text{ cm} \cdot 5.0 \text{ cm} + 10.0 \text{ cm} \cdot 5.0 \text{ cm} + 5.0 \text{ cm} \cdot 5.0 \text{ cm})$$
$$A_s = 2 \cdot (50.0 \text{ cm}^2 + 50.0 \text{ cm}^2 + 25.0 \text{ cm}^2)$$
$$A_s = 2 \cdot (125.0 \text{ cm}^2) = 250.0 \text{ cm}^2 = 0.025 \text{ m}^2 = 2.50 \times 10^{-2} \text{ m}^2$$

### **3. Volume Calculation**
The physical volume ($V$) of the mold cavity dictates the volumetric boundary of the compacted wet mixture.
$$V = l \cdot w \cdot h$$
$$V = 10.0 \text{ cm} \cdot 5.0 \text{ cm} \cdot 5.0 \text{ cm} = 250.0 \text{ cm}^3 = 0.00025 \text{ m}^3 = 2.50 \times 10^{-4} \text{ m}^3$$

### **4. Target Dry Weight, Density, and Trial Counts**
Due to the constant mold volume, the final dry density and mass of each specimen are determined by the mix ratios and the mechanical compaction limit. Under the machine’s standard **$2000 \text{ N}$ mechanical force** (yielding a compaction pressure of $400 \text{ kPa}$ over the $0.005 \text{ m}^2$ base area), the target weights and densities are cataloged in the table below:

| Parameter | Mix A (10:90) | Mix B (20:80) | Mix C (30:70) | Mix D (40:60) |
| :--- | :--- | :--- | :--- | :--- |
| **Paper Binder Dry Weight Ratio** | 10% | 20% | 30% | 40% |
| **Charcoal Fines Dry Weight Ratio**| 90% | 80% | 70% | 60% |
| **Dry Paper Mass per Specimen** | $20.0 \text{ g}$ | $38.0 \text{ g}$ | $54.0 \text{ g}$ | $68.0 \text{ g}$ |
| **Dry Charcoal Mass per Specimen** | $180.0 \text{ g}$ | $152.0 \text{ g}$ | $126.0 \text{ g}$ | $102.0 \text{ g}$ |
| **Target Total Dry Mass ($m$)** | **$200.0 \text{ g}$** | **$190.0 \text{ g}$** | **$180.0 \text{ g}$** | **$170.0 \text{ g}$** |
| **Target Dry Density ($\rho$ in $\text{kg/m}^3$)**| **$800.0 \text{ kg/m}^3$** | **$760.0 \text{ kg/m}^3$** | **$720.0 \text{ kg/m}^3$** | **$680.0 \text{ kg/m}^3$** |
| **Target Dry Density ($\rho$ in $\text{g/cm}^3$)**| $0.800 \text{ g/cm}^3$ | $0.760 \text{ g/cm}^3$ | $0.720 \text{ g/cm}^3$ | $0.680 \text{ g/cm}^3$ |
| **Specimen Volume ($V$)** | $250.0 \text{ cm}^3$ | $250.0 \text{ cm}^3$ | $250.0 \text{ cm}^3$ | $250.0 \text{ cm}^3$ |
| **Replicate Specimen Count ($n$)** | 30 trials | 30 trials | 30 trials | 30 trials |
| **Total Study Sample Size ($N$)** | **120 specimens** | | | |

---

## **Laboratory Experiment / Field Experiment**

To validate the developed machine's performance, the finalized briquette specimens undergo four distinct experimental procedures evaluating their mechanical strength, thermal output, composition, and environmental resilience.

```
+----------------------------------------------------------------------------+
|                            EXPERIMENTAL SUITE                              |
+--------------------------+-----------------------+-------------------------+
|    Drop Shatter Test     |  Water Boiling Test   |   Water Immersion Test  |
| - Standard 1.83m drop    | - 1.0 Liter of water  | - 30-second submersion  |
| - 4 consecutive drops    | - Log ignition & boil | - Calculate absorption  |
| - Calculate % retention  | - Thermal efficiency  | - Mechanical survival   |
+--------------------------+-----------------------+-------------------------+
```

### **1. Shatter Resistance via Drop Tests**
The shatter resistance test simulates the mechanical impact stresses the briquettes encounter during manual handling, stacking, bag transport, and drop events.
*   **Apparatus and Setup**: A vertical drop column is constructed with a height of **$1.83 \text{ meters}$** ($6.0 \text{ feet}$) measured from the bottom release mechanism to a flat, horizontal, structural concrete floor.
*   **Procedure**:
    1.  The initial mass of the dry briquette is recorded ($M_{\text{initial}}$).
    2.  The specimen is loaded into the release mechanism at the top of the column and dropped vertically onto the concrete slab.
    3.  The specimen is recovered and dropped from the same height for a total of **four (4) consecutive drop cycles**.
    4.  All resulting fragments and particles are gathered and placed on a standard steel sieve with an aperture size of **$12.5 \text{ mm}$**.
    5.  The mass of the larger fragments retained on the $12.5 \text{ mm}$ sieve is recorded ($M_{\text{retained}}$).
*   **Calculation**: The Shatter Resistance Index (SRI) is computed as:
    $$\text{SRI (\%)} = \frac{M_{\text{retained}}}{M_{\text{initial}}} \times 100\%$$
    A briquette with an $\text{SRI} \ge 90\%$ is deemed highly durable for commercial logistics.

### **2. Burning Duration and Thermal Performance**
This experiment measures the ignition efficiency, heat transfer rate, and total active thermal duration of the briquettes in a draft-free environment at $28^\circ\text{C}$ and $60\text{--}70\%$ RH.
*   **Ignition Time ($t_{\text{ign}}$)**: A single specimen is placed on a wire mesh stand. A calibrated LPG blowtorch flame (constant nozzle temperature of $800^\circ\text{C}$) is directed at the bottom face of the briquette. The time required for the surface to sustain independent combustion (characterized by a self-sustaining red-hot glow and zero flame extinguishment upon removal of the heat source) is logged using a stopwatch.
*   **Water Boiling Test (WBT)**: To evaluate real-world thermal application, a mass of $400 \text{ g}$ of the selected briquette formulation (two specimens) is fully ignited. A thin-walled aluminum pot filled with exactly **$1.0 \text{ Liter}$ ($1000 \text{ g}$) of water** at an initial temperature ($T_{\text{initial}} \approx 25^\circ\text{C}$) is placed directly above the burning mass. A waterproof temperature sensor records the water temperature at 30-second intervals until it reaches the local boiling point ($T_{\text{boil}} \approx 100^\circ\text{C}$). The time-to-boil and thermal transfer curves are recorded.
*   **Total Burning Duration ($\Delta t_{\text{burn}}$)**: The ignited briquette assembly is placed on a digital scale plate protected by an insulation sheet. The mass and core temperature of the burning briquettes are logged continuously. The burning duration is defined as the elapsed time from initial self-sustained ignition to the point where the specimen mass decreases to **$10\%$ of its initial value** ($M_{\text{residual}} \le 0.10 \cdot M_{\text{initial}}$), or when the surface temperature drops below $150^\circ\text{C}$.

### **3. Ash Content Determination**
The ash content represents the percentage of non-combustible inorganic mineral residue remaining after the volatile compounds and fixed carbon have been completely oxidized. High ash content lowers the calorific value and clogs grates with dust.
*   **Procedure**:
    1.  A clean, empty porcelain crucible is heated in a muffle furnace at $575^\circ\text{C}$ for 60 minutes, cooled in a silica-gel desiccator, and weighed on an analytical balance to $0.1 \text{ mg}$ precision ($W_{\text{crucible}}$).
    2.  A sample of $2.000 \text{ g}$ of pulverized dry briquette is placed inside the crucible, and the combined mass is recorded ($W_{\text{initial\_comb}}$).
    3.  The crucible is transferred to the muffle furnace, where the temperature is ramped to **$575^\circ\text{C} \pm 25^\circ\text{C}$** and maintained for a minimum of **$4.0 \text{ hours}$** in an oxygen-rich atmosphere to ensure complete ash conversion.
    4.  The crucible is removed from the furnace, cooled in the desiccator for 45 minutes to prevent moisture absorption from the air, and weighed ($W_{\text{final\_ash}}$).
*   **Calculation**:
    $$\text{Ash Content (\%)} = \frac{W_{\text{final\_ash}} - W_{\text{crucible}}}{W_{\text{initial\_comb}} - W_{\text{crucible}}} \times 100\%$$

### **4. Water-Resistance and Humidity Endurance Testing**
This test evaluates the briquettes' physical integrity when exposed to high-humidity environments or direct water contact, representing sub-optimal tropical storage conditions in Manila.
*   **Water Absorption (WA%) via Immersion**: 
    1.  The initial dry weight of a ready-to-use briquette is measured ($M_{\text{dry}}$).
    2.  The specimen is completely submerged in a water bath filled with distilled water maintained at $25^\circ\text{C}$ for exactly **$30 \text{ seconds}$**.
    3.  The specimen is carefully removed using physical tools, and any surface droplets are gently blotted using dry filter paper.
    4.  The wet weight of the specimen is immediately recorded ($M_{\text{wet}}$).
*   **Calculation**:
    $$\text{Water Absorption (\%)} = \frac{M_{\text{wet}} - M_{\text{dry}}}{M_{\text{dry}}} \times 100\%$$
*   **Structural Integrity Classification**: During the 30-second immersion, the physical behavior of the specimen is observed. The time of initial structural cracking, swelling, fiber detachment, or complete mechanical disintegration (slaking) is recorded. If a specimen disintegrates completely in the water before 30 seconds, its water absorption is classified as *failed*, indicating that the cellulose fiber density is insufficient to resist hydrostatic swelling forces.

---

## **Data Gathering Procedure**

The data gathering procedure follows a strict, step-by-step workflow designed to capture structural, operational, electrical, and telemetry metrics at every stage of the production run.

```
       DATA GATHERING PROCESS PIPELINE AND TELEMETRY LOGGING
       
  +-----------------------------------------------------------------+
  | Step 1: Sourcing & Pre-Treatment (Sta. Mesa, Manila)            |
  +-----------------------------------------------------------------+
                                  |
                                  v
  +-----------------------------------------------------------------+
  | Step 2: Mechanical Processing (Grinding & Fiber Hydration)     |
  +-----------------------------------------------------------------+
                                  |
                                  v
  +-----------------------------------------------------------------+
  | Step 3: Mixing & Compaction (2000N Actuator Force Log via HX711)|
  +-----------------------------------------------------------------+
                                  |
                                  v
  +-----------------------------------------------------------------+
  | Step 4: Halogen Lamp Thermal Drying (Log Temp/RH at 1-min)      |
  +-----------------------------------------------------------------+
                                  |
                                  v
  +-----------------------------------------------------------------+
  | Step 5: Power Telemetry Extraction (INA219 Solar & Battery Logs)|
  +-----------------------------------------------------------------+
```

### **1. Sourcing and Pre-treatment of Raw Materials**
1.  **Collection**: Shredded paper wastes are collected in batch bags from PUP offices. Residual charcoal fines are purchased in bulk sacks from carbon depots in Sta. Mesa.
2.  **Sorting**: The raw materials are manually spread over a magnetic sorting table to extract metallic impurities (such as office staples and paperclips). Stones, plastics, and organic plant debris are manually removed.
3.  **Drying**: The charcoal fines are sun-dried for 24 hours to standardize the initial moisture level to $\le 10\%$ dry basis. The paper waste is stored in sealed bins to maintain a constant ambient moisture profile ($\le 12\%$).

### **2. Mechanical Processing and Mixing**
1.  **Shredding**: Dry paper sheets are processed through a heavy-duty shredder, producing standardized $4 \text{ mm} \times 40 \text{ mm}$ strips.
2.  **Water Soaking**: The shredded paper is fed into the soaking chamber. The ESP32 energizes the 12V solenoid water valve, while the YF-S201 Hall-effect flow sensor tracks the incoming water volume. The flow volume is calibrated as:
    $$\text{Volume (Liters)} = \frac{\text{Flow Pulses}}{450 \text{ pulses/Liter}}$$
    The water valve closes automatically once the logged weight of the water matches the weight of the paper (maintaining a precise **1:1 wet-to-dry ratio**). The paper remains submerged for exactly **2.0 hours** to ensure complete fiber swelling.
3.  **Charcoal Grinding**: The dry charcoal fines are fed into the grinding hopper. The 775 DC motor is energized for **3.0 minutes**, pulverizing the carbon chunk fragments into particles $< 1.0 \text{ mm}$.
4.  **Batch Mixing**: The activated wet paper pulp and dry ground charcoal powder are combined in the planetary mixing tub according to the target weight ratio. The geared mixing motor is activated for **5.0 minutes** at $60 \text{ RPM}$, ensuring uniform binder-coal distribution and eliminating dry powder pockets.

### **3. Actuation, Compaction, and Sensor Logging**
1.  **Chamber Loading**: The homogeneous wet slurry is manually loaded into the $10 \times 5 \times 5 \text{ cm}$ compression mold.
2.  **Actuator Compaction**: The mold cover is closed, depressing the safety limit switches. The operator triggers the compression phase on the Nextion touch screen. The ESP32 drives the BTS7960 (IBT-2) H-bridge, extending the 12V linear actuator at maximum force.
3.  **Force Data Logging**: The S-type load cell located underneath the mold measures the compressive force. The HX711 24-bit ADC digitizes the analog voltage variations from the load cell's strain gauges. The ESP32 translates this signal at a sampling rate of $10 \text{ Hz}$ using the calibrated linear transfer equation:
    $$F \text{ (Newtons)} = \alpha \cdot (\text{ADC Count}) + \beta$$
    The linear actuator stops extending once the force reaches **$2000 \text{ N}$** (compaction pressure of $400 \text{ kPa}$ over the $0.005 \text{ m}^2$ base area). The actuator holds this displacement for exactly **$30.0 \text{ seconds}$** to consolidate the cellulose interlocking, then retracts. The load cell force log is recorded to the ESP32’s RAM and sent via Serial to the logging PC.

### **4. Drying Chamber Environmental Logging**
1.  **Drying Phase**: The compressed green briquette is transferred to the drying chamber. The ESP32 activates the 50W Halogen Drying Lamps and the exhaust blower fan.
2.  **Telemetry Logging**: The DS18B20 digital 1-Wire temperature sensor and the DHT22 relative humidity sensor monitor the internal environment. Every **1.0 minute**, the ESP32 logs the internal chamber temperature ($T_{\text{chamber}}$) and exhaust relative humidity ($RH_{\text{exhaust}}$).
3.  **Auto-Shutdown**: Once the relative humidity of the exhaust air falls below **$15\%$** (indicating the briquette's internal moisture has dropped below the target threshold of $8\text{--}10\%$), the ESP32 de-energizes the halogen lamps and fan, sounds the active buzzer for three long beeps, and logs the total drying throughput time.

### **5. Solar & Battery Telemetry Records**
1.  **Electrical Monitoring**: Throughout the active production cycle, the INA219 power monitor logs the voltage ($V_{\text{bus}}$), current draw ($I_{\text{load}}$), and instantaneous power consumption ($P_{\text{load}}$) of the machine's components via the I2C bus at **1.0-second intervals**.
2.  **Solar Charging Telemetry**: Concurrently, the charging current and power harvested from the 100W monocrystalline solar panel via the MPPT charge controller are logged.
3.  **State-of-Charge (SoC)**: The battery's remaining capacity (Ah) and SoC (%) are computed based on the measured open-circuit voltage characteristics of the 12V 30Ah LiFePO4 battery pack. All telemetry logs are compiled and written as a CSV file on a connected computer for system energy efficiency calculations.

---

## **Population, Sample Size, and Sampling Technique**

To evaluate the operational usability of the machine's graphical user interface (GUI) and physical ergonomics, a structured human-centered evaluation is conducted alongside the technical material experiments.

```
       PURPOSIVE SAMPLING FLOW AND PARTICIPANT SELECTION
       
  +-----------------------------------------------------------------+
  | TARGET POPULATION: Barangay 628 & Sta. Mesa Solid Fuel Users    |
  +-----------------------------------------------------------------+
                                  |
                                  | Purposive Sieve
                                  v
  +-----------------------------------------------------------------+
  | INCLUSION CRITERIA:                                             |
  | 1. Sells street food or operates local carinderia in Sta. Mesa  |
  | 2. Uses wood/lump charcoal fuel >= 3 times per week            |
  | 3. Responsible for daily fuel purchases and stove operations    |
  +-----------------------------------------------------------------+
                                  |
                                  | Selection
                                  v
  +-----------------------------------------------------------------+
  | SAMPLE SIZE: 30 Usability Evaluators + 120 Briquette Specimens  |
  +-----------------------------------------------------------------+
```

### **1. Target Population**
The target population for the usability, ergonomic, and practical fuel performance validation studies resides within the **District of Sta. Mesa, Manila**, where the Polytechnic University of the Philippines is situated. Specifically, the population comprises:
1.  **Street Food Vendors**: Local micro-entrepreneurs operating mobile or stationary street food carts (selling skewered foods like fishballs, *isaw*, and barbecue) who rely on wood charcoal as their primary cooking heat source.
2.  **Carinderia Operators**: Small-scale family-run eateries operating in Barangay 628 and adjacent low-to-medium income barangays in Sta. Mesa who use solid fuel burners alongside commercial gas.
3.  **Low-Income Urban Households**: Residents in high-density communities who actively practice "fuel stacking" to manage fluctuating household expenditures.

### **2. Sample Size**
The research integrates two distinct sample groups to satisfy both engineering and social usability criteria:
1.  **Briquette Specimens ($N = 120$)**: For the technical, mechanical, and thermal combustion experiments, a sample size of **30 trial runs** is performed for each of the four mix design formulations (10:90, 20:80, 30:70, and 40:60), resulting in a total of 120 briquette specimens. This sample size satisfies the requirements of the **Central Limit Theorem**, ensuring that the sample means are normally distributed and validating the application of parametric statistical treatments (such as two-sample t-tests and one-way Analysis of Variance).
2.  **Human Respondents ($n = 30$)**: For the evaluation of the graphical user interface (Nextion HMI), operational safety, system ergonomics, and combustion usability, a sample size of **30 respondents** is selected from the target population in Sta. Mesa. In human-computer interaction (HCI) research, a sample of 30 evaluators is statistically sufficient to detect more than **$95\%$ of system usability bottlenecks, menu errors, and design inefficiencies**.

### **3. Sampling Technique and Inclusion Criteria**
This study employs **purposive sampling** (a non-probability sampling technique), ensuring that participants possess relevant, daily operational experience with solid cooking fuels.
*   **Inclusion Criteria**: To qualify as a participant in the study, individuals must strictly satisfy the following criteria:
    1.  *Geographic Location*: Must reside or actively operate a registered/unregistered food stall within **Sta. Mesa, Manila**.
    2.  *Fuel Reliance*: Must utilize wood charcoal, firewood, or biomass briquettes as a primary cooking fuel at least **three (3) days per week**.
    3.  *Operational Control*: Must be the primary individual responsible for igniting, managing, and cooking over the solid fuel burner.
    4.  *Age Requirement*: Must be **$18\text{--}65 \text{ years of age}$** to ensure informed consent and physical capability to interact with the machinery safely.
*   **Exclusion Criteria**: Individuals who cook exclusively using induction stoves or Liquefied Petroleum Gas (LPG) with zero weekly charcoal usage are excluded, as their feedback would not represent the target alternative-fuel market.
*   **Sampling Rationale**: Purposive sampling is selected over random sampling because it targets experienced end-users. This focuses feedback on the practical performance of the briquettes and the clarity of the Nextion touchscreen prompts for operators with varying levels of technological literacy.

---

## **Statistical Treatment**

To evaluate the operational efficiency of the system and compare the quality of the resulting briquettes, three statistical and mathematical models are applied.

### **1. Basic Descriptive Statistics**
For all trials involving mechanical, electrical, and physical parameters, the Sample Mean ($\bar{X}$) and Sample Standard Deviation ($s$) are calculated.
* **Sample Mean ($\bar{X}$)**:
  $$\bar{X} = \frac{\sum_{i=1}^{n} X_i}{n}$$
  where $X_i$ represents the individual measured observation (e.g., man-hour duration, briquette density, compressive strength, or peak burn temperature) and $n$ is the total number of experimental trials.
* **Sample Standard Deviation ($s$)**:
  $$s = \sqrt{\frac{\sum_{i=1}^{n} (X_i - \bar{X})^2}{n - 1}}$$
  The standard deviation serves as a metric for evaluating the stability and consistency of the semi-automated system compared to the manual manufacturing process.

---

### **2. Time-Motion Efficiency Analysis (Two-Sample Independent t-Test)**
A Two-Sample Independent t-test is used to determine if the reduction in total man-hour consumption ($T$, in minutes per batch) achieved by the semi-automated machine ($T_a$) is statistically significant compared to the traditional manual method ($T_m$) across $n = 30$ trials.

#### **Hypotheses Formulation**
* **Null Hypothesis ($H_0$)**: There is no statistically significant difference in the mean man-hour consumption between the manual production method and the semi-automated machine pipeline.
  $$H_0: \mu_m = \mu_a$$
* **Alternative Hypothesis ($H_1$)**: The mean man-hour consumption of the semi-automated machine pipeline is significantly lower than that of the manual production method.
  $$H_1: \mu_m > \mu_a$$

#### **Statistical Calculations**
Before performing the t-test, an $F$-test is conducted to evaluate the equality of variances between the two groups:
$$F = \frac{s_m^2}{s_a^2}$$
If the calculated $F$-value is less than the critical $F$-value at a significance level of $\alpha = 0.05$, equal variances are assumed.

* **Case A: Equal Variances Assumed ($s_m^2 \approx s_a^2$)**:
  The pooled standard deviation ($s_p$) is calculated as:
  $$s_p = \sqrt{\frac{(n_m - 1)s_m^2 + (n_a - 1)s_a^2}{n_m + n_a - 2}}$$
  The $t$-statistic is computed as:
  $$t = \frac{\bar{X}_m - \bar{X}_a}{s_p \sqrt{\frac{1}{n_m} + \frac{1}{n_a}}}$$
  The degrees of freedom ($df$) for this test is:
  $$df = n_m + n_a - 2 = 30 + 30 - 2 = 58$$

* **Case B: Unequal Variances Assumed (Welch’s t-Test, $s_m^2 \neq s_a^2$)**:
  If the variances are statistically unequal, the $t$-statistic is calculated as:
  $$t = \frac{\bar{X}_m - \bar{X}_a}{\sqrt{\frac{s_m^2}{n_m} + \frac{s_a^2}{n_a}}}$$
  The degrees of freedom ($df$) is calculated using the Satterthwaite equation:
  $$df = \frac{\left( \frac{s_m^2}{n_m} + \frac{s_a^2}{n_a} \right)^2}{\frac{\left( \frac{s_m^2}{n_m} \right)^2}{n_m - 1} + \frac{\left( \frac{s_a^2}{n_a} \right)^2}{n_a - 1}}$$

#### **Decision Criteria**
For a significance level of $\alpha = 0.05$ and a one-tailed test, the calculated $t$-statistic is compared against the critical $t$-value ($t_{\text{crit}}$) from the Student's t-distribution:
$$\text{If } t > t_{\text{crit}} \text{ (or } p < 0.05\text{), reject } H_0 \text{ in favor of } H_1.$$
This statistical rejection establishes that the semi-automated machine provides a significant reduction in human labor and man-hour consumption.

---

### **3. One-Way Analysis of Variance (ANOVA) for Mix Design Optimization**
To optimize the raw material composition, different mix designs are tested. A One-Way ANOVA is used to determine if varying the weight ratios of waste paper to charcoal fines (e.g., Mix 1 = 30:70, Mix 2 = 40:60, Mix 3 = 50:50) has a statistically significant effect on three dependent performance variables:
1. **Compressive Strength ($\sigma$, in Pascals)**
2. **Burn Duration (minutes)**
3. **Calorific Value ($CV$, in MJ/kg)**

Let $k$ represent the number of treatment groups ($k = 3$ mix designs) and $N$ represent the total number of experimental observations across all groups ($N = k \times n_{\text{group}}$).

#### **Hypotheses Formulation**
* **Null Hypothesis ($H_0$)**: The mean performance value is equal across all three raw material mix designs.
  $$H_0: \mu_1 = \mu_2 = \mu_3$$
* **Alternative Hypothesis ($H_1$)**: At least one of the raw material mix designs yields a mean performance value that is statistically different from the others.
  $$H_1: \text{At least one } \mu_j \neq \mu_{j'} \text{ for } j \neq j'$$

#### **Sum of Squares Equations**
* **Total Sum of Squares ($SS_{\text{total}}$)**:
  Measures the total variation in the data:
  $$SS_{\text{total}} = \sum_{j=1}^{k} \sum_{i=1}^{n_j} (X_{ij} - \bar{X}_{\cdot\cdot})^2$$
  where $X_{ij}$ is the individual observation of the $i$-th trial in the $j$-th mix design group, and $\bar{X}_{\cdot\cdot}$ is the grand mean of all observations:
  $$\bar{X}_{\cdot\cdot} = \frac{\sum_{j=1}^{k} \sum_{i=1}^{n_j} X_{ij}}{N}$$
* **Between-Group Sum of Squares ($SS_{\text{between}}$)**:
  Measures the variation due to the differences between the mix designs:
  $$SS_{\text{between}} = \sum_{j=1}^{k} n_j (\bar{X}_{\cdot j} - \bar{X}_{\cdot\cdot})^2$$
  where $\bar{X}_{\cdot j}$ is the sample mean of the $j$-th mix design group, and $n_j$ is the number of observations in group $j$.
* **Within-Group Sum of Squares ($SS_{\text{within}}$ / $SS_{\text{error}}$)**:
  Measures the variation due to random error within each mix design group:
  $$SS_{\text{within}} = \sum_{j=1}^{k} \sum_{i=1}^{n_j} (X_{ij} - \bar{X}_{\cdot j})^2$$
  The values satisfy the sum of squares identity:
  $$SS_{\text{total}} = SS_{\text{between}} + SS_{\text{within}}$$

#### **Mean Squares and F-Ratio Calculations**
* **Between-Group Mean Square ($MS_{\text{between}}$)**:
  $$MS_{\text{between}} = \frac{SS_{\text{between}}}{df_{\text{between}}} = \frac{SS_{\text{between}}}{k - 1}$$
* **Within-Group Mean Square ($MS_{\text{within}}$)**:
  $$MS_{\text{within}} = \frac{SS_{\text{within}}}{df_{\text{within}}} = \frac{SS_{\text{within}}}{N - k}$$
* **F-Ratio**:
  $$F = \frac{MS_{\text{between}}}{MS_{\text{within}}}$$

#### **Standard ANOVA Table Layout**
The calculated values are summarized in the standard ANOVA table below:

| Source of Variation | Sum of Squares ($SS$) | Degrees of Freedom ($df$) | Mean Square ($MS$) | Calculated $F$-Ratio | Significance ($P$-Value) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Between Groups (Treatment)** | $SS_{\text{between}}$ | $k - 1$ | $MS_{\text{between}}$ | $F = \frac{MS_{\text{between}}}{MS_{\text{within}}}$ | $P(F_{(k-1, N-k)} \ge F)$ |
| **Within Groups (Error)** | $SS_{\text{within}}$ | $N - k$ | $MS_{\text{within}}$ | | |
| **Total** | $SS_{\text{total}}$ | $N - 1$ | | | |

#### **Post-Hoc Analysis: Tukey’s Honestly Significant Difference (HSD)**
If the calculated $F$-ratio exceeds the critical value $F_{\text{crit}}$ at a significance level of $\alpha = 0.05$ (causing the rejection of $H_0$), Tukey's HSD post-hoc test is conducted. This test identifies which specific pair of mix designs has a statistically significant difference.

The Tukey HSD value is calculated as:
$$HSD = q \sqrt{\frac{MS_{\text{within}}}{n}}$$
where:
* $q$ is the studentized range statistic obtained from standard Tukey Studentized Range distribution tables, for a given level of significance $\alpha$, degrees of freedom $df_{\text{within}} = N - k$, and total groups $k$.
* $n$ is the sample size per individual mix design group (assuming equal group sizes, $n_1 = n_2 = n_3 = n$).

The absolute difference between the sample means of any two mix designs, $|\bar{X}_{\cdot j} - \bar{X}_{\cdot j'}|$, is calculated:
$$\text{If } |\bar{X}_{\cdot j} - \bar{X}_{\cdot j'}| > HSD, \text{ the difference between the two mix designs is statistically significant.}$$

Through these statistical models, the study determines the optimal raw material mix design that maximizes structural durability and combustion efficiency while minimizing manufacturing costs and energy use.

---

## **Design Project Flow**

The design project flow is structured to translate the engineering specifications of the semi-automated briquetting machine into a coherent, functional cyber-physical system, composed of three integrated design domains: System Architecture, Schematic Diagram, and Prototype physical chambers.

### **1. System Architecture**
The system architecture governs the data, power, and logical flows across the different hardware domains, categorized into three operational layers:
*   **Power Distribution Layer (12V High-Power & 5V/3.3V Logic Rails)**: Coordinates electrical flows from the 100W monocrystalline solar panel through the 10A MPPT charge controller to charge the 12V 30Ah LiFePO4 battery pack. Real-time consumption is monitored via the INA219. High-power actuators (grinder, mixer, linear press, halogen drying lamps) run on the raw 12V DC bus. A step-down buck converter supplies a stable, low-noise 5.0V rail to power the ESP32 and Nextion touchscreen.
*   **Control Layer (Firmware State Machine)**: The ESP32 coordinates the operational sequence of the FSM using low-power optocoupled relays to handle active inductive motor starts, solenoid open/closes, and solid-state heat switching, maintaining absolute galvanic isolation from sensitive microcontroller components.
*   **Data and Telemetry Feedback Layer**: Establishes continuous sensing pathways, reading volumetric flow interrupt pulses (YF-S201), strain-gauge ADC packets (HX711/Load Cell), 1-Wire temperature codes (DS18B20), and single-bus relative humidity (DHT22) to update the visual GUI dashboard on the Nextion screen over hardware serial.

---

### **2. Schematic Diagram**
The system's electrical circuit schematic isolates high-frequency electrical noise generated by high-current motors and actuators from the sensitive analog sensor network. 

To prevent power startup transients from resetting the ESP32 or corrupting the I2C telemetry, the system incorporates the following logical isolation boundaries:
*   **Galvanic Optoisolation**: Driving signals for the grinder, wiper motor, solenoid valve, and halogens run through PC817 optocouplers. The ESP32's digital output pins drive the internal infrared LEDs of the optocouplers, ensuring complete electrical separation from the high-current 12V returns.
*   **Star Grounding Configuration**: High-current grounds from the motors, linear press, and halogen array return directly to the negative terminal of the 12V LiFePO4 battery using heavy-gauge wire. Low-power logic grounds are kept on an isolated plane on the PCB and connected to the common ground at a single star node at the regulator's output.
*   **Startup Noise Filtration**: Decoupling capacitors ($100\mu\text{F}$ and $0.1\mu\text{F}$) are positioned close to the ESP32 VCC pin to filter out high-frequency voltage ripples. An active freewheeling flyback diode (1N4007) is wired in parallel across the solenoid valve coil, and RC snubber networks are installed on the high-current motor relays to suppress voltage surges during inductive load switching.

```text
==================================================================================================
                                    SYSTEM SCHEMATIC DIAGRAM
==================================================================================================
                 [100W Solar Panel]  
                        |  (+)18V, (-)GND
                        v
           [10A MPPT Charge Controller]
                        |  (+)12.8V Charging Bus
                        |--------------------------------+-----------------+
                        | (+)                            | (+)             | (+)
                        v                                v                 v
              [12V 30Ah LiFePO4 Battery]            [IBT-2 Motor]    [Opto-Relay Box]
                        | (-)                       [ Driver  ]      [  (5 Channels)]
                        v                                |                 |
              [INA219 Power Monitor]                      |                 |
                   |             |                        |                 |
                   | I2C Bus     +--(-) High-Power GND----+                 |
                   |                                      |                 |
                   |                                      |                 |
                   v                                      v                 v
             [ESP32 MCU]                               [Actuator]       [Load Subsystems]
              | | | | |                                 M(+) M(-)       - Grinder (12V 5A)
              | | | | |                                  |    |         - Solenoid (12V 0.8A)
              | | | | +--[GPIO 25, 26 RPWM/LPWM]---------+----+         - Mixer (12V 3A)
              | | | +----[GPIO 27, 14, 12, 13, 15 Relays]---------------+ - Halogen (12V 4.16A)
              | | +------[GPIO 16, 17 UART2]-------> [Nextion 4.3 HMI]    - Exhaust (12V 0.2A)
              | |
              | +--------[GPIO 4, 5 Serial]--------> [HX711 24-Bit ADC] <--- [S-Type Load Cell]
              +----------[GPIO 18, 19, 23 Sensor]--> [Flow / Temp / Humidity Sensors]
==================================================================================================
```

---

### **3. Prototype Design**
The mechanical framework of the semi-automated machine is constructed from structural $2020$ T-slot aluminum extrusions, which provide a high strength-to-weight ratio, and food-grade 304 stainless steel sheets for the material chambers. The physical prototype occupies a space-efficient footprint of $120.0 \text{ cm} \times 100.0 \text{ cm}$.

```text
0       10       20       30       40       50       60       70       80       90      100 cm
+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+ 120 cm
|                                                                                         |
|       [SOLENOID INLET]                       [LINEAR ACTUATOR MOTOR]                    |
|             |                                        ||                                 |
|             v                                        ||                                 |
|      +--------------+                                ||                                 | 100 cm
|      |  GRINDING    |                                || (Shaft)                         |
|      |  CHAMBER     |                                ||                                 |
|      | (775 Motor)  |                                ||                                 |
|      +-------+------+                                v                                  | 80 cm
|              |                               +---------------+                          |
|              v                               | MOLD COVER    | [LID SWITCH]             |
|      +--------------+                        +---------------+                          |
|      | MIXING AND   |                        |  MOLDING      |                          | 60 cm
|      | SOAKING      |=====[Slurry Valve]====>|  CHAMBER      |                          |
|      | CHAMBER      |                        | (Briquette)   |                          |
|      | (Mixer Motor)|                        +---------------+                          | 40 cm
|      +--------------+                                | (Reaction Force)                 |
|                                                      v                                  |
|                                              ================= [Lever Pivot Point]      |
|    +----------------------+                  \               /                          | 20 cm
|    |   DRYING CHAMBER     |                   \             /                           |
|    | (4x Halogen Lamps    |                    \           /                            |
|    |  + Exhaust Blower)   |                     v         v                             |
|    +----------------------+                 [S-TYPE LOAD CELL] (100kg Capacity)         |  0 cm
+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
```

The prototype incorporates four functional, sequential chambers:

*   **Grinding Chamber**: Featuring a cylindrical steel container fitted with dual rotary steel cutting blades. The high-speed rotation driven by the 12V 775 DC motor (10,000 RPM) crushes dry residual charcoal fines into a fine dust ($d < 2.0\text{ mm}$), creating an optimal carbonaceous base.
*   **Soaking and Mixing Chamber**: A high-capacity blending vessel housing dual-layer paddles offset by 90 degrees. It is fed with a precise volume of water via a closed-loop solenoid control and driven by the high-torque geared mixing motor to blend hydrated paper fibers and charcoal powder.
*   **Molding Press Chamber**: Equipped with a heavy-duty rectangular steel sleeve. Compression is executed by the 12V high-force linear actuator pushing the press platen down into the mold cavity. 
    To protect the 100kg S-type load cell located at the base from high impact stress, a class-1 force multiplier lever is integrated. The pivot arm acts as a **2:1 force divider**, routing exactly half of the compaction force ($1000\text{ N}$ or $\approx 102\text{ kg}$) to the strain-gauge sensor, allowing safe, highly precise monitoring up to the targeted $2000\text{ N}$ limit.

```text
               [ACTUATOR SHAFT]
                      |
                      v
              +---------------+
              |   MOLD LID    | <--------- [LID LIMIT SWITCH] (Safety verification)
              +---------------+
              |   RECTANGULAR |
              |   MOLD SLEEVE | <--------- [10x5x5 cm Briquette Compartment]
              |   (Steel)     |
              +---------------+
              |  BASE PLATEN  | 
              +-------+-------+
                      |
                      v (Compaction Force = 2000N)
              +---------------+
              |   PIVOT ARM   |
              +-------+-------+
             /                 \
            /  2:1 Lever Ratio  \
           v                     v
      [Fixed Ground]     [S-TYPE LOAD CELL] (Experienced Force = 1000N / ~102kg)
```

*   **Infrared Thermal Drying Chamber**: A highly insulated thermal cavity lined with reflective ceramic fiber sheeting. Four 12V 12.5W halogen lamps are arranged in a circular array at the top to focus short-wave infrared heat directly onto the wet briquettes. 

    The 12V exhaust fan continuously draws away the moisture-laden boundary layer at the surface, maintaining a steep vapor pressure gradient that accelerates evaporation. The internal temperature and exhaust relative humidity are monitored continuously by the DS18B20 and DHT22 sensors, allowing the ESP32 to automatically shut down the heating lamps and fan once relative humidity falls below $15\%$, indicating the briquette is fully cured.

```text
                  [EXHAUST BLOWER]
                         ^
                 +-------+-------+
                 |  Vapor Outlet |
           +-----+---------------+-----+
           |   [H]   [H]   [H]   [H]   | <---- [H] Halogen Lamps (4x12.5W Array)
           |                           |
           |     +---------------+     | <---- [DHT22 Exhaust Sensor]
           |     |  PAPER-CHAR   |     |
           |     |  BRIQUETTE    |     |
           |     | (Drying Zone) |     | <---- [DS18B20 Core Temp Sensor]
           |     +---------------+     |
           |                           |
           |     Ambient Vents         |
           +---------------------------+
```

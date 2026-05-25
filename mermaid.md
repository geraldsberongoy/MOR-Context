---
config:
  layout: elk
---

```mermaid
graph TD
    %% Style Definitions
    classDef inputStyle fill:#ffe2bc,stroke:#ff9233,stroke-width:2px,color:#333333;
    classDef hardwareStyle fill:#e1f5fe,stroke:#03a9f4,stroke-width:2px,color:#333333;
    classDef firmwareStyle fill:#e8eaf6,stroke:#3f51b5,stroke-width:2px,color:#333333;
    classDef outputStyle fill:#d4edda,stroke:#28a745,stroke-width:2px,color:#333333;
    classDef pauseStyle fill:#ffebee,stroke:#ef5350,stroke-width:2px,stroke-dasharray: 4 4,color:#b71c1c;
    classDef layerStyle fill:#eceff1,stroke:#607d8b,stroke-width:2px,color:#37474f;

    %% =================================================================
    %% INPUT LAYER
    %% =================================================================
    subgraph INPUT_LAYER ["INPUT LAYER: MATERIAL & SUBSYSTEM COMPONENT ALLOCATION"]
        In_Charcoal["RAW MATERIAL INPUT:<br>• Loose Residual Charcoal Fines"]:::inputStyle
        In_Paper["RAW MATERIAL INPUT:<br>• Shredded Waste Paper Sheets"]:::inputStyle
        In_Fluid["FLUID INFUSION:<br>• Municipal Water Supply"]:::inputStyle
        In_Power["POWER REGULATION INFRASTRUCTURE:<br>• 100W Monocrystalline Solar Panel<br>• 10A MPPT Charge Controller<br>• 12V 30Ah LiFePO4 Battery Pack"]:::inputStyle
        In_Control["HMI SCREEN COMMANDS:<br>• Manual Stage Selections / Confirmation Flags"]:::inputStyle
    end

    %% =================================================================
    %% PROCESS LAYER
    %% =================================================================
    subgraph PROCESS_LAYER ["PROCESS LAYER: EVENT-DRIVEN HARDWARE & SOFTWARE STAGES"]
        
        P0_Boot["ESP32 Core Boot Initialization"]:::firmwareStyle
        SF_NVS["Firmware Memory: Preferences.h Allocation<br/>(Caches Active State to Prevent Mid-Batch Data Corruption)"]:::firmwareStyle
        SF_HTTP["Firmware Webserver: ESPAsyncWebServer.h + LittleFS<br/>(Serves UI Asset Bundles via Local Wi-Fi Access Point)"]:::firmwareStyle
        
        %% STAGE 1
        subgraph STAGE_1 ["STAGE 1: GRINDING"]
            P1_Grind["STATE_GRINDING<br/>Predefined Execution: 3.0 Minutes"]:::firmwareStyle
            HW_P1_Act["Hardware Actuator:<br>12V 775 High-Speed DC Motor<br>(Optocoupled Inductive Relay Switch)"]:::hardwareStyle
            HW_P1_Sens["Hardware Sensor:<br>I2C INA219 Telemetry Rail<br>(Real-Time Current & Motor Load Tracking)"]:::hardwareStyle
            
            P1_Grind -.-> HW_P1_Act
            P1_Grind -.-> HW_P1_Sens
        end

        Pause_1["⏸️ USER TRANSITION GATE 1:<br/>Awaits Manual Confirmation on Nextion Screen"]:::pauseStyle

        %% STAGE 2
        subgraph STAGE_2 ["STAGE 2: SOAKING"]
            P2_Soak["STATE_SOAKING<br/>Closed-Loop Volumetric Metering"]:::firmwareStyle
            HW_P2_Act["Hardware Actuator:<br>12V Solenoid Water Valve<br>(Normally Closed Relay Node)"]:::hardwareStyle
            HW_P2_Sens["Hardware Sensor:<br>YF-S201 Hall-Effect Flow Sensor<br>(Interrupt Pin Pulse Counting: 450 pulses/L)"]:::hardwareStyle
            
            P2_Soak -.-> HW_P2_Act
            P2_Soak -.-> HW_P2_Sens
        end

        Pause_2["⏸️ USER TRANSITION GATE 2:<br/>Awaits Manual Confirmation on Nextion Screen"]:::pauseStyle

        %% STAGE 3
        subgraph STAGE_3 ["STAGE 3: COMBINING"]
            P3_Combine["STATE_COMBINING<br/>Predefined Execution: 5.0 Minutes"]:::firmwareStyle
            HW_P3_Act["Hardware Actuator:<br>12V Planetary Geared Motor (60 RPM)<br>(Heavy-Duty Blending Relay Channels)"]:::hardwareStyle
            HW_P3_Chem["Material Chemistry Execution:<br>Fibrillar Hydration & Exposed Cellulose<br>Hydroxyl -OH Secondary Valence Bonding"]:::hardwareStyle
            
            P3_Combine -.-> HW_P3_Act
            P3_Combine -.-> HW_P3_Chem
        end

        Pause_3["⏸️ USER TRANSITION GATE 3:<br/>Manual Slurry Gate Drop & Latch Casing Lid"]:::pauseStyle

        %% STAGE 4
        subgraph STAGE_4 ["STAGE 4: MOLDING & COMPRESSION"]
            P4_Comp["STATE_COMPRESSION<br/>Target Dynamic Limits: 2000N Force"]:::firmwareStyle
            HW_P4_Act["Hardware Actuator:<br>BTS7960 IBT-2 High-Current H-Bridge<br>+ 12V High-Force 4000N Linear Actuator"]:::hardwareStyle
            HW_P4_Sens["Hardware Sensor:<br>S-Type 100kg Load Cell + HX711 ADC<br>(Calibrated Matrix Scalar: 22,480 LSB/kg)"]:::hardwareStyle
            HW_P4_Mech["Mechanical Advantage:<br>Class-1 2:1 Lever Pivot Divider Plate<br>(Protects Sensor by Halving Physical Load)"]:::hardwareStyle
            HW_P4_Safe["Hardware Interlock:<br>Lid & Base Safety Limit Micro-Switches<br>(Active-LOW Low-Latency Interrupt Lines)"]:::hardwareStyle
            
            P4_Comp -.-> HW_P4_Act
            P4_Comp -.-> HW_P4_Sens
            HW_P4_Sens -.-> HW_P4_Mech
            P4_Comp -.-> HW_P4_Safe
        end

        Pause_4["⏸️ USER TRANSITION GATE 4:<br/>Transfer Compressed Briquette to Drying Enclosure"]:::pauseStyle

        %% STAGE 5
        subgraph STAGE_5 ["STAGE 5: DRYING"]
            P5_Dry["STATE_DRYING<br/>Thermal Auto-Shutdown: 15% Exhaust RH"]:::firmwareStyle
            HW_P5_Act["Hardware Actuator:<br>4x 12.5W Halogen Thermal Array (50W)<br>+ 12V DC Brushless Exhaust Blower Fan"]:::hardwareStyle
            HW_P5_Sens["Hardware Sensors:<br>1-Wire DS18B20 Digital Temp Probe<br>+ Polynomial-Corrected DHT22 Sensor"]:::hardwareStyle
            
            P5_Dry -.-> HW_P5_Act
            P5_Dry -.-> HW_P5_Sens
        end

        %% STAGE 6
        subgraph STAGE_6 ["STAGE 6: COMPLETED"]
            P6_Comp["STATE_COMPLETED<br/>Batch System Reset & Memory Flush"]:::firmwareStyle
            HW_P6_Act["Hardware Alert:<br>5V Transistor Base Passive Buzzer Array<br>(Audible Cue Sequence: 3 Long Beeps)"]:::hardwareStyle
            HW_P6_Mem["Firmware Memory Clear:<br>Wipes Transient Session Caches from Flash<br>(Returns Bootloader to STATE_IDLE)"]:::firmwareStyle
            
            P6_Comp -.-> HW_P6_Act
            P6_Comp -.-> HW_P6_Mem
        end
        
        E_Stop["EMERGENCY STOP SHUTDOWN INTERRUPT ROUTINE"]:::layerStyle
    end

    %% =================================================================
    %% OUTPUT LAYER
    %% =================================================================
    subgraph OUTPUT_LAYER ["OUTPUT LAYER: COMPLETED FUELS & RESEARCH SYSTEM ASSETS"]
        Out_Fuel["SOLID FUEL CHUNK PRODUCT:<br>• Uniform Rectangular Prism Briquettes<br>• Standardized 10 x 5 x 5 cm Footprint<br>• High-Density Extended-Burn Cook Alternative"]:::outputStyle
        Out_Matrix["STRUCTURAL COMPOSITION:<br>• 100% Chemical-Free Durable Matrix<br>• Cohesive Cross-Linked Hydrogen Networks"]:::outputStyle
        Out_HMI["LOCAL DATA AGGREGATION:<br>• Real-Time 10Hz Parameter Gauge Clusters<br>• Nextion Discovery 4.3 Capacitive Screen HUD"]:::outputStyle
        Out_PWA["REMOTE TELEMETRY STREAM:<br>• Local Hotspot Broadcast http://192.168.4.1<br>• Offline React/Tailwind Dashboard WebSockets"]:::outputStyle
        Out_Logs["EXPORTABLE RESEARCH ASSETS:<br>• FAT32 External Mass Storage SPI SD Card Files<br>• Downloadable Run Spreadsheets in CSV Format"]:::outputStyle
    end

    %% --- FLOW ROUTING PATHS ---
    %% Input to Process Stage Cross-Wiring (Reflecting your optimized routing)
    In_Power -->|"12V Regulated DC Bus"| P0_Boot
    In_Control -->|"UART Serial2 Data Link"| P0_Boot
    
    In_Charcoal == "Fed Directly into Grinding Mechanism" ==> P1_Grind
    In_Paper == "Fed Directly into Hydro-Activation Chamber" ==> P2_Soak
    In_Fluid -->|"Channeled to Solenoid Inlet"| HW_P2_Act
 
    %% Core Process Step Interlocking with Mandatory Semi-Automated User Gates
    P0_Boot --> SF_NVS
    SF_NVS --> SF_HTTP
    SF_HTTP --> P1_Grind
    
    P1_Grind -->|"3-Minute Grinding Complete"| Pause_1
    Pause_1 -->|"Operator Selects 'Next' on GUI"| P2_Soak
    P2_Soak -->|"1:1 Water-to-Paper Ratio Reached"| Pause_2
    
    %% Material Convergence Step
    P1_Grind -.->|"Powdered Charcoal Aggregate Output"| P3_Combine
    Pause_2 -->|"Operator Loads Powder & Selects 'Combine'"| P3_Combine
    
    P3_Combine -->|"5-Minute Blending Complete"| Pause_3
    Pause_3 -->|"Operator Loads Mold & Latches Casing"| P4_Comp
    P4_Comp -->|"Target Compressive Load Logged"| Pause_4
    Pause_4 -->|"Operator Positions Block & Selects 'Dry'"| P5_Dry
    P5_Dry -->|"Exhaust Drops Below 15% RH"| P6_Comp
 
    %% Hardware Safety Interrupt Loop
    HW_P4_Safe -->|"Lid Unlatched / Circuit Micro-Switch Break"| E_Stop
    E_Stop -->|"Relays Drop to Open-Circuit / Actuator Duty Cycle to 0%"| Out_HMI
 
    %% Process to Output Asset Generations
    P6_Comp --> Out_Fuel
    HW_P3_Chem -.-> Out_Matrix
    P5_Dry -.-> Out_HMI
    SF_HTTP -.-> Out_PWA
    P6_Comp -.-> Out_Logs
```
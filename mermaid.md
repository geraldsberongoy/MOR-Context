---
config:
  layout: elk
---
graph TD
    %% Style Definitions
    classDef startEndStyle fill:#ffffff,stroke:#333333,stroke-width:2px,color:#333333;
    classDef processStyle fill:#ffffff,stroke:#333333,stroke-width:2px,color:#333333;
    classDef ioStyle fill:#ffffff,stroke:#333333,stroke-width:2px,color:#333333;
    classDef decisionStyle fill:#ffffff,stroke:#333333,stroke-width:2px,color:#333333;

    F_Start([START]):::startEndStyle
    
    P_Init[Initialize Sensors & Relays<br/>• Mount LittleFS & External SD Card]:::processStyle
    
    %% PARALLEL PREPARATION SHOWN SEQUENTIALLY IN SINGLE-CORE FSM
    P_Grind[Execute STATE_GRINDING<br/>• Actuate 12V 775 DC Grinder Motor<br/>• Pulverize Charcoal Fines to Powder]:::processStyle
    
    D_Grind{Has Predefined<br/>3-Minute Grinding<br/>Timer Ended?}:::decisionStyle
    
    D_Gate1{Did Operator<br/>Confirm Transition to<br/>Soaking Stage?}:::decisionStyle
    
    P_Soak[Execute STATE_SOAKING<br/>• Open 12V Water Solenoid Valve<br/>• Activate Paper Cellulose Fibers in Water]:::processStyle
    
    D_Soak{Is Target Volumetric<br/>Water Mass (1:1 Ratio)<br/>Achieved?}:::decisionStyle
    
    D_Gate2{Did Operator<br/>Confirm Transition to<br/>Combining Stage?}:::decisionStyle
    
    P_Combine[Execute STATE_COMBINING<br/>• Run 12V Planetary Geared Motor<br/>• Blend Activated Paper with Charcoal Powder]:::processStyle

    %% Connections
    F_Start --> P_Init
    P_Init --> P_Grind
    P_Grind --> D_Grind
    D_Grind -->|No| P_Grind
    D_Grind -->|Yes| D_Gate1
    D_Gate1 -->|No| D_Gate1
    D_Gate1 -->|Yes| P_Soak
    P_Soak --> D_Soak
    D_Soak -->|No| P_Soak
    D_Soak -->|Yes| D_Gate2
    D_Gate2 -->|No| D_Gate2
    D_Gate2 -->|Yes| P_Combine
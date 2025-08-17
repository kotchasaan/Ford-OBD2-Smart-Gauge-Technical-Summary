# Ford Motor Company PID Reference (2009 PCED Gasoline Engines)

## 📋 Document Information
- **Source:** 2009 PCED Gasoline Engines - Section 2: Diagnostic Methods  
- **Revision Date:** February 20, 2010  
- **Application:** Ford Ranger PJ/PK and compatible vehicles

---

## 🔍 Parameter Identification (PID) Overview

The PID mode allows access to powertrain control module (PCM) information, including:
- Analog and digital signal inputs/outputs
- Calculated values and system status
- Real-time and freeze frame data

### PID Types Available:
1. **Generic OBD-II (J1979)** - Standard PIDs accessible by all scan tools
2. **Ford-Specific (J2190)** - Requires appropriate Ford-compatible scan tool

For detailed specifications, refer to SAE document J2205.

---

## 🚗 Generic OBD-II PID List

> **Note:** An "X" in the Freeze Frame column indicates both Mode 1 (real-time) and Mode 2 (freeze frame) PID availability.

| Freeze Frame | Acronym | Description | Units |
|:---:|---------|-------------|-------|
| X | **AAT** | Ambient Air Temperature | °C/°F |
| X | **AIR** | Secondary Air Status | On/Off |
| X | **APP_D** | Accelerator Pedal Position D | % |
| X | **APP_E** | Accelerator Pedal Position E | % |
| X | **APP_F** | Accelerator Pedal Position F | % |
| X | **CATEMP11** | Catalyst Temperature Bank 1, Sensor 1 | °C/°F |
| X | **CATEMP12** | Catalyst Temperature Bank 1, Sensor 2 | °C/°F |
| X | **CATEMP21** | Catalyst Temperature Bank 2, Sensor 1 | °C/°F |
| X | **CATEMP22** | Catalyst Temperature Bank 2, Sensor 2 | °C/°F |
| | **CLR_DST** | Distance Since Codes Cleared | km |
| | **CCNT** | Continuous DTC Counter | Count |
| X | **ECT** | Engine Coolant Temperature | °C/°F |
| X | **EGR_PCT** | Commanded EGR | % |
| X | **EGR_ERR** | EGR Error | % |
| X | **EVAP_PCT** | Commanded Evaporative Purge | % |
| X | **EVAP_VP** | Evaporative System Vapor Pressure | Pa |
| X | **EQ_RAT** | Commanded Equivalence Ratio | Ratio |
| X | **FUEL_SYS1** | Fuel System Status Bank 1 | OL/CL/OL_DRIVE/OL_FAULT/CL_FAULT |
| X | **FUEL_SYS2** | Fuel System Status Bank 2 | OL/CL/OL_DRIVE/OL_FAULT/CL_FAULT |
| | **IAT** | Intake Air Temperature | °C/°F |
| X | **LOAD** | Calculated Engine Load¹ | % |
| X | **LOAD_ABS** | Absolute Load Value | % |
| X | **LONGFT1** | Long Term Fuel Trim Bank 1 | % |
| X | **LONGFT2** | Long Term Fuel Trim Bank 2 | % |
| X | **MAF** | Mass Air Flow Rate | g/s, lb/min |
| | **MIL_DIST** | Distance Traveled with MIL On | km |
| X | **O2S11** | Bank 1 Upstream Oxygen Sensor | V |
| X | **O2S12** | Bank 1 Downstream Oxygen Sensor | V |
| X | **O2S13** | Bank 1 Downstream Oxygen Sensor | V |
| X | **O2S21** | Bank 2 Upstream Oxygen Sensor | V |
| X | **O2S22** | Bank 2 Downstream Oxygen Sensor | V |
| X | **O2S23** | Bank 2 Downstream Oxygen Sensor | V |
| | **OBDSUP** | On Board Diagnostic System | OBD-II/OBD-I/OBD/Combination/None |
| X | **PTO** | Power Take-Off Status | On/Off |
| X | **RPM** | Engine Speed | RPM |
| X | **RUNTM** | Engine Run Time | Seconds |
| X | **SHRTFT1** | Short Term Fuel Trim Bank 1 | % |
| X | **SHRTFT2** | Short Term Fuel Trim Bank 2 | % |
| X | **SPARKADV** | Spark Advance Requested | ° BTDC |
| X | **SPARK_ACT** | Spark Advance Actual | ° BTDC |
| X | **TAC_PCT** | Commanded Throttle Actuator | % |
| X | **TP** | Throttle Position | % |
| X | **TP_R** | Relative Throttle Position | % |
| | **WARM_UPS** | Warm-ups Since Codes Cleared | Count |
| X | **VSS** | Vehicle Speed | km/h, mph |

### Fuel System Status Definitions:
- **OL** = Open loop (conditions for closed loop not satisfied)
- **CL** = Closed loop using HO2S feedback for fuel control
- **OL_DRIVE** = Open loop due to driving conditions (heavy acceleration)
- **OL_FAULT** = Open loop due to fault with all upstream HO2S sensors
- **CL_FAULT** = Closed loop with fault in one upstream HO2S sensor (dual bank)

¹ *Engine load adjusted for atmospheric pressure*

---

## 🔧 Ford-Specific PID List

> **Note:** This is not a complete list of all available Ford PIDs. These are PIDs documented in the original manual.

### Engine Control & Monitoring

| Acronym | Description | Units |
|---------|-------------|-------|
| **ACCS** | Air Conditioning Cycling Switch Input | On/Off |
| **ACP** | A/C Pressure Transducer Sensor | V/Pressure |
| **ACP_PRESS** | A/C Pressure Transducer Sensor | V/Pressure |
| **AIR** | Secondary AIR Pump Control | On/Off |
| **AIRF** | Secondary AIR Fault Indicator | Yes/No |
| **AIRM** | Secondary AIR Pump Monitor | On/Off |
| **APP** | Accelerator Pedal Position | % |
| **APP1** | Accelerator Pedal Position 1 | V |
| **APP2** | Accelerator Pedal Position 2 | V |
| **APP3** | Accelerator Pedal Position 3 | V |
| **APP_MAXDIFF** | Maximum Difference between APP1 and APP2 | ° |
| **APP_MODE** | Accelerator Pedal Position Mode | Position |
| **AXLE** | Axle Ratio | Ratio |
| **B+** | Battery Voltage | V |
| **BARO** | Barometric Pressure Sensor | Frequency/Pressure |
| **BOO** | Brake Pedal Position Switch | On/Off |
| **BOO1** | Brake Pedal Position Switch | On/Off |
| **BOO2** | Brake Pedal Switch | On/Off |
| **BPA** | Brake Pressure Applied | On/Off |
| **BPP/BOO** | Brake Pedal Position Switch | On/Off |

### Engine Temperature & Sensors

| Acronym | Description | Units |
|---------|-------------|-------|
| **CHT** | Cylinder Head Temperature Input | V/°F |
| **ECT** | Engine Coolant Temperature Input | V/°F |
| **EOT** | Engine Oil Temperature Sensor Input | V/°F |
| **EOT_F** | Engine Oil Temperature Sensor Fault | Fault/No Fault |
| **IAT** | Intake Air Temperature Input | °F/V |
| **IAT2** | Intake Air Temperature Sensor 2 Input | °F/V |

### Fuel System

| Acronym | Description | Units |
|---------|-------------|-------|
| **FLI** | Fuel Level Indicator Input | % |
| **FP** | Fuel Pump Duty Cycle | % |
| **FPM** | Fuel Pump Secondary Monitor | %/On/Off |
| **FRP** | Fuel Rail Pressure Input | V/Pressure |
| **FRP_DSD** | Fuel Rail Pressure Desired | Pressure |
| **FRT** | Fuel Rail Temperature | °F/V |
| **FTP** | Fuel Tank Pressure Input | V/Pressure |
| **FTP_H2O** | Fuel Tank Pressure Input | Pressure |
| **FUELPW1** | Injector Pulse Width Bank 1 | ms |
| **FUELPW2** | Injector Pulse Width Bank 2 | ms |
| **FUELSYS** | Fuel System Status | Open/Closed Loop |

### Ignition & Timing

| Acronym | Description | Units |
|---------|-------------|-------|
| **IGN_R/S** | Ignition Switch Run/Start | On/Off |
| **RPM** | Engine Speed Based Upon CKP Input | RPM |
| **RPMDSD** | RPM Desired | RPM |
| **SPARKADV** | Spark Advance Desired | ° BTDC |
| **SPKDUR_1-8** | Spark Duration (Cylinders 1-8) | ms |
| **SYNC** | CMP and CKP Synchronized | Yes/No |

### Oxygen Sensors & Exhaust

| Acronym | Description | Units |
|---------|-------------|-------|
| **HO2S11** | Bank 1 Sensor 1 HO2S Input | V |
| **HO2S12** | Bank 1 Sensor 2 HO2S Input | V |
| **HO2S13** | Bank 1 Sensor 3 HO2S Input | V |
| **HO2S21** | Bank 2 Sensor 1 HO2S Input | V |
| **HO2S22** | Bank 2 Sensor 2 HO2S Input | V |
| **HTR11** | Bank 1 Sensor 1 HO2S Heater Control | On/Off |
| **HTR11F** | Bank 1 Sensor 1 HO2S Heater Circuit Fault | Yes/No |
| **HTR12** | Bank 1 Sensor 2 HO2S Heater Control | On/Off |
| **HTR12F** | Bank 1 Sensor 2 HO2S Heater Circuit Fault | Yes/No |
| **HTR13** | Bank 1 Sensor 3 HO2S Heater Control | On/Off |
| **HTR21** | Bank 2 Sensor 1 HO2S Heater Control | On/Off |
| **HTR21F** | Bank 2 Sensor 1 HO2S Heater Circuit Fault | Yes/No |
| **HTR22** | Bank 2 Sensor 2 HO2S Heater Control | On/Off |
| **HTR22F** | Bank 2 Sensor 2 HO2S Heater Circuit Fault | Yes/No |

### Transmission Control

| Acronym | Description | Units |
|---------|-------------|-------|
| **CCS** | Coast Clutch Solenoid Control | On/Off |
| **EPC** | Electronic Pressure Control | kPa/PSI |
| **EPC_V** | Electronic Pressure Control | V |
| **GEAR** | Transmission Gear Status | Gear |
| **ISS_SRC** | Intermediate Shaft Speed | Frequency/RPM |
| **OSS** | Output Shaft Speed | RPM |
| **OSS_SRC** | Output Shaft Speed | RPM |
| **REV_SW** | Transmission Reverse Switch Input | On/Off |
| **SSA/SS1** | Shift Solenoid 1 Control | On/Off |
| **SSB/SS2** | Shift Solenoid 2 Control | On/Off |
| **SSC/SS3** | Shift Solenoid 3 Control | On/Off |
| **SSD/SS4** | Shift Solenoid 4 Control | On/Off |
| **TCC** | Torque Converter Clutch Control | % |
| **TCIL** | Transmission Control Indicator Lamp | On/Off |
| **TCS** | Transmission Control Switch | Depressed/Not Depressed |
| **TFT** | Transmission Fluid Temperature Input | V/°F |
| **TFTV** | Transmission Fluid Temperature Input | V |
| **TORQUE** | Net Torque Into Torque Converter | Nm |
| **TR** | Transmission Selector Position Input Status | Position |
| **TR1** | Transmission Range Sensor 1 | Open/Closed |
| **TR2** | Transmission Range Sensor 2 | Open/Closed |
| **TR3** | Transmission Range Sensor 3 | Open/Closed |
| **TR4** | Transmission Range Sensor 4 | Open/Closed |
| **TR_V** | Transmission Selector Position Input Status | V |
| **TR_D** | Transmission Selector Position Input Status (Digital) | Binary |
| **TSS** | Turbine Shaft Speed | RPM |
| **TSS_SRC** | Unfiltered Turbine Shaft Speed | RPM |

### Throttle Control

| Acronym | Description | Units |
|---------|-------------|-------|
| **ETC_ACT** | Electronic Throttle Control Actual | ° |
| **ETC_DSD** | Electronic Throttle Control Desired | ° |
| **ETC_TRIM** | Electronic Throttle Control Trim | ° |
| **IAC** | Idle Air Control | % |
| **TP** | Throttle Position Input | V |
| **TPCT** | Lowest Closed Throttle Voltage | V |
| **TP_MAXDIFF** | Maximum Angle Difference between TP1 and TP2 | ° |
| **TP1** | Throttle Position 1 Voltage | V |
| **TP2** | Throttle Position 2 Voltage | V |

### Variable Cam Timing

| Acronym | Description | Units |
|---------|-------------|-------|
| **VCTADV** | Variable Cam Timing Advance | ° |
| **VCTADV2** | Variable Cam Timing Advance 2 | ° |
| **VCTADVERR** | Variable Cam Timing Advance Error | ° |
| **VCTADVERR2** | Variable Cam Timing Advance 2 Error | ° |
| **VCTDC** | Variable Cam Timing Advance Duty Cycle | % |
| **VCTDC2** | Variable Cam Timing Advance Duty Cycle | % |
| **VCTSYS** | Variable Cam Timing System Status | Open/Closed |

### System Monitoring

| Acronym | Description | Units |
|---------|-------------|-------|
| **CAT_EVAL** | Catalyst Evaluated | Yes/No |
| **CLRDIST** | Distance Since DTCs Cleared | Miles |
| **CLRWRMUP** | Number of Warm-ups Since DTCs Cleared | Count |
| **DRIVECNT** | Number of Successful Ignition Cycles and Engine Starts | Count |
| **DTCCNT** | Total Number of Fault Codes | Count |
| **LOAD** | Calculated Engine Load | % |
| **LONGFT1** | Long Term Fuel Trim Bank 1 | % |
| **LONGFT2** | Long Term Fuel Trim Bank 2 | % |
| **MIL** | Malfunction Indicator Lamp Control | On/Off |
| **MIL_DIS** | Distance Since MIL was Activated | Miles |
| **MISFIRE** | Misfire Status | Yes/No |
| **NM** | Number of Misfires | Count |
| **SHRTFT** | Short Term Fuel Trim | % |
| **SHRTFT1** | Short Term Fuel Trim Bank 1 | % |
| **SHRTFT2** | Short Term Fuel Trim Bank 2 | % |
| **TRIP_CNT** | OBD-II Trips Completed | Count |

---

## 🛠️ Implementation Notes for Ford OBD2 Smart Gauge

### Currently Implemented PIDs:
- **010C** (RPM) - Engine Speed
- **0105** (ECT) - Engine Coolant Temperature  
- **010D** (VSS) - Vehicle Speed
- **0104** (LOAD) - Engine Load
- **010F** (IAT) - Intake Air Temperature
- **010B** (MAP) - Manifold Absolute Pressure
- **0133** (BARO) - Barometric Pressure
- **221674** (TFT) - Transmission Fluid Temperature (Ford-specific)

### Potential PIDs for Future Implementation:
- **221127** - Ford Barometric Pressure
- **22114A** - Ford IAT Voltage (for validation)
- **22114D** - Ford ECT Voltage (for validation)
- **221E1C** - Alternative ATF Temperature
- **2211BD** - Secondary ATF Temperature

### Notes:
- PIDs marked with "X" in Freeze Frame are available in both real-time and freeze frame modes
- Ford-specific PIDs may require specific initialization sequences
- Not all PIDs may be available on all vehicle configurations
- Some PIDs may be model year or engine specific

---

## 📚 References
- Ford Motor Company Service Manual (2009 PCED Gasoline Engines)
- SAE J1979 (OBD-II Standard)
- SAE J2190 (Enhanced Diagnostic Communication)
- SAE J2205 (Data Parameter Definitions)

---

*This document serves as a reference for implementing OBD-II communication with 2009 Ford vehicles, specifically the Ford Ranger PJ/PK platform.*